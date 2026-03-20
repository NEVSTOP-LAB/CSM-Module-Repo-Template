# CSM 模块仓库模板

[English](./README.md) | [中文](./README(CN).md)

这是一个用于构建可复用 [CSM（可通信状态机）](https://nevstop-lab.github.io/CSM-Wiki/) 模块的**模板仓库**。它提供规范、文件模板和文档标准，使每个 CSM 模块都能被清晰描述、轻松集成，并且对 AI 友好。

## 什么是 CSM？

**可通信状态机（Communicable State Machine，CSM）** 是一个基于 [JKI 状态机（JKISM）](http://jki.net/state-machine/) 的 LabVIEW 应用框架。它扩展了 JKISM，增加了模块间消息通信机制，包括：

| 机制 | 语法 | 说明 |
| --- | --- | --- |
| 异步调用 | `API: Xxx >> 参数 -> 目标模块` | 发出后不等待响应 |
| 无应答异步调用 | `API: Xxx >> 参数 ->| 目标模块` | 明确抑制任何响应 |
| 同步调用 | `API: Xxx >> 参数 -@ 目标模块` | 调用方阻塞直到收到响应 |
| 广播正常状态 | `Status >> 参数 -><status>` | 通知所有订阅者当前状态变化 |
| 广播中断状态 | `Interrupt >> 参数 -><interrupt>` | 通知所有订阅者发生中断级事件 |
| 订阅状态 | `Status@源模块 >> API:Handler@目标模块 -><register>` | 将源模块的状态路由到目标模块的处理 API |
| 取消订阅 | `Status@源模块 >> API:Handler@目标模块 -><unregister>` | 移除订阅关系 |

📖 完整文档：<https://nevstop-lab.github.io/CSM-Wiki/>

## 仓库结构

```text
.
├── README.md                  # 项目概述（英文）
├── README(CN).md              # 项目概述（中文）  ← 当前文件
├── module-template.md         # CSM 模块接口文档模板
└── docs/
    └── csm-module-skill.md    # AI 技能文档：编写/解读模块文档的规范
```

## 如何使用本模板仓库

1. 在 GitHub 上点击 **"Use this template"** 创建你的模块仓库。
2. 将仓库命名为你的模块名（例如 `CSM-DataLogger`）。
3. 仓库中的**每一个 CSM 模块**，都需要创建一个**同名的 `.md` 文件**描述其接口（例如，VI 模块 `DataLogger` → `DataLogger.md`）。
4. 以 [`module-template.md`](./module-template.md) 为起点填写每个模块的文档。
5. 用项目级别的信息（徽章、概述、链接）更新 `README.md`。

---

## 编写 CSM 模块接口文档

每个模块的 `.md` 文件必须涵盖以下内容：

### 1. 功能简述

简洁地（1～3 句）说明模块的功能和主要职责。

### 2. 依赖项

列出所需的 CSM 插件或外部库（例如 MassData 支持、INI 静态变量支持等）。

### 3. API 接口（消息接口）

模块**接受**的所有 `API:` 消息（外部可调用接口）。

| API | 描述 | 参数 | 响应 |
| --- | --- | --- | --- |
| `API: Initialize` | 初始化模块 | 配置文件路径 `(Plain String)` | N/A |
| `API: Start` | 启动主任务 | N/A | N/A |
| `API: Stop` | 优雅停止 | N/A | N/A |

> **参数类型**：根据情况选用 `Plain String`、`HexStr`、`MassData`、`Safe String` 或 `${变量}`（INI 静态变量）。

### 4. 状态广播接口

模块**发出**的所有状态/中断消息（其可观察的输出）。

| 状态 | 类型 | 描述 | 参数 |
| --- | --- | --- | --- |
| `Data Ready` | Status | 新数据已就绪 | 一维波形数组 `(MassData)` |
| `Error` | Interrupt | 发生不可恢复错误 | 错误描述 `(Plain String)` |

### 5. 配置说明

描述前面板控件、INI 文件键值，或模块在启动时读取的任何静态配置。

```ini
[DataLogger]
OutputFolder = C:\Data
MaxFileSizeMB = 100
```

### 6. 调用限制与注意事项

记录初始化顺序、线程安全要求、单例限制或调用方必须遵守的其他约束。

### 7. 使用示例

提供典型的调用脚本片段，涵盖完整生命周期。

```text
// =============================================
// 典型调用脚本示例（以 DataLogger 模块为例）
// =============================================

// 1. 订阅模块广播（在启动前注册，确保不遗漏事件）
Data Ready@DataLogger >> API: On Data Ready@Processor -><register>
Error Occurred@DataLogger >> API: On Error@MainApp -><register as interrupt>

// 2. 初始化并启动模块
API: Initialize >> C:\config\datalogger.ini -> DataLogger
API: Start -> DataLogger

// 3. 运行时调用
API: Update Settings >> OutputFolder:<SAFESTR>C:\NewData -> DataLogger

// 4. 停止并清理
API: Stop -> DataLogger

// 5. 取消订阅（可选，模块退出时框架会自动清理内部订阅）
Data Ready@DataLogger >> API: On Data Ready@Processor -><unregister>
```

---

## 参数类型参考

| 类型 | 内置？ | 说明 |
| --- | --- | --- |
| `Plain String` | ✅ | 使用 [API String Arguments](https://github.com/NEVSTOP-LAB/CSM-API-String-Arugments-Support) 插件 |
| `Safe String` | ✅ | 特殊字符编码为 `%[HEXCODE]` |
| `HexStr` | ✅ | 数据序列化为 Variant 再转十六进制字符串 |
| `MassData` | 插件 | 内存映射缓冲区；传递 `Start:N,Size:M` 作为参数 |
| `${变量}` | 插件 | 通过 [INI 静态变量](https://github.com/NEVSTOP-LAB/CSM-INI-Static-Variable-Support) 支持 INI 配置变量 |

---

## 相关资源

- 📖 [CSM Wiki](https://nevstop-lab.github.io/CSM-Wiki/)
- 🔧 [CSM 核心框架](https://github.com/NEVSTOP-LAB/Communicable-State-Machine)
- 📦 [VIPM 上的 CSM](https://www.vipm.io/package/nevstop_lib_communicable_state_machine/)
- 💡 [CSM 连续测量示例](https://github.com/NEVSTOP-LAB/CSM-Continuous-Meausrement-and-Logging)
- 📄 [模块文档模板](./module-template.md)
- 🤖 [AI 技能文档](./docs/csm-module-skill.md)
