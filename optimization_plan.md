# Yudao-Cloud 项目结构优化建议方案

## 1. 核心目标
*   **清晰化模块职责**：将项目按功能类型（网关、框架、业务模块、前端）进行物理隔离。
*   **优化依赖管理**：统一依赖版本，减少模块间的冗余依赖。
*   **提升可维护性**：规范命名，统一目录结构，方便开发者快速定位代码。

## 2. 目录结构重构方案

建议将根目录下的模块进行归类，移动到二级目录下：

### 2.1 目录划分建议
| 目录名 | 说明 | 包含原模块 |
| :--- | :--- | :--- |
| `yudao-gateway` | 网关模块 | `yudao-gateway` |
| `yudao-framework` | 核心框架与 Starter | `yudao-framework` (保持原样) |
| `yudao-module` | 业务模块集合 | `yudao-module-system`, `yudao-module-infra`, `yudao-module-member` 等 |
| `yudao-server` | 单体启动壳工程 | `yudao-server` |
| `yudao-ui` | 前端项目集合 | `yudao-ui` (保持原样) |
| `yudao-dependencies` | 依赖管理 | `yudao-dependencies` |

### 2.2 业务模块内部结构优化
目前业务模块采用 `api` + `server` 的模式，建议保持，但规范内部包名：
*   `yudao-module-xxx-api`：对外暴露的 DTO、Feign 客户端、常量。
*   `yudao-module-xxx-biz`：业务实现逻辑（建议将 `server` 重命名为 `biz`，更符合业务模块的定位）。

## 3. 依赖管理优化
*   **统一 BOM 管理**：所有模块必须继承 `yudao-dependencies` 或在根 `pom.xml` 中引入。
*   **按需引入 Starter**：业务模块只引入必要的 `yudao-spring-boot-starter-xxx`，避免过度引入。
*   **清理冗余依赖**：检查并移除各模块中未使用的 Maven 依赖。

## 4. 实施步骤
1.  **创建二级目录**：创建 `yudao-module` 文件夹。
2.  **移动模块**：将所有 `yudao-module-*` 移动到 `yudao-module/` 下。
3.  **修改 POM 文件**：
    *   更新根目录 `pom.xml` 的 `<modules>` 节点。
    *   更新各业务模块 `pom.xml` 中的 `<parent>` 路径（如果需要）。
    *   更新模块间的相互引用路径。
4.  **重命名模块（可选）**：将 `yudao-module-xxx-server` 重命名为 `yudao-module-xxx-biz`。
5.  **清理与校验**：执行 `mvn clean install` 确保项目编译通过。
