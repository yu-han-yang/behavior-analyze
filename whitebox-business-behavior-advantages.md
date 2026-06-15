# 白盒分析对 business-behavior 提取的优势说明

## 1. 背景与输入

本文说明白盒分析在生成 `business-behavior.md` 这类业务行为文档时的优势，重点回答：哪些 business behavior 或 behavior 细节在没有代码的情况下不可见、不可证明，或容易被 OpenAPI/黑盒观察误判。

本次分析相关输入包括：

- 生成结果：`/business-behavior.md`
- 函数级分析：`/full-behavior.md`
- OpenAPI/Swagger：`/familie-ba-sak.json`
- 生成提示词：`/Users/yangyuhan/.codex/attachments/77989814-b699-4262-a7cc-a4f77a8bda0c/pasted-text.txt`
- 实现源码、测试和种子数据：`/src`、`/tests`

这里的“白盒”指同时读取 API 合约、函数级分析、控制器、Service、Repository、Task、测试和数据模型；“黑盒”指主要依赖 OpenAPI、接口路径、请求/响应示例、运行时调用结果或外部观察。

## 2. 核心结论

`business-behavior.md` 最重要的价值不是把 endpoint 翻译成业务名称，而是把实现中的真实状态机、跨接口依赖、隐藏副作用、失败语义和缺失能力抽取为 domain-level behavior。本仓库的业务行为高度依赖实现逻辑：同一个 endpoint 可能创建、复用、重置、排队、异步触发任务，甚至以 `GET` 执行写操作；同一个 path 参数也不一定被 service 层用于强归属校验。

没有代码时，能看到的大多是“某个路径存在、参数叫什么、返回类型是什么”。有代码时，才能判断：

- 行为到底是同步完成还是仅创建异步任务。
- endpoint 是真实 CRUD、幂等复用、状态机推进，还是维护/修复入口。
- 业务状态是否被重置、级联修改、自动派生或延迟处理。
- 哪些 ID 必须来自前一步返回值，哪些 ID 只是 path 装饰但没有强校验。
- 错误是 HTTP error、`Ressurs.failure`、部分成功，还是 no-op。
- OpenAPI 描述与实现是否不一致。
- 预期业务能力是否真的可以由现有函数组合出来。

因此，白盒分析对于 business-behavior 的优势主要体现在“可解释真实业务状态变化”和“可证明不可支持行为”两个方面。

## 3. 白盒相对黑盒的优势

| 分析维度 | 黑盒/OpenAPI 通常可见 | 白盒源码分析可补齐 |
|---|---|---|
| Endpoint 能力 | HTTP method、path、参数、响应 schema | 真实业务含义、是否写状态、是否异步、是否只返回任务触发结果 |
| Workflow 组合 | endpoint 之间表面上可串联 | 哪些前置状态必须存在、哪些 ID 必须复用、哪些步骤会重置后续状态 |
| 状态机 | 可能看到字段名如 status/steg | 哪些状态允许/禁止迁移，等待、锁定、关闭、决策阶段如何影响后续行为 |
| 业务约束 | 参数必填、enum、简单校验 | Service 层业务校验、feature toggle、角色校验、外部系统发送后禁止变更 |
| 副作用 | 通常不可见 | 创建 task、发布统计、更新外部任务期限、发送 Infotrygd feed、重算支付结果 |
| 失败语义 | 状态码和 schema | HTTP 200 携带 failure resource、no-op、部分成功、catch-and-continue |
| 归属与权限 | path 上看起来有 parent id | 实际是否校验 child id 属于 parent，是否只按 child id load/delete |
| API/实现差异 | 只能信 OpenAPI | 能发现 mutating `GET`、Swagger 暴露但项目内无 controller、body/header 不一致 |
| 缺失能力 | 只能看到“没有 endpoint” | 能证明现有函数组合仍无法实现某个业务目标 |

## 4. 没有代码不可见或不可可靠提取的 behavior

下面列出 `business-behavior.md` 中典型的“必须读代码才能提取或确认”的 behavior。这里的“不可见”不是说 OpenAPI 中完全没有 endpoint，而是说仅凭 endpoint 不能可靠得出真实业务行为。

| Behavior | 源码揭示的行为 | 没有代码时为什么不可见或易误判 |
|---|---|---|
| Behavior 1: Establish a case and reuse the case identity | `POST /api/fagsaker` 不只是创建 case；相同 `personIdent + fagsakType + institution` 会返回既有 case，并可能创建/更新身份、统计、shadow-case 等周边状态。 | OpenAPI 只能显示创建接口，难以证明幂等业务键、HTTP 201 返回既有资源、机构 case 必须有 org number、访问校验和周边副作用。 |
| Behavior 4: Create and check manual repayment treatment | 手动追回处理创建是一个 mutating `GET`，检查接口只是读取 open repayment state。 | 黑盒容易把 `GET` 当成安全读取，无法从 HTTP method 判断它会创建状态。 |
| Behavior 5: Create a treatment and restart an early active treatment | 第二次 `POST /api/behandlinger` 在早期 active treatment 存在时不是创建新 treatment，而是复用并重置；进入 `BESLUTTE_VEDTAK` 后则阻止。 | OpenAPI 只能显示“创建 behandling”，看不出 active-treatment 互斥、早期重置、决策阶段锁定、active decision 初始化、task/statistics/Infotrygd side effect。 |
| Behavior 6/56/57/59/60/62/63/65/66: Queue event/rate/reconciliation jobs | 多个行为只是创建异步 task，并不代表业务处理已完成；有些返回值也不含可追踪 task id。 | 黑盒调用看到 success 容易误判为同步完成；没有 task executor 和 task payload 代码，看不到真实处理边界、重试和关联缺口。 |
| Behavior 8: Execute the caseworker treatment step flow through decision | 核心处理流是严格状态机：登记申请、校验条件、派生结果、追回评估、送 beslutter、决策；等待、关闭、决策阶段和角色会阻止不同步骤；决策后还有内部 task/step。 | OpenAPI 能看到多个 step endpoint，但看不出合法顺序、角色切换、自动决策、等待状态阻塞，以及“API-visible decision 不等于完全关闭 treatment”。 |
| Behavior 9: Dismiss an active treatment | henlegg/dismiss 受外部发送状态、dismissal type、feature toggle、技术维护原因等控制，并会执行 finish-treatment 逻辑。 | 仅看 endpoint 不知道哪些原因被 feature-gated，也不知道已经发送到外部服务后不能 dismiss。 |
| Behavior 10: Register institution and guardian information | 请求转换后必须得到 institution 或 guardian；否则返回 failure resource，可能不是普通 HTTP error。 | 黑盒若只看状态码会遗漏业务失败；OpenAPI schema 不足以说明转换结果为空时的 `Ressurs.failure` 语义。 |
| Behavior 11: Add a child to treatment basis and reset later treatment steps | 添加 child 不只是插入 person basis，会把后续 condition/result/decision 派生状态重置回需要重新处理的阶段。 | OpenAPI 只能看到“legg-til-barn”，看不到已有结果被废弃、后续步骤被 reset 的业务含义。 |
| Behavior 12: Put treatment on wait, update wait, and resume | wait lifecycle 会修改 treatment status、active wait record、open task deadlines，并发布统计；等待期间普通 step execution 被阻止。 | 黑盒可观察到 status 字段变化，但很难完整提取 task deadline、统计发布、no-op update 被拒绝、resume deadline 调整等副作用。 |
| Behavior 14: Refresh treatment register basis and manually record death | `GET /api/person/oppdater-registeropplysninger/{behandlingId}` 是写操作；manual death 必须绑定 treatment participant。 | OpenAPI 的 `GET` 会误导为读取；没有 service 代码看不出 register basis 被刷新，也看不出 person 必须属于该 treatment。 |
| Behavior 15: Maintain condition assessment records | 条件评估的 add/update/delete 会重置后续 result/decision 状态，且 `personIdent`、`vilkaarId`、`annenVurderingId` 必须绑定 active assessment。 | OpenAPI 能列出 CRUD，但无法说明这些不是孤立编辑，而是 entitlement state 的 reset point。 |
| Behavior 17: Maintain foreign period amounts | API 只有 update/delete，没有可 API-realizable 的初始 create；update 会 load 既有 row 并保留 `utbetalingsland`。 | 只看 path 名容易以为完整支持维护生命周期；读 service 才能证明创建缺口和“必须已有 id”。 |
| Behavior 18/19: Currency-rate update branches | ECB 分支取决于币种/日期变化；`ISK` 且日期早于 2018-02-01 走手工汇率分支。 | OpenAPI 看不到这种硬编码业务分支，也看不到 ECB lookup 只在 code/date 变化时发生。 |
| Behavior 20: Maintain changed payment shares and reset treatment result | changed payment share 的 create/update/delete 都会触发 recalculation-sensitive reset，显式 `tilbakestill` 会把 treatment 回退到 result step。 | 黑盒只能看到资源变化，难以可靠知道 payment recalculation 和 treatment result reset 是核心业务结果。 |
| Behavior 22/23: Maintain EEA refund and overpaid currency periods | path 上带 `behandlingId`，但部分 update/delete service 主要按 child `id` load；path-treatment ownership 弱于 endpoint 形状暗示。 | 只有读 service/repository 才能发现 parent-child ownership enforcement 不完整，OpenAPI path 本身会给出相反暗示。 |
| Behavior 24/25: Corrected decision/after-payment metadata | 创建新 active correction 会先 deactivate 旧 active record；deactivate 在没有 active record 时可能是 no-op。 | OpenAPI 只能显示 create/list/patch，不能说明 active-flag 语义、唯一 active 约束和 no-op 行为。 |
| Behavior 31 and Missing Behavior 8: Manual letter recipients | 删除 recipient 主要按 `mottakerId`，缺少 service-level treatment ownership；历史/audit 只能通过 broad treatment log 间接看。 | 黑盒无法证明跨 treatment id 错误是否会被拦截，也无法判断缺少 recipient-specific history 是真实能力缺口。 |
| Behavior 48/59: Complete task / queue rate change with `GET` | 外部 task completion 和 single-case rate-change queue 都是 mutating `GET`。 | 仅依赖 HTTP method 会把它们归为读取或安全操作，从而误提取 behavior。 |
| Behavior 61/62: Rate change sync vs identity queue | 同步 rate change 需要 eligibility check 并立即执行；identity-based queue 先解析 person identities 再创建 task，不等同于直接传 case id。 | OpenAPI 可见接口不同，但只有代码能确认两者的业务边界、候选发现逻辑和失败/降级语义。 |
| Behavior 64: Identify ongoing cases without latest rate | 返回 `callId` 只代表后台分析启动；Swagger 中有 task/callId 辅助路径，但项目内没有对应 controller function。 | 没有代码会误以为可通过 Swagger task endpoint 查询结果；白盒才能发现 result retrieval 不可作为 supported behavior。 |
| Behavior 70/72/73/74: Statistics sent-state and queueing | 登记 sent-state、按 unsent filter queue、手动 queue 绕过正常 sent-state filter、migration resend 是不同业务能力。 | OpenAPI 可见多个统计 endpoint，但读实现才能区分防重复发送、手工补发、dry-run/real mutation 等业务差异。 |
| Behavior 75/77/78/80/85: Administrative bulk operations | 多个 admin bulk endpoint 是 partial success：捕获单项异常、继续处理其他项、返回或记录失败集合。 | 黑盒一次调用可能只看到整体响应；没有代码无法证明是否事务化、是否会留下混合状态、重试是否会重复副作用。 |
| Behavior 90: Fill empty condition start dates in preprod | 该行为是 profile/environment-gated 的 preprod/local 数据修复，生产环境应拒绝。 | OpenAPI 无法表达运行 profile 约束；只有实现能判断它不是普通生产业务行为。 |

## 5. 没有代码更难证明的 missing behavior

白盒分析不仅能发现“支持什么”，还能证明“不能支持什么”。这类 missing behavior 通常无法通过黑盒可靠证明，因为黑盒只能看到某次调用失败，不能证明所有函数组合都不够。

| Missing Behavior | 白盒证明点 | 没有代码时的风险 |
|---|---|---|
| Missing Behavior 1: Fully synchronous treatment closure after decision implementation | `decide treatment` 后续 ordinary implementation step、economy callback、journal/distribution/finalization 在代码中是内部 task/step，不是 public function。 | 可能误以为调用 decision、generate letter、admin payment send 就等于完整关闭 treatment。 |
| Missing Behavior 2: API-create foreign amount and currency-rate rows | update/delete service 都要求已有 row id；没有独立 create/upsert 入口能保证初始 row。 | 可能把 update endpoint 当作完整 lifecycle，忽略 direct DB/internal generation 依赖。 |
| Missing Behavior 3: Strong child-resource ownership enforcement | `RefusjonEøsService`、`FeilutbetaltValutaService`、`BrevmottakerService` 等路径显示 parent id，但实现存在按 child id 操作的弱归属点。 | 只看 path 会认为 parent scoping 已经安全 enforce。 |
| Missing Behavior 4: Query corrected decision metadata and small-child supplement corrections directly | corrected after-payment 有 list，但 corrected decision metadata 和 small-child supplement correction 没有等价 list/retrieve function。 | 容易把 broad `retrieve treatment` 或 log 当作资源级查询能力。 |
| Missing Behavior 5: Async task correlation and result retrieval | 许多 queue endpoint 不返回稳定 task id；Swagger auxiliary `/api/task/*` 没有项目 controller 实现证据。 | 可能把 Swagger 中的 shared task endpoint 当成业务可用能力，误报“可追踪异步结果”。 |
| Missing Behavior 6: Transactional all-or-nothing administrative bulk operations | Forvalter/admin 逻辑按 item catch exception 并继续，产生 partial success。 | 黑盒很难区分“接口支持批量”与“批量是原子事务”。 |
| Missing Behavior 7: Public per-case close/reopen lifecycle | Public fagsak endpoint 只覆盖 create/read/search；close/reopen 只有服务级发现/批量更新状态，且不是 case-scoped explicit transition。 | 可能误把 admin bulk update 当成业务用户可控的 close/reopen。 |
| Missing Behavior 8: Safe delete/update ownership for letter and document recipient effects | recipient delete 缺少强 treatment ownership，历史记录不是 typed recipient history contract。 | 只看 list/delete endpoint 会认为 recipient lifecycle 完整且可审计。 |

## 6. 提示词为什么能放大白盒价值

本次生成 `business-behavior.md` 的提示词对结果质量有直接影响。它不仅要求读取 `full-behavior.md`，还明确要求读取 OpenAPI、实现源码、测试和 seed data，并在 OpenAPI 与源码不一致时优先实现逻辑。这让输出从“接口清单”升级为“业务行为模型”。

提示词中对白盒价值最关键的约束包括：

- 要求用 `full-behavior.md` 的 exact function name 引用 workflow step，避免行为描述脱离可执行函数。
- 要求 required execution workflow 包含建立状态所需的 setup function，避免假设数据库里已经有资源。
- 要求 Existing-state shortcuts 单独说明，区分完整 API-realizable workflow 和已有状态捷径。
- 要求 Parameter and value bindings，强制说明 id、identity、body/path/query/header 值如何跨函数复用。
- 要求 Failure and exceptional cases，促使分析进入 controller/service 级业务约束。
- 要求 Unsupported or Missing Business Behaviors，并证明函数组合为什么仍不够。
- 要求实现优先于 OpenAPI，确保 mutating `GET`、Swagger/source discrepancy、failure resource 等不会被 schema 掩盖。

这些要求共同保证：输出的 behavior 是 domain-facing capability，而不是 endpoint-by-endpoint 的机械改写。

## 7. 推荐的白盒提取方法

针对类似 REST API 服务，建议按以下顺序进行 business-behavior 白盒提取：

1. 从 OpenAPI 和 `full-behavior.md` 建立 function inventory，只记录真实可调用函数。
2. 按 controller/service/repository 找到 aggregate boundary，例如 `fagsakId`、`behandlingId`、`vedtakId`、`oppgaveId`。
3. 沿 service 调用链追踪 state transition、生成 ID、active flag、status、step、task payload、外部系统调用。
4. 区分同步完成、异步排队、后台 job、admin repair、read-only verification。
5. 对每个 behavior 写完整 API-realizable workflow，再单独写 existing-state shortcut。
6. 对每个 mutation 检查是否重置、级联、发布统计、更新任务期限、调用外部系统或产生 no-op。
7. 对每个 child-resource endpoint 检查 path parent id 是否真的在 service/repository 层 enforce。
8. 对每个 bulk endpoint 检查事务边界，是 all-or-nothing 还是 partial success。
9. 对 OpenAPI 与源码差异建立清单，特别关注 mutating `GET`、body/header mismatch、Swagger 中有但项目内无实现的路径。
10. 最后写 missing behavior，并给出“为什么函数组合仍然不足”的证明。

## 8. 对业务与工程的实际收益

白盒 business-behavior 分析能带来以下收益：

- 帮助领域专家看到系统真实支持的业务流程，而不是 API 表面的资源列表。
- 帮助测试设计覆盖状态机边界、角色边界、等待/锁定/关闭状态、异步 task 和 partial success。
- 帮助 API 治理发现语义问题，例如写操作使用 `GET`、HTTP 200 failure resource、OpenAPI 与源码不一致。
- 帮助安全和数据一致性审查发现 parent-child ownership、direct id mutation、缺少审计查询等风险。
- 帮助产品/业务判断哪些能力只是内部维护工具，哪些才是可承诺给调用方的 public workflow。
- 帮助后续重构确定优先级：补 task correlation、补 create/list endpoint、补强归属校验、补事务或 per-item audit。

结论：对于本仓库这样的复杂业务系统，白盒分析不是可选增强，而是生成可信 business-behavior 文档的必要条件。没有代码，很多 behavior 只能被猜测；有代码，才能把“接口能调用”还原为“业务状态如何真实改变，以及哪些能力实际上不存在”。
