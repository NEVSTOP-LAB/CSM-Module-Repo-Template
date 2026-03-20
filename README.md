# CSM-Module-Repo-Template

基于 [CSM（可通信状态机）](https://nevstop-lab.github.io/CSM-Wiki/) 框架的模块仓库模板。使用本模板可快速搭建一个符合规范的 CSM 可复用模块仓库。

## 创建 CSM 模块

一个可复用的 CSM 模块需要做好两件事：**提供外部接口（API）** 和 **发布状态变化（Status / Interrupt）**。

### 设计要点

- **单一职责**：一个模块只负责一件事（如采集、存储、通信）。
- **接口清晰**：用 `API:` 前缀标识对外接口，名称语义化（如 `API: Start`、`API: LoadConfig`）。
- **模块解耦**：不在代码中硬编码其他模块名称；通过广播/订阅机制解耦模块间依赖。
- **错误处理**：实现 `Error Handler` 状态，出错时发出 `Error Occurred` 广播通知外部。

### 接口约定

| 接口类型 | 说明 |
| --- | --- |
| **消息接口（API）** | 以 `API:` 为前缀的 case 分支，或其他对外公开的非内置 case 分支 |
| **广播接口（Status）** | 模块内部状态变化时发出的 `Status`（普通）或 `Interrupt`（高优先级）广播 |

### 参数传递

CSM 只支持字符串参数，复杂数据需编码：

| 类型 | 说明 |
| --- | --- |
| 纯字符串 / `APIString` | 直接传递，或使用 [API String Arguments](https://github.com/NEVSTOP-LAB/CSM-API-String-Arguments-Support) 插件支持键值对 |
| `SafeStr` | 含特殊字符的字符串，编码为 `%[HEXCODE]` |
| `HexStr` | 任意 LabVIEW 数据序列化为十六进制字符串 |
| `MassData` | 大数组/波形，通过内存映射缓冲区高效传递；需要 [MassData 插件](https://github.com/NEVSTOP-LAB/CSM-MassData-Parameter-Support) |

### 使用本模板

1. 点击 **"Use this template"** 创建你的模块仓库，并以模块名命名（如 `CSM-DataLogger`）。
2. 仓库中每个 CSM 模块 VI 对应一个同名 `.md` 文件（如 `DataLogger.md`）。
3. 以 [`module-template.md`](./module-template.md) 为起点填写模块接口文档。

更多详情请参阅 [CSM Wiki](https://nevstop-lab.github.io/CSM-Wiki/) 和 [README(CN).md](./README(CN).md)。