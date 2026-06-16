# C4 Level 1 — 系统上下文

## 系统

**demo-repository** — GitHub 最佳实践示范仓库，帮助开发者快速学习并应用 GitHub 平台功能（Actions、Issues、PR、Projects、CI/CD）。

## 外部角色

| 角色 | 说明 |
|------|------|
| 开发者（Developer） | 主要用户。clone 仓库学习 GitHub 最佳实践，参考 CI/CD workflow 配置自己的项目 |
| 开源维护者（Maintainer） | 参考社区健康文件（CONTRIBUTING、CODE_OF_CONDUCT）和 CODEOWNERS 标准化自己的贡献流程 |
| DevOps 工程师 | 参考多语言 Monorepo 的 CI/CD 设计模式（路径过滤、矩阵构建、缓存优化） |
| 模板使用者（Template User） | 使用 Template 功能一键 fork 仓库为新项目，通过 init.sh 替换变量快速启动 |

## 外部系统

| 系统 | 说明 |
|------|------|
| GitHub Platform | 托管仓库、运行 Actions、管理 Issues/PR/Projects |
| GitHub Actions Marketplace | 提供第三方 Action（dorny/paths-filter、subosito/flutter-action 等） |
| Docker Hub | 提供基础镜像（mysql:8.0、redis:7.2-alpine、golang、python、node） |

## 上下文图

```mermaid
C4Context
  title demo-repository 系统上下文

  person developer as "开发者"
  person maintainer as "开源维护者"
  person devops as "DevOps 工程师"
  person templateUser as "模板使用者"

  system demoRepo as "demo-repository"
  system github as "GitHub Platform"
  system marketplace as "Actions Marketplace"
  system dockerHub as "Docker Hub"

  developer --> demoRepo : clone / 学习最佳实践
  maintainer --> demoRepo : 参考社区健康文件
  devops --> demoRepo : 参考多语言 CI/CD 模式
  templateUser --> demoRepo : Template fork + init.sh

  demoRepo --> github : 托管 + 运行 Actions
  demoRepo --> marketplace : 引用第三方 Action
  demoRepo --> dockerHub : 拉取基础镜像
```

## 价值主张

- **对开发者**：从 clone 到理解 < 5 分钟，每个 GitHub 功能有独立示例和文档
- **对维护者**：开箱即用的社区健康文件模板，标准化贡献流程
- **对 DevOps**：多语言 Monorepo 的 CI/CD 参考实现（路径过滤、矩阵构建、缓存）
- **对模板使用者**：一键 Template + init.sh，快速启动新项目
