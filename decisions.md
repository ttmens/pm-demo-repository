# Decisions — demo-repository

## ADR-001: 源仓库不可访问时的处理策略

- **Status**: proposed
- **Context**: `amnRideAI/demo-repository` 在 GitHub 返回 404（可能已删除或设为私有）。
  pipeline 的 `import_repo` 场景依赖 clone 外部仓库作为起点。
  这是一个高风险假设——若仓库有独特结构，重建产物可能偏离用户期望。
- **Decision**: 基于 GitHub 最佳实践通用知识重建仓库结构，不依赖源仓库内容。
  若用户后续提供本地副本或替代仓库，可在 research 阶段调整。
- **Consequences**:
  - 正面: 不阻塞 pipeline 推进
  - 负面: 产物可能缺少源仓库的特定设计意图
  - 风险缓解: align 检查点由用户确认方向是否正确

## ADR-002: 仓库定位为"学习模板"而非"工具产品"

- **Status**: proposed
- **Context**: 原始需求提到"深入优化"，但未明确优化方向。
  两种可能路径：(a) 可复用的模板仓库 (b) 实际运行的工具产品。
  假设用户意图是 (a)，因为原始描述强调"展示 GitHub 最佳实践"。
- **Decision**: 将仓库定位为 **GitHub 最佳实践学习模板**，
  每个功能模块附带说明文档和使用指南。
- **Consequences**:
  - 正面: 不涉及运行时服务部署，ship 阶段简化
  - 负面: 若用户期望工具产品，需大幅调整
  - 验证: align 检查点确认

## ADR-003: 技术栈锁定 Python

- **Status**: accepted
- **Context**: harness-rules.yaml 已指定 `tech_stack: python`。
  所有示例代码和 CI/CD 均基于 Python。
  假设用户接受 Python 作为唯一示例语言。
- **Decision**: 示例项目使用 Python，CI/CD workflow 使用 Python 生态工具
  （pytest、ruff、pip）。
- **Consequences**:
  - 正面: 技术栈统一，降低维护复杂度
  - 负面: 不覆盖其他语言生态的最佳实践
  - 风险: 若用户期望多语言示例，需扩展范围

## ADR-004: 文档语言以中文为主

- **Status**: accepted
- **Context**: harness-rules.yaml 指定 `language: zh-CN`，用户主要使用中文交流。
  假设目标用户群体为中文开发者。
- **Decision**: 所有文档、README、注释使用中文；代码标识符和 YAML 配置保留英文。
- **Consequences**:
  - 正面: 符合目标用户画像
  - 负面: 对非中文用户的可访问性降低
  - 风险: 若目标是国际化项目，需增加英文版本

## ADR-005: 仓库结构采用功能模块化布局

- **Status**: proposed
- **Context**: 作为学习模板，仓库结构需要清晰展示每个 GitHub 功能的独立实现。
  假设按功能模块组织（.github/actions/、.github/ISSUE_TEMPLATE/ 等）。
- **Decision**: 采用功能模块化布局，每个 GitHub 功能有独立目录和 README。
- **Consequences**:
  - 正面: 易于定位和学习特定功能
  - 负面: 可能导致目录层级较深
  - 验证: research 阶段评估其他开源模板仓库的结构

## 假设汇总与验证计划

| 假设 | 风险等级 | 验证方式 | 阶段 |
|------|----------|----------|------|
| 源仓库不可访问 | HIGH | git clone 测试 | research |
| 用户关注 CI/CD | MEDIUM | align 检查点确认 | align |
| 优化方向为教学 | MEDIUM | align 检查点确认 | align |
| Python 为唯一语言 | LOW | 检查源仓库 | research |
| 功能模块化布局 | LOW | 对比其他模板 | research |
