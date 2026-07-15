## Behavior 5——创建并重启早期 active treatment

### 接口表面能够看到的内容

两次操作使用完全相同的接口：

```http
POST /api/behandlinger
```

从 OpenAPI 或普通黑盒调用只能知道“创建 treatment”。无法根据路由区分：

- 新建 treatment；
- 返回已有 treatment；
- 重置已有 treatment；
- 因已有 treatment 所处步骤过晚而拒绝请求。

### 白盒发现的真实业务状态机

源码中的实际分支是：

```text
没有 active treatment 或 active treatment 已结束
    → 创建新 treatment
    → 初始化步骤状态
    → 创建 active decision
    → 可能创建 task
    → 发布统计事件

已有 active treatment，且 step < BESLUTTE_VEDTAK
    → 不创建新 treatment
    → 复用原 treatment ID
    → 增加/恢复 FØRSTE_STEG 状态
    → status 重置为初始状态

已有 active treatment，且 step >= BESLUTTE_VEDTAK
    → 拒绝创建
```

关键源码见 [`BehandlingService.kt`](/Users/yangyuhan/behavior-analyze/familie-ba-sak/src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/BehandlingService.kt:66)。

### 白盒优势为什么是决定性的

黑盒即使发现两次请求返回相同 `behandlingId`，也不能可靠判断：

- 是简单幂等返回，还是执行了状态回退；
- 精确分界是不是 `BESLUTTE_VEDTAK`；
- 原有步骤状态是否被重置；
- 是否重新创建 decision、task 或统计事件；
- decision 阶段以后为什么失败。

因此，白盒不仅发现了一个隐藏分支，还改变了 Behavior 的业务定义：它不是单纯的“创建 treatment”，而是“创建或根据生命周期重启 treatment”。

### 应验证的白盒断言

- 第二次调用返回原 `behandlingId`。
- 数据库中仍然只有一个 active treatment。
- treatment 被重置到 `FØRSTE_STEG`。
- treatment 状态恢复为初始状态。
- 未创建第二个 active treatment。
- `step == BESLUTTE_VEDTAK` 和 `step > BESLUTTE_VEDTAK` 均被拒绝。
- 新建分支产生的 decision、task、统计事件没有在重启分支中被错误复制。

对应 Behavior 描述见 [`business-behavior.md`](/Users/yangyuhan/behavior-analyze/familie-ba-sak/business-behavior.md:336)。

---

## Behavior 11——添加儿童并重置后续处理步骤

### 接口表面能够看到的内容

```http
POST /api/behandlinger/{behandlingId}/legg-til-barn
```

请求体只有：

```json
{
  "barnIdent": "C1"
}
```

从接口名称和请求结构看，它很容易被分析成：

> 在 treatment 的 person basis 中新增一个儿童。

### 白盒发现的真实行为

Controller 在添加儿童后，还会调用：

```kotlin
tilbakestillService.initierOgSettBehandlingTilVilkårsvurdering(behandling)
```

因此完整状态变化是：

```text
解析儿童身份
    → 更新 active person basis
    → 写入添加儿童日志
    → 初始化新的后续处理状态
    → treatment 回退到 VILKÅRSVURDERING
    → 原有结果和 decision 派生状态需要重新计算
```

关键调用见 [`GrunnlagController.kt`](/Users/yangyuhan/behavior-analyze/familie-ba-sak/src/main/kotlin/no/nav/familie/ba/sak/kjerne/grunnlag/søknad/GrunnlagController.kt:47)。

### 白盒优势为什么是决定性的

“重置后续步骤”不是添加儿童接口名称、参数或 HTTP 响应能够完整表达的内容。如果只做黑盒接口清单分析，很可能生成：

> Behavior：向 treatment 添加儿童。

正确的白盒分析则是：

> Behavior：向 treatment 添加儿童，并回退到条件评估阶段，使依赖旧 person basis 的结果重新计算。

两者对测试设计的影响很大。前者只会检查儿童是否存在，后者必须检查整个 treatment 状态机。

### 应验证的白盒断言

- 新儿童出现在 active person basis 中。
- 旧 active person basis 被正确替换或失效，而不是产生两个 active basis。
- treatment 当前步骤变为 `VILKÅRSVURDERING`。
- 原有 result、decision period 或支付计算不再被当作有效终态。
- 添加儿童日志只产生一次。
- 重复添加同一儿童命中 `duplicate-child guard`，且不发生部分重置。
- 儿童刷新后仍不存在时，命中 `post-refresh guard`。

对应分析见 [`business-behavior.md`](/Users/yangyuhan/behavior-analyze/familie-ba-sak/business-behavior.md:725)。

---

## Behavior 1 及补充测试 T1——创建并幂等复用 case

黑盒可以通过连续调用两次：

```http
POST /api/fagsaker
```

并比较返回的 `fagsakId`，发现接口具有一定幂等性。因此“相同业务键返回同一 case”并不是完全无法通过黑盒发现。

但是黑盒通常只能证明：

```text
response.data.id 相同
```

它不能证明：

```text
数据库只有一条 Fagsak
shadow-case 只创建一次
统计事件只发布一次
没有第二个 treatment 或其他子资源
第二次调用确实走 existing-case 分支
```

所以 Behavior 的表面语义属于 **A 档**，而完整的 T1 测试规格属于 **S 档白盒测试**。

### T1 中真正体现白盒优势的部分

测试不能停留在：

```text
第一次返回 fagsakId=100
第二次仍返回 fagsakId=100
```

还必须验证：

- repository 中相同 actor、type、institution key 的记录数为 1；
- shadow-case create 调用次数为 1；
- case-create/statistics event 数量为 1；
- 第二次请求没有产生重复外部副作用；
- JaCoCo 同时覆盖 `eksisterendeFagsak == null` 和已有 case 返回分支。

这些要求见 [`business-behavior-coverage-report.md`](/Users/yangyuhan/behavior-analyze/familie-ba-sak/business-behavior-coverage-report.md:3410)，内部断言见 [`business-behavior-coverage-report.md`](/Users/yangyuhan/behavior-analyze/familie-ba-sak/business-behavior-coverage-report.md:3447)。

### 黑盒测试可能出现的假阳性

即使两次响应返回相同 ID，以下错误仍可能被黑盒测试遗漏：

- 第二次请求重复发送 shadow-case 消息；
- 重复发布统计事件；
- 插入重复数据库记录后又返回第一条记录；
- 产生孤立的关联数据；
- 外部系统收到两次创建通知。

因此，T1 的核心白盒价值不是“比较 ID”，而是证明：

> 幂等性覆盖整个业务事务及其副作用，而不仅是 HTTP 响应。