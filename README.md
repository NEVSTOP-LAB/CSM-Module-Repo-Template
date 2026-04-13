# CSM-Module-Repo-Template

基于 [CSM（可通信状态机）](https://nevstop-lab.github.io/CSM-Wiki/) 框架的模块仓库模板，用于参考如何编写 CSM 模块接口文档并完成模块开发。

## 创建 CSM 模块

### 设计要点

- **单一职责**：一个模块只负责一件事（如采集、存储、通信）。
- **接口清晰**：用 `API:` 前缀标识对外接口，名称语义化（如 `API: Start`、`API: LoadConfig`）。
- **模块解耦**：通过广播/订阅机制传递状态，避免在代码中硬编码其他模块名称。
- **错误处理**：实现 `Error Handler` 状态，出错时发出 `Error Occurred` 广播通知外部。

### 接口约定

| 接口类型 | 说明 |
| --- | --- |
| **消息接口<br/>API** | 以 `API:` 为前缀的 case 分支，或其他对外公开的非内置 case 分支 |
| **广播接口<br/>Broadcast** | 模块内部状态变化时发出的 `Status`（普通）或 `Interrupt`（高优先级）广播 |
| **属性接口<br/>Attribute** | 可通过 `CSM - Get/Set Module Attribute.vi` 直接读写；使用 LabVIEW 原生数据类型（`String`、`Boolean`、`DBL` 等），而非 CSM 参数类型 |

### 参数传递

CSM 消息接口只支持字符串参数，复杂数据需编码：

| 类型 | 说明 |
| --- | --- |
| `APIString` | 纯文本字符串，支持键值对；可使用 [API String Arguments](https://github.com/NEVSTOP-LAB/CSM-API-String-Arguments-Support) 插件 |
| `SafeStr` | 含特殊字符的字符串，编码为 `%[HEXCODE]` |
| `HexStr` | 任意 LabVIEW 数据序列化为十六进制字符串 |
| `MassData` | 大数组/波形，通过内存映射缓冲区高效传递；需要 [MassData 插件](https://github.com/NEVSTOP-LAB/CSM-MassData-Parameter-Support) |
| `用户自定义` | 自定义字符串格式；多数场景下 `APIString` 可满足需求 |

### 使用本模板

1. 点击 **"Use this template"** 创建模块仓库，并以模块名命名（如 `CSM-DataLogger`）。
2. 复制 [`module-template.md`](./module-template.md)，将其重命名为与模块 VI 同名的文件（如 `DataLogger.md`）；每个模块 VI 都应有一份对应的接口文档。
3. 按模板说明替换占位符，填写接口文档。

更多详情请参阅 [CSM Wiki](https://nevstop-lab.github.io/CSM-Wiki/)。

### 文档结构设计

模块文档采用**文件头优先（Header-First）**设计：

```
# 模块名称 — CSM 模块接口文档   ← 文件头开始
                                       （功能描述 + 模块信息 + 依赖项 + 接口一览）
---                                ← 文件头结束
## API 接口（详细说明）            ← 详细章节
## 状态广播接口
...
```

- **文件头**（标题 + 摘要引用块，直到第一个 `---` 分隔线）：包含模块功能简述、元数据、依赖项和所有接口名称一览。AI 助手只需读取文件头即可了解模块的完整接口概况，无需加载整个文档。
- **详细章节**（`---` 之后）：提供每个 API、广播和属性的完整参数描述、使用示例等详细说明，供深度查阅。

> AI 技能规范详见 [`docs/csm-module-skill.md`](./docs/csm-module-skill.md)。
