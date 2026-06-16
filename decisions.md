# Decisions — demo-repository

## ADR-001: 源仓库不可访问时的处理策略

- **Status**: accepted
- **Context**: `amnRideAI/demo-repository` 在 GitHub 最初返回 404。pipeline 的 `import_repo` 场景依赖 clone 外部仓库作为起点。
- **Decision**: 基于 GitHub 最佳实践通用知识重建仓库结构，不依赖源仓库内容。若用户后续提供本地副本或替代仓库，可在 research 阶段调整。
- **Consequences**:
  - 正面: 不阻塞 pipeline 推进
  - 负面: 产物可能缺少源仓库的特定设计意图
  - **实际结果**: 源仓库 clone 成功（_source_repo/），发现是多语言 Monorepo（Go+Python+Flutter+React+Node）。基于实际结构调整了分析方向。

## ADR-002: 仓库定位为"学习模板"而非"工具产品"

- **Status**: accepted
- **Context**: 原始需求提到"深入优化"，但未明确优化方向。两种可能路径：(a) 可复用的模板仓库 (b) 实际运行的工具产品。
- **Decision**: 将仓库定位为 **GitHub 最佳实践学习模板**，每个功能模块附带说明文档和使用指南。包含模板化改造（Template Repository + init.sh）。
- **Consequences**:
  - 正面: 不涉及运行时服务部署，ship 阶段简化；可复用为新项目
  - 负面: 若用户期望工具产品，需大幅调整
  - 影响容器: Community Files, Learning Docs, Init Scripts

## ADR-003: 技术栈覆盖策略

- **Status**: superseded by ADR-003b
- **Context**: harness-rules.yaml 指定 `tech_stack: python`。最初假设所有示例代码和 CI/CD 均基于 Python。
- **Original Decision**: 示例项目使用 Python，CI/CD workflow 使用 Python 生态工具。
- **Superseded**: 源仓库实际是多语言 Monorepo（Go + Python + Flutter + React + Node），仅覆盖 Python 无法展示 Monorepo 路径过滤的核心价值。
- **Affected Containers**: Source Services, CI Workflows

### ADR-003b: 技术栈覆盖全语言

- **Status**: accepted
- **Context**: 源仓库包含 6 个服务（ride-hermes/Go, ai-service/Python, admin-web/React, driver-app/Flutter, passenger-app/Flutter, ride-hermes-mcp/Node）。仅覆盖 Python 无法展示 Monorepo CI 的最佳实践。
- **Decision**: CI/CD 覆盖全语言（Go + Python + Node + Flutter），但 harness-rules.yaml 的 `tech_stack: python` 约束仅影响 MVP 代码实现（04-mvp/），不影响 CI workflow 设计。
- **Consequences**:
  - 正面: 完整展示多语言 Monorepo 的 CI/CD 最佳实践
  - 负面: 需要维护 5+ 个不同语言的 workflow
  - 风险: Flutter CI 环境配置复杂，可能超时
  - 影响容器: CI Workflows, Source Services

## ADR-004: 文档语言以中文为主

- **Status**: accepted
- **Context**: harness-rules.yaml 指定 `language: zh-CN`，用户主要使用中文交流。
- **Decision**: 所有文档、README、注释使用中文；代码标识符和 YAML 配置保留英文。
- **Consequences**:
  - 正面: 符合目标用户画像
  - 负面: 对非中文用户的可访问性降低
  - 影响容器: Community Files, Learning Docs

## ADR-005: 仓库结构采用功能模块化布局

- **Status**: accepted
- **Context**: 作为学习模板，仓库结构需要清晰展示每个 GitHub 功能的独立实现。
- **Decision**: 采用功能模块化布局，每个 GitHub 功能有独立目录和 README。
- **Consequences**:
  - 正面: 易于定位和学习特定功能
  - 负面: 可能导致目录层级较深
  - 影响容器: 全部容器

## ADR-006: 多语言 CI 策略 — 每服务独立 workflow + paths-filter

- **Status**: accepted
- **Context**: 源仓库是多语言 Monorepo，需要为每个服务设计独立的 CI/CD 流水线。两种策略：(a) 单一 monolith workflow + matrix (b) 每服务独立 workflow + paths-filter。
- **Decision**: 采用策略 (b) — 为每个服务创建独立 workflow（ci-backend.yml / ci-ai-service.yml / ci-admin-web.yml / ci-mobile.yml / ci-mcp.yml），使用 `dorny/paths-filter@v3` 检测变更目录，仅触发对应 workflow。
- **Rationale**:
  - 独立失败、独立重试、独立缓存
  - 避免无关变更触发全量构建，节省 Actions 分钟
  - 展示 Monorepo CI 的核心最佳实践
  - 替代方案（单一 workflow + matrix）失败时需全量重跑
- **Consequences**:
  - 正面: 增量构建，缓存友好，可教学性强
  - 负面: 需要维护 5+ 个 workflow 文件
  - 缓解: 使用 Reusable Workflows 提取公共 lint 逻辑
  - 影响容器: CI Workflows（Path Filter, Go/Python/Node/Flutter CI Pipeline, Dependency Cache）

## ADR-007: 模板化改造策略

- **Status**: accepted
- **Context**: 仓库定位为"学习模板"（ADR-002），需要支持一键 Template 为新项目。
- **Decision**: 标记仓库为 Template Repository，创建 `scripts/init.sh` 替换项目名称/作者/描述等变量。README 增加"使用此模板"章节。
- **Consequences**:
  - 正面: 可复用为新项目启动模板
  - 负面: 需要清理示例内容，添加 TODO 标记
  - 影响容器: Init Scripts, Community Files

## 假设汇总与验证计划

| 假设 | 风险等级 | 验证方式 | 阶段 | 状态 |
|------|----------|----------|------|------|
| 源仓库不可访问 | HIGH | git clone 测试 | research | ✅ 已验证（clone 成功） |
| 用户关注 CI/CD | MEDIUM | align 检查点确认 | align | ⏳ 待确认 |
| 优化方向为教学 | MEDIUM | align 检查点确认 | align | ⏳ 待确认 |
| Python 为唯一语言 | LOW | 检查源仓库 | research | ❌ 已推翻（多语言） |
| 功能模块化布局 | LOW | 对比其他模板 | research | ✅ 已验证 |
| paths-filter 减少 50%+ 无关触发 | MEDIUM | 对比开启前后运行次数 | Phase 2 | ⏳ 待验证 |
| 首次体验 < 5 分钟 | MEDIUM | 邀请 3 位开发者测试 | Phase 3 | ⏳ 待验证 |
