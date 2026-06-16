# Decisions — demo-repository

## ADR-001: 源仓库不可访问时的处理策略

- **Status**: proposed
- **Context**: `amnRideAI/demo-repository` 在 GitHub 返回 404（可能已删除或设为私有）。pipeline 的 `import_repo` 场景依赖 clone 外部仓库作为起点。
- **Decision**: 基于 GitHub 最佳实践通用知识重建仓库结构，不依赖源仓库内容。若用户后续提供本地副本或替代仓库，可在 research 阶段调整。
- **Consequences**: 产物可能偏离用户原始期望（如果源仓库有特定结构）。需在 align 检查点由用户确认方向。

## ADR-002: 仓库定位为"学习模板"而非"工具产品"

- **Status**: proposed
- **Context**: 原始需求提到"深入优化"，但未明确优化方向——是做成可复用的模板仓库，还是做成一个实际运行的工具。
- **Decision**: 将仓库定位为 **GitHub 最佳实践学习模板**，每个功能模块附带说明文档和使用指南。
- **Consequences**: 不涉及运行时服务部署，ship 阶段简化为 GitHub Pages 文档发布。

## ADR-003: 技术栈锁定 Python

- **Status**: accepted
- **Context**: harness-rules.yaml 已指定 `tech_stack: python`，所有示例代码和 CI/CD 均基于 Python。
- **Decision**: 示例项目使用 Python，CI/CD workflow 使用 Python 生态工具（pytest、ruff、pip）。
- **Consequences**: 不涉及其他语言生态的最佳实践展示（如 Node.js、Go）。

## ADR-004: 文档语言以中文为主

- **Status**: accepted
- **Context**: harness-rules.yaml 指定 `language: zh-CN`，用户主要使用中文交流。
- **Decision**: 所有文档、README、注释使用中文；代码标识符和 YAML 配置保留英文。
- **Consequences**: 对非中文用户的可访问性降低，但符合目标用户画像。
