# Design — demo-repository

> 本文件链接 C4 架构模型，不重复描述架构细节。

## 架构文档

- [C4 Level 1 — 系统上下文](../architecture/c4-context.md)
- [C4 Level 2 — 容器](../architecture/c4-container.md)
- [C4 Level 3 — 组件](../architecture/c4-component.md)

## 设计原则

1. **功能模块化**：每个 GitHub 功能有独立目录和文档
2. **增量构建**：paths-filter 仅构建变更的服务
3. **可复用性**：Reusable Workflows 提取公共逻辑
4. **可教学性**：每个功能模块附带学习文档

## 技术决策摘要

详见 [decisions.md](../decisions.md) 和 [02-analysis.md](../02-analysis.md)。

| 决策 | 选择 | ADR |
|------|------|-----|
| CI 策略 | 每服务独立 workflow + paths-filter | ADR-006 |
| 模板化 | Template Repository + init.sh | ADR-002 |
| 文档语言 | 中文为主，代码标识符英文 | ADR-004 |
| 技术栈覆盖 | 全语言（Go/Python/Node/Flutter） | ADR-003 (updated) |
