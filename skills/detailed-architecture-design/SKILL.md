---
name: detailed-architecture-design
description: 基于已批准的概要架构生成可开发、可联调、可迁移、可测试的详细设计，覆盖模块与类、HTTP/RPC/MQ/文件契约、API-Diff、表结构、缓存、时序、状态机、事务、幂等、异常、安全、发布和验证。用户提出详细设计、接口设计、数据库设计、时序图或 API 契约时使用。
---

# 详细架构设计

## 目标

把已批准的概要架构转化为开发、联调、迁移和测试可直接使用的设计。使用 `references/detailed-template.md` 作为章节骨架，使用 `references/api-diff-template.yaml` 生成外部契约变更文件。

## 前置检查

1. 读取概要架构及其版本、状态、模块编号、ADR 和质量指标。
2. 若概要架构不是 `approved`，将本设计标为 `draft` 或 `pass_with_followups`；存在边界、核心技术选型或一致性模型未决时不得标 `approved`。
3. 检查 `AGENTS.md`、`specs` 和现有代码，优先扫描接口入口、DTO/VO/Entity/Mapper、事务、缓存、锁、导入导出和历史兼容逻辑。
4. 明确本次 `In Scope`、`Out of Scope`、目标模块、契约变更和数据模型变更。

## 设计工作流

### 1. 复用与实现边界

对每个点位标记复用、扩展、替换或新增，并记录影响文件/模块。不得把未验证的现有实现写成事实。

### 2. 模块与类设计

明确模块职责、输入/输出、依赖和事务边界；展开 Controller/Handler、DTO/Command、VO/Response、Entity/Aggregate、Repository/Mapper、Service/Biz、Event Handler 的职责与依赖规则。

### 3. 接口和事件契约

字段级锁定 HTTP、RPC、MQ/领域事件和文件导入导出：方法/路径、鉴权、请求/响应字段、校验、错误码、幂等键、超时、重试、熔断、降级、投递语义、Schema 版本和兼容窗口。统一响应至少包含 `code`、`message`、`data`、`status`、`requestId`；若项目已有契约，以项目规范为准。

### 4. API-Diff 规则

只要新增或修改外部 HTTP、RPC、事件或文件契约，就必须生成 API-Diff。文件统一命名为：

`api-diffs/{模块编号}/{API编号}_v{版本号}_diff.yaml`

示例：`api-diffs/M-001/API-001_v1.2_diff.yaml`。一个详细设计涉及多个契约时分别建文件并逐项链接；无外部变化时明确填写“本次无需 API diff”。未确定且会阻塞开发的字段写入 `followups` 或将状态设为 `blocked`。

### 5. 数据与运行时设计

展开表、字段、主键/唯一键、索引、分片、数据分类、生命周期、归档、清理、备份、Schema 演进、缓存 Key/TTL/一致性、时序图、状态机、事务、一致性、补偿、幂等、并发、锁、重试、死信、限流和背压。

### 6. 横切实现

把概要架构的安全与可观测性落到实现点：权限校验位置、输入防护、加密/脱敏、审计事件、密钥轮换、日志字段、指标标签、Trace 传播、告警阈值和 Runbook。

### 7. 迁移、发布和验证

定义迁移规模、方式、校验/对账、回滚和窗口；定义资源规格、配置/密钥来源、灰度、回滚和观察窗口；为契约、性能、故障恢复和安全补充可执行的验证标准。

## 强制变更回写门禁

> ⚠️ 若本详细设计改变概要架构中的系统边界、核心技术选型或一致性模型，必须先更新概要架构并新增 ADR，否则本文档不可进入 `approved` 状态。

## 输出协议

文档末尾强制输出：`status`、`assumptions`、`open_questions`、`risks`、`impacted_files`、`next_step`；涉及契约时额外输出 `contract_lock_status` 和 `api_diff_followups`。

## 不越界

不要用详细设计替代测试用例、验收标准或完整运维手册；只提供架构验证入口和实现约束。不要未经授权修改业务代码、数据库或部署环境。
