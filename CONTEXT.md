# Context — demo-repository

## 系统上下文

本项目是一个 **GitHub 最佳实践示范仓库**，目标是作为开发者学习 GitHub 平台功能的参考实现。通过 pm-idea-to-mvp v7.2.0 pipeline 的 `import_repo` 场景导入，从原始需求视角对仓库进行深度优化。

## Glossary

| 术语 | 定义 |
|------|------|
| demo-repository | 展示 GitHub 最佳实践的代码仓库，作为模板和学习资源 |
| GitHub Actions | GitHub 原生 CI/CD 自动化平台，通过 YAML workflow 定义构建/测试/部署流水线 |
| GitHub Issues | GitHub 内置的问题跟踪系统，支持模板、标签、里程碑 |
| PR Template | Pull Request 模板，标准化贡献者提交代码时的描述格式 |
| GitHub Projects | GitHub 内置的项目管理看板，支持自动化工作流 |
| import_repo | PM pipeline 场景之一，用于导入外部仓库并走完整流水线 |
| harness-rules | 项目治理规则文件，定义技术栈、测试命令、人工检查点等 |
| CI/CD Pipeline | 持续集成/持续部署流水线，覆盖 lint → test → build → deploy |
| 模板仓库 | GitHub Template Repository，可一键 fork 为新项目而不继承 git 历史 |

## Stakeholders

| 角色 | 关注点 |
|------|--------|
| 开发者（主要用户） | 快速理解并应用 GitHub 最佳实践 |
| 开源维护者 | 标准化贡献流程、自动化质量门禁 |
| DevOps 工程师 | CI/CD 流水线设计、部署自动化 |
| 项目拥有者（用户） | 通过优化仓库深入理解 GitHub 功能 |

## Constraints

- 技术栈：Python（harness-rules.yaml 指定）
- 文档语言：中文为主（zh-CN）
- 源仓库可能不可访问（GitHub 404）
- 产物为静态仓库，非运行时服务

## Non-goals

- 不构建 SaaS / Web 应用
- 不涉及商业变现
- 不做移动端适配
- 不替代 GitHub 官方文档
