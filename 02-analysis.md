# 02-analysis.md — 方案论证

> **项目**: demo-repository  
> **阶段**: Analysis  
> **日期**: 2026-06-17  
> **输入**: 01-research.md, CONTEXT.md, _source_repo/

---

## 1. 问题陈述

### 1.1 核心问题

`amnRideAI/demo-repository` 是一个**多语言 Monorepo 网约车平台示范仓库**（Go 后端 + Python AI + Flutter 移动端 + React 管理后台 + Node MCP），但当前在 GitHub 最佳实践方面存在严重缺失：

| 维度 | 现状 | 目标 |
|------|------|------|
| CI/CD 覆盖率 | 2 个 workflow（auto-assign + proof-html），无 lint/test/build | 每个服务独立 CI，路径过滤触发 |
| 社区健康文件 | 仅极简 README（4 行） | 完整 Community Profile（README/LICENSE/CONTRIBUTING/CODE_OF_CONDUCT/SECURITY） |
| 多语言支持 | 无，未区分服务独立构建 | Go/Python/Node/Flutter 各有专属 workflow |
| 模板化程度 | 无，不可直接复用为新项目 | 标记为 Template Repository，含初始化脚本 |
| 可教学性 | 无注释/说明 | 每个功能模块附带学习文档 |

### 1.2 用户痛点

- **开发者**：无法从该仓库学到 GitHub Actions 的实战用法（路径过滤、矩阵构建、缓存优化）
- **开源维护者**：缺少标准化的贡献流程（无 CONTRIBUTING、无 PR 模板、无 CODEOWNERS）
- **DevOps 工程师**：无法参考多语言 Monorepo 的 CI/CD 设计模式

### 1.3 成功标准

1. GitHub Community Profile 评分从 ~20% 提升到 100%
2. 每个服务（ride-hermes/ai-service/admin-web/driver-app/passenger-app/mcp）有独立 CI workflow
3. `paths-filter` 实现增量构建，无关变更不触发全量 CI
4. 仓库可一键 Template 为新项目，含 `scripts/init.sh` 替换变量
5. 从 clone 到理解仓库用途 < 5 分钟（首次体验）

---

## 2. 方案选项

### 方案 A：最小可行改造（Minimal Viable Enhancement）

**描述**：仅补充社区健康文件 + 1 个通用 CI workflow，不区分服务。

**范围**：
- 补充 README.md（项目概述、架构、快速开始）
- 补充 LICENSE（MIT）
- 补充 CONTRIBUTING.md
- 补充 CODE_OF_CONDUCT.md（Contributor Covenant）
- 补充 SECURITY.md
- 创建 1 个 `ci.yml`：对所有 PR 运行 Python lint + test（仅覆盖 ai-service）

**优点**：
- 工作量最小（~2 天）
- Community Profile 评分快速提升
- 不涉及复杂的多语言 CI 设计

**缺点**：
- 不解决多语言 Monorepo 的核心痛点
- 无法展示 GitHub Actions 的高级用法（路径过滤、矩阵构建）
- 对其他服务（Go/Flutter/React）的开发者无价值

**风险**：
- 若用户期望完整的多语言 CI，需大幅返工

**置信度**：🟡 中

---

### 方案 B：分阶段渐进改造（Phased Progressive Enhancement）⭐ 推荐

**描述**：分 3 个阶段逐步完善，每阶段交付可独立使用的产物。

**阶段划分**：

**Phase 1 — 社区健康文件（1 天）**
- 补充完整 Community Profile（README/LICENSE/CONTRIBUTING/CODE_OF_CONDUCT/SECURITY）
- 创建 `.github/CODEOWNERS`
- 创建 `.github/ISSUE_TEMPLATE/`（bug_report.md, feature_request.md）
- 创建 `.github/PULL_REQUEST_TEMPLATE.md`

**Phase 2 — 多语言 CI/CD（3 天）**
- 为每个服务创建独立 workflow：
  - `ci-backend.yml`（Go: lint + test + build）
  - `ci-ai-service.yml`（Python: ruff + pytest + build）
  - `ci-admin-web.yml`（Node: lint + test + build）
  - `ci-mobile.yml`（Flutter: analyze + build，覆盖 driver-app + passenger-app）
  - `ci-mcp.yml`（Node: lint + test）
- 使用 `dorny/paths-filter@v3` 实现路径过滤
- 使用 `actions/cache@v4` 优化依赖缓存
- 创建 `docker-build.yml`：main push 时构建全服务镜像

**Phase 3 — 模板化改造（2 天）**
- 标记仓库为 Template Repository
- 创建 `scripts/init.sh`：替换项目名称、作者、描述等变量
- 创建 `docs/LEARNING_GUIDE.md`：每个 GitHub 功能的学习路径
- 清理示例内容，添加 `TODO:` 标记自定义点

**优点**：
- 每阶段交付独立价值，可中途停止
- 完整覆盖多语言 Monorepo 的 CI/CD 最佳实践
- 模板化改造使仓库可复用
- 符合"学习模板"定位（ADR-002）

**缺点**：
- 总工作量 ~6 天，比方案 A 多
- 需要维护 5+ 个 workflow 文件

**风险**：
- Flutter CI 可能因环境配置复杂而超时（缓解：使用 `subosito/flutter-action@v2` + 缓存）
- 多 workflow 维护成本（缓解：使用 Reusable Workflows 提取公共逻辑）

**置信度**：🟢 高

---

### 方案 C：全量一步到位（Full Comprehensive Overhaul）

**描述**：一次性完成所有改造，包括高级功能（Release 自动化、Dependabot、SLSA 签名）。

**范围**：方案 B 全部 + 
- `release.yml`：tag push 时自动发布 + 生成 changelog
- `dependabot.yml`：自动依赖更新 PR
- `stale.yml`：自动关闭过期 issue
- SLSA Level 3 构建证明（Sigstore 签名）
- Ephemeral Runners 配置（自托管 runner）
- 多语言 Super-Linter 集成

**优点**：
- 一次到位，无需后续迭代
- 覆盖 GitHub 平台的所有高级功能
- 可作为"生产级 Monorepo"的完整参考

**缺点**：
- 工作量 ~10 天
- 复杂度过高，可能超出"学习模板"的定位
- SLSA/Ephemeral Runners 对初学者不友好

**风险**：
- 过度工程化，违背"简洁"原则
- 若用户需求变化，返工成本高

**置信度**：🟡 中

---

### 方案 D：聚焦单一语言（Single-Language Focus）

**描述**：仅改造 Python 部分（ai-service），忽略其他语言。

**范围**：
- 方案 A 的社区健康文件
- 1 个 `ci-ai-service.yml`（Python: ruff + pytest + build）
- 1 个 `docker-ai.yml`（构建 ai-service 镜像）

**优点**：
- 工作量 ~3 天
- 符合 harness-rules.yaml 的 `tech_stack: python` 约束
- 符合 ADR-003（Python 为唯一语言）

**缺点**：
- 与实际仓库结构不符（源仓库是多语言 Monorepo）
- 对 Go/Flutter/React 开发者无价值
- 无法展示 Monorepo 路径过滤的核心价值

**风险**：
- 若用户期望完整的多语言支持，需大幅返工

**置信度**：🟡 中

---

## 3. 方案对比矩阵

| 维度 | 方案 A（最小） | 方案 B（分阶段）⭐ | 方案 C（全量） | 方案 D（单语言） |
|------|---------------|-------------------|---------------|-----------------|
| 工作量 | 2 天 | 6 天 | 10 天 | 3 天 |
| Community Profile | ✅ 100% | ✅ 100% | ✅ 100% | ✅ 100% |
| 多语言 CI | ❌ 仅 Python | ✅ 全语言 | ✅ 全语言 | ❌ 仅 Python |
| 路径过滤 | ❌ | ✅ | ✅ | ❌ |
| 模板化 | ❌ | ✅ | ✅ | ❌ |
| 高级功能 | ❌ | ❌ | ✅ | ❌ |
| 可教学性 | 🟡 低 | 🟢 高 | 🟡 中（过于复杂） | 🟡 低 |
| 维护成本 | 低 | 中 | 高 | 低 |
| 符合用户期望 | 🟡 可能不足 | 🟢 高 | 🟡 可能过度 | 🟡 可能不足 |

---

## 4. SWOT 分析（方案 B）

### Strengths（优势）

- **分阶段交付**：每阶段独立可用，降低风险
- **完整覆盖**：多语言 CI + 社区健康 + 模板化，全面解决痛点
- **可教学性**：每个功能模块可独立学习，符合"学习模板"定位
- **路径过滤**：展示 Monorepo CI 的核心最佳实践

### Weaknesses（劣势）

- **工作量中等**：6 天比方案 A 多，但比方案 C 少
- **多 workflow 维护**：5+ 个 workflow 文件需要同步更新
- **Flutter CI 复杂性**：移动端构建环境配置可能超时

### Opportunities（机会）

- **GitHub 官方推荐**：路径过滤、Reusable Workflows 是 GitHub 官方推荐模式
- **社区影响力**：完整的多语言 Monorepo 示范可吸引开源贡献者
- **复用价值**：模板化后可直接用于新项目启动

### Threats（威胁）

- **技术栈变化**：若用户后续增加新语言（如 Rust），需扩展 workflow
- **GitHub Actions 限制**：免费账户每月 2000 分钟，多 workflow 可能超限
- **维护负担**：若仓库不活跃，workflow 可能因依赖过期而失败

---

## 5. 推荐方案

**推荐方案 B：分阶段渐进改造**

### 理由

1. **平衡工作量与价值**：6 天工作量适中，每阶段交付独立价值
2. **完整覆盖核心痛点**：多语言 CI + 社区健康 + 模板化，全面解决用户问题
3. **符合"学习模板"定位**（ADR-002）：每个功能模块可独立学习
4. **降低风险**：分阶段交付，可中途调整方向
5. **展示最佳实践**：路径过滤、矩阵构建、缓存优化等高级用法

### 不推荐理由

- **方案 A**：过于简单，不解决多语言 Monorepo 的核心痛点
- **方案 C**：过度工程化，SLSA/Ephemeral Runners 超出"学习模板"定位
- **方案 D**：与实际仓库结构不符，无法展示 Monorepo 路径过滤价值

### 关键决策

| 决策 | 选择 | 理由 |
|------|------|------|
| CI 策略 | 每服务独立 workflow + paths-filter | 展示 Monorepo 最佳实践 |
| 模板化 | 标记 Template + init.sh | 提高复用价值 |
| 文档语言 | 中文为主，代码标识符英文 | 符合 ADR-004 |
| 技术栈覆盖 | 全语言（Go/Python/Node/Flutter） | 符合实际仓库结构 |

### 与 ADR 的一致性

- ✅ ADR-001（源仓库不可访问）：已 clone 成功，基于实际结构调整
- ✅ ADR-002（学习模板定位）：方案 B 完全符合
- ⚠️ ADR-003（Python 唯一）：**需更新**——实际仓库是多语言，应覆盖全语言
- ✅ ADR-004（中文文档）：方案 B 符合
- ✅ ADR-005（功能模块化）：方案 B 采用模块化布局

---

## 6. 风险与缓解

| 风险 | 影响 | 概率 | 缓解措施 |
|------|------|------|----------|
| Flutter CI 超时 | 高 | 中 | 使用 `subosito/flutter-action@v2` + 缓存；限制构建目标（仅 analyze，不 build APK） |
| 多 workflow 维护成本 | 中 | 高 | 使用 Reusable Workflows 提取公共逻辑（如 `reusable-lint.yml`） |
| GitHub Actions 分钟超限 | 中 | 低 | 使用 `paths-filter` 减少无关触发；缓存依赖 |
| 用户期望偏差 | 高 | 低 | align 检查点确认方向；分阶段交付可中途调整 |
| 依赖过期导致 CI 失败 | 中 | 中 | 配置 Dependabot 自动更新；定期手动检查 |

---

## 7. 可测试假设

| # | 假设 | 验证方式 | 成功标准 | 阶段 |
|---|------|----------|----------|------|
| H1 | 路径过滤可减少 50%+ 无关 CI 触发 | 对比 paths-filter 开启前后的 workflow 运行次数 | 无关触发 < 50% | Phase 2 |
| H2 | 社区健康文件可提升 Community Profile 到 100% | GitHub Insights → Community Profile 检查 | 评分 100% | Phase 1 |
| H3 | 从 clone 到理解用途 < 5 分钟 | 邀请 3 位开发者测试首次体验 | 平均 < 5 分钟 | Phase 3 |
| H4 | 模板化后可一键启动新项目 | 使用 Template 创建新仓库，运行 init.sh | 无报错，变量替换成功 | Phase 3 |
| H5 | Flutter CI 可在 10 分钟内完成 | 观察 workflow 运行时间 | 平均 < 10 分钟 | Phase 2 |

---

## 8. 下一步

1. **Phase 1（1 天）**：补充社区健康文件 → 验证 H2
2. **Phase 2（3 天）**：创建多语言 CI workflow → 验证 H1, H5
3. **Phase 3（2 天）**：模板化改造 + 学习指南 → 验证 H3, H4

**MVP 范围**：Phase 1 + Phase 2（4 天），Phase 3 作为后续迭代。

---

*本分析由 pm-analyst 生成，基于 01-research.md 和实际仓库结构的交叉验证。*
