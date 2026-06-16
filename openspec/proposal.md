# Proposal: demo-repository — GitHub 最佳实践示范仓库深度优化

## Why

`amnRideAI/demo-repository` 是一个多语言 Monorepo 网约车平台示范仓库（Go + Python + Flutter + React + Node），但在 GitHub 最佳实践方面存在严重缺失：

- **CI/CD 覆盖率极低**：仅 2 个 workflow（auto-assign + proof-html），无 lint/test/build
- **社区健康文件缺失**：仅 4 行 README，无 LICENSE/CONTRIBUTING/CODE_OF_CONDUCT/SECURITY
- **多语言支持为零**：未区分服务独立构建，无路径过滤
- **不可复用**：未标记为 Template Repository，无初始化脚本

开发者无法从该仓库学到 GitHub Actions 的实战用法（路径过滤、矩阵构建、缓存优化），开源维护者无法参考标准化的贡献流程。

## What changes

### Phase 1 — 社区健康文件（1 天）

**新增文件**：
- `README.md`：项目概述、架构图、快速开始、服务清单
- `LICENSE`：MIT 许可证
- `CONTRIBUTING.md`：分支策略、代码规范、PR 流程、代码审查标准
- `CODE_OF_CONDUCT.md`：Contributor Covenant v2.1
- `SECURITY.md`：漏洞报告流程、维护版本策略
- `.github/CODEOWNERS`：各服务目录的审查负责人
- `.github/ISSUE_TEMPLATE/bug_report.md`：Bug 报告模板
- `.github/ISSUE_TEMPLATE/feature_request.md`：功能请求模板
- `.github/PULL_REQUEST_TEMPLATE.md`：PR 描述模板

**验证**：GitHub Community Profile 评分达到 100%

### Phase 2 — 多语言 CI/CD（3 天）

**新增 workflow**：
- `.github/workflows/ci-backend.yml`：Go lint + test + build（路径过滤 `src/ride-hermes/**`）
- `.github/workflows/ci-ai-service.yml`：Python ruff + pytest + build（路径过滤 `src/ai-service/**`）
- `.github/workflows/ci-admin-web.yml`：Node eslint + jest + build（路径过滤 `src/admin-web/**`）
- `.github/workflows/ci-mobile.yml`：Flutter analyze + build（路径过滤 `src/driver-app/**` + `src/passenger-app/**`）
- `.github/workflows/ci-mcp.yml`：Node eslint + test（路径过滤 `src/ride-hermes-mcp/**`）
- `.github/workflows/docker-build.yml`：main push 时构建全服务 Docker 镜像

**关键技术**：
- `dorny/paths-filter@v3`：路径过滤，仅构建变更的服务
- `actions/cache@v4`：依赖缓存（Go modules / pip / npm / pub）
- `actions/setup-go@v5` / `setup-python@v5` / `setup-node@v4` / `subosito/flutter-action@v2`
- Reusable Workflows：提取公共 lint 逻辑到 `.github/workflows/reusable-lint.yml`

**验证**：
- 每个 workflow 在 PR 时正确触发（仅针对对应服务变更）
- 无关变更不触发全量构建（paths-filter 生效）
- 依赖缓存命中率 > 80%

### Phase 3 — 模板化改造（2 天）

**新增文件**：
- `scripts/init.sh`：替换项目名称、作者、描述等变量
- `docs/LEARNING_GUIDE.md`：每个 GitHub 功能的学习路径
- `.github/workflows/stale.yml`：自动关闭过期 issue（可选）

**改造**：
- 标记仓库为 Template Repository（Settings → Template repository）
- 清理示例内容，添加 `TODO:` 标记自定义点
- README 增加"使用此模板"章节

**验证**：
- 使用 Template 创建新仓库，运行 `init.sh` 无报错
- 从 clone 到理解用途 < 5 分钟（邀请 3 位开发者测试）

## Impact

### 正面影响

1. **GitHub Community Profile 100%**：仓库可信度大幅提升
2. **多语言 CI/CD 参考实现**：展示 Monorepo 路径过滤、矩阵构建、缓存优化的最佳实践
3. **可复用模板**：一键 Template + init.sh，快速启动新项目
4. **学习资源**：每个 GitHub 功能有独立示例和文档，降低学习曲线

### 负面影响

1. **维护成本**：5+ 个 workflow 文件需要同步更新
2. **Actions 分钟消耗**：多 workflow 可能增加 CI 运行时间（缓解：paths-filter 减少无关触发）
3. **Flutter CI 复杂性**：移动端构建环境配置可能超时（缓解：仅 analyze，不 build APK）

### 风险

| 风险 | 缓解措施 |
|------|----------|
| Flutter CI 超时 | 使用 `subosito/flutter-action@v2` + 缓存；限制构建目标 |
| 多 workflow 维护成本 | 使用 Reusable Workflows 提取公共逻辑 |
| GitHub Actions 分钟超限 | paths-filter 减少无关触发；缓存依赖 |
| 依赖过期导致 CI 失败 | 配置 Dependabot 自动更新 |

## Acceptance scenarios

### Scenario 1: 开发者首次 clone 仓库

**Given** 开发者执行 `git clone https://github.com/amnRideAI/demo-repository`  
**When** 打开 README.md  
**Then** 5 分钟内理解仓库用途、架构、各服务职责

### Scenario 2: 提交 PR 触发 CI

**Given** 开发者修改 `src/ai-service/main.py` 并提交 PR  
**When** GitHub Actions 触发  
**Then** 仅 `ci-ai-service.yml` 运行（Python lint + test），其他 workflow 不触发

### Scenario 3: 使用模板创建新项目

**Given** 用户点击 "Use this template" 创建新仓库  
**When** 运行 `bash scripts/init.sh`  
**Then** 项目名称、作者、描述等变量替换成功，无报错

### Scenario 4: Community Profile 评分

**Given** 仓库管理员访问 GitHub Insights → Community Profile  
**When** 检查清单运行  
**Then** 评分 100%（README + LICENSE + CONTRIBUTING + CODE_OF_CONDUCT + SECURITY + PR template + Issue templates）

## 成功指标

| 指标 | 目标 | 验证方式 |
|------|------|----------|
| Community Profile 评分 | 100% | GitHub Insights |
| CI workflow 数量 | 6 个（5 服务 + 1 Docker） | `.github/workflows/` 目录 |
| paths-filter 减少无关触发 | > 50% | 对比开启前后的 workflow 运行次数 |
| 首次体验时间 | < 5 分钟 | 邀请 3 位开发者测试 |
| 模板初始化成功率 | 100% | 运行 init.sh 无报错 |

## 非目标

- 不构建 SaaS / Web 应用
- 不涉及商业变现
- 不做移动端适配
- 不替代 GitHub 官方文档
- 不实现 SLSA 签名 / Ephemeral Runners（超出"学习模板"定位）

## 依赖

- GitHub Actions 免费账户每月 2000 分钟（需监控）
- Docker Hub 镜像拉取频率限制（需配置 mirror 或缓存）
- Flutter SDK 下载速度（需缓存）

## 时间线

| 阶段 | 工作量 | 交付物 |
|------|--------|--------|
| Phase 1 | 1 天 | 社区健康文件 + Community Profile 100% |
| Phase 2 | 3 天 | 6 个 CI workflow + paths-filter + 缓存 |
| Phase 3 | 2 天 | 模板化 + init.sh + 学习指南 |
| **总计** | **6 天** | **完整的 GitHub 最佳实践示范仓库** |

**MVP 范围**：Phase 1 + Phase 2（4 天），Phase 3 作为后续迭代。
