# Yudao-Cloud 项目结构优化详细方案

## 1. 物理结构重构（目录归类）

为了解决根目录模块过多的问题，建议引入二级目录进行归类：

### 1.1 目录结构调整
*   `yudao-gateway` -> `yudao-gateway` (保持，或移入 `yudao-infra`)
*   `yudao-framework` -> `yudao-framework` (保持)
*   `yudao-server` -> `yudao-server` (保持)
*   `yudao-dependencies` -> `yudao-dependencies` (保持)
*   **[新增]** `yudao-module`：统一存放所有业务模块。
    *   `yudao-module-system` -> `yudao-module/yudao-module-system`
    *   `yudao-module-infra` -> `yudao-module/yudao-module-infra`
    *   `yudao-module-member` -> `yudao-module/yudao-module-member`
    *   ... 其他 `yudao-module-*`
*   **[新增]** `yudao-visual`：存放监控、代码生成等可视化组件（可选）。

## 2. 模块命名与职责规范

### 2.1 模块重命名
将 `yudao-module-xxx-server` 统一重命名为 `yudao-module-xxx-biz`。
*   理由：`server` 容易产生误解（以为是独立运行的服务），而 `biz` (Business) 更准确地描述了它是业务逻辑实现层。

### 2.2 内部包结构规范
目前内部包结构已经非常清晰（`api`, `controller`, `service`, `dal`, `convert`, `mq`），建议保持并强制执行。

## 3. 依赖关系优化

### 3.1 核心 Starter 抽象
*   检查 `yudao-framework` 下的 Starter，确保没有循环依赖。
*   建议将常用的第三方库版本（如 `lombok`, `mapstruct`）完全托管在 `yudao-dependencies` 中。

### 3.2 减少业务模块冗余
*   业务模块的 `biz` 层应通过 `yudao-dependencies` 引入版本，避免在各模块 `pom.xml` 中重复定义版本号。

## 4. 前端项目整理
*   将 `yudao-ui` 下的多个项目（vue2, vue3, vben, uniapp）保持现状，但可以在 README 中明确各项目的维护状态。

## 5. 实施路线图

1.  **第一阶段：目录迁移**
    *   创建 `yudao-module` 目录。
    *   移动所有业务模块文件夹。
    *   修改根 `pom.xml` 的 `<modules>` 配置。
2.  **第二阶段：POM 调整**
    *   修改各模块 `pom.xml` 中的 `<parent>` 相对路径。
    *   修改模块间依赖的 `artifactId`（如果进行了重命名）。
3.  **第三阶段：包名与代码调整**
    *   如果有重命名，需要全局替换相关的 `import`。
4.  **第四阶段：编译校验**
    *   运行 `mvn clean install -DskipTests`。
