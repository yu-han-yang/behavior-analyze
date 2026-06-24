- 测试套件每个用例前都会 `resetDatabase(Arrays.asList())`，而且没有业务 fixture 注入：[测试文件](/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/tests/EM_tiltaksgjennomforing_True_25_false_false_SPECIFIED_false_0_Test.java:76)
- 195 个测试里状态码分布是：`200` 只有 13 次，`400` 100 次，`401` 75 次，`403` 5 次，`500` 10 次。也就是说主要是在 fuzz 请求入口。
- 13 个 `200` 基本集中在 code list、feature、healthcheck、Altinn URL 这种无状态查询；不是 agreement lifecycle。
- 真正业务 failure 通常需要先有一个“有效业务对象 + 正确状态”，比如已创建 agreement、已审批/未审批、已有 tilskuddsperiode、已有 varsel、已有 journal candidate。当前生成测试几乎没有这些前置状态。

所以“按理说 happy path 覆盖后 failure 很容易覆盖”这个前提在这里没有成立：**大多数核心 happy path 本身都没覆盖**。没有创建出合法 agreement，就很难覆盖“已审批后不能 dry-run update”“结束日期必须更早”“employer 已经审批过”“notification 不属于当前用户”等业务失败。

第二个问题是 **failure 在代码里常常藏在多层前置条件后面**。比如很多 agreement 操作先要通过：

- UUID/path/query/header/cookie 绑定
- `innlogget-part` / token issuer 组合
- repository 找到真实 `Avtale`
- agreement 当前状态满足某个阶段

然后才会进入业务分支。看 [AvtaleController](/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java:260) 这一类 mutation endpoint，很多都是先 `findById(...).orElseThrow(...)`，再调用领域方法。生成测试大量使用随机/非法 id，所以失败在入口或 not-found 前置处，没到真正业务规则。