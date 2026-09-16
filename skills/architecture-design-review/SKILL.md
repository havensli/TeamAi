---
name: architecture-design-review
description: 依据概要架构、详细设计和 API-Diff 标准执行架构评审，发现边界、质量属性、安全、数据、契约、容灾、变更回写和阶段准入问题。用户提出架构评审、设计走查、质量门禁、文档审查或上线前架构检查时使用。
---

# 架构设计评审

## 目标与输入

对概要架构和详细设计进行 findings-first 评审，输出可执行的阻塞项、整改建议和阶段结论。读取：

- `references/overview-template.md`
- `references/detailed-template.md`
- `references/review-recommendations.md`
- `references/api-diff-template.yaml`

若仓库存在 `AGENTS.md`、`specs` 或组织评审清单，先读取并遵循；发现冲突时以用户和仓库规范为准，并记录偏离。

## 评审顺序

### 1. 文档治理与追溯

检查文档状态、版本、Owner、评审人、变更记录、关联需求、概要/详细设计链接、模块编号、API/事件编号、ADR、`assumptions`、`open_questions`、`risks` 和 `next_step` 是否完整。

### 2. 概要架构质量

检查目标/范围/边界/术语、上下文图、领域边界、逻辑模块、数据所有权、技术选型、部署/故障域、租户隔离、安全、可观测、容灾、容量、演进和 ADR。质量属性必须有数字指标、基线来源和验证方式；基线来源应为历史监控数据、压测报告、业务 SLA 协议、行业基准或容量模型推算之一。

### 3. 详细设计质量

检查模块/类职责、接口字段、统一响应、错误码、鉴权、幂等、超时、重试、熔断、降级、事件 Schema、文件契约、表字段/索引、缓存、时序、状态机、事务、一致性、补偿、并发、迁移、发布、回滚、日志/指标/Trace 和测试验证是否可执行。

### 4. 两阶段一致性

- 详细设计是否继承概要架构的边界、技术选型和一致性模型；
- 若改变任一项，是否先回写概要架构并新增 ADR；
- 概要架构未 `approved` 时，详细设计是否被错误标为 `approved`；
- 模块/API/事件编号、质量指标和风险 Owner 是否可追溯。

### 5. API-Diff 与兼容性

凡新增或修改 HTTP、RPC、事件或文件契约，必须存在 API-Diff，且路径符合：

`api-diffs/{模块编号}/{API编号}_v{版本号}_diff.yaml`

检查字段级请求/响应、包装体、校验、错误码、幂等、版本、兼容、弃用、事件投递和死信规则。没有外部契约变化时，应明确写“本次无需 API diff”。

## 质量门禁

按严重度记录：

- **P0 / blocked**：边界、核心一致性、安全、数据丢失风险、契约缺失或阶段状态错误，必须整改后才能进入下一阶段。
- **P1 / must-fix**：影响开发、联调、发布或运维可靠性的重大缺口，需要指定 Owner 和截止时间。
- **P2 / follow-up**：文档完善、可维护性或优化项，可带整改计划通过。

每条 finding 必须包含：编号、严重度、证据（文件/章节/行号）、问题、影响、建议、Owner、截止时间、复验条件。

## 输出格式

先给结论和 P0/P1 findings，再给完整检查矩阵：

```markdown
Status: pass | pass_with_followups | blocked

## Findings
| ID | Severity | Evidence | Issue | Impact | Remediation | Owner | Due |
|---|---|---|---|---|---|---|---|

## Gate Decision
- Overview gate:
- Detailed gate:
- API contract lock:
- Required follow-ups:

## Assumptions
## Open Questions
## Risks
## Next Step
```

不得用“看起来完整”替代证据；找不到证据时标为未验证并提出补证要求。评审默认只读，不未经授权改写设计文件或代码。
