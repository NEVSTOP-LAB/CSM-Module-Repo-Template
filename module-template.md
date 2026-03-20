# `[模块名称]` — CSM 模块接口文档

> **使用说明**
> 1. 复制本文件并将其重命名为你的模块名（例如 `DataLogger.md`）。
> 2. 将所有 `[占位符]` 替换为实际内容。
> 3. 删除不适用的章节。
> 4. 完成后删除本说明块。

---

## 功能简述

`[模块名称]` 是一个 CSM 模块，用于 [一句话描述模块的功能]。

[可选：1～2 句补充说明主要设计目标或所解决的问题。]

---

## 依赖项

| 依赖 | 类型 | 链接 |
| --- | --- | --- |
| Communicable State Machine (CSM) | 必须 | [GitHub](https://github.com/NEVSTOP-LAB/Communicable-State-Machine) |
| [例如：CSM MassData Parameter Support] | 必须 / 可选 | [GitHub](https://github.com/NEVSTOP-LAB/CSM-MassData-Parameter-Support) |

> 删除不需要的行。

---

## API 接口（消息接口）

以下是外部调用者可以发送给本模块的消息。

| API | 描述 | 参数 | 响应 |
| --- | --- | --- | --- |
| `API: Initialize` | 初始化内部资源。必须在其他 API 之前调用。 | 配置文件路径 `(Plain String)` | N/A |
| `API: Start` | 启动模块的主要操作。 | N/A | N/A |
| `API: Stop` | 优雅地停止主要操作。 | N/A | N/A |
| `API: Update Settings` | 在不重启的情况下更新运行时配置。 | 配置字符串或路径 `(Plain String)` | N/A |
| `API: Get Status` | 同步查询当前状态。 | N/A | 状态描述 `(Plain String)` |

> 根据需要增减行。请记录每一个对外可调用的 `API:` 消息。

### 参数类型说明

| 类型 | 说明 |
| --- | --- |
| `Plain String` | 需要 [CSM API String Arguments](https://github.com/NEVSTOP-LAB/CSM-API-String-Arugments-Support) 插件 |
| `Safe String` | 内置；特殊字符编码为 `%[HEXCODE]` |
| `HexStr` | 内置；数据转换为 Variant 再转十六进制字符串 |
| `MassData` | 插件；内存映射缓冲区，传递 `Start:N,Size:M`；需要 [MassData 插件](https://github.com/NEVSTOP-LAB/CSM-MassData-Parameter-Support) |
| `${变量名}` | 插件；INI 配置变量；需要 [INI 静态变量插件](https://github.com/NEVSTOP-LAB/CSM-INI-Static-Variable-Support) |

---

## 状态广播接口

以下是本模块**发出**的消息，用于通知订阅者内部状态变化。

| 状态 | 广播类型 | 描述 | 参数 |
| --- | --- | --- | --- |
| `[状态名称 A]` | `Status` | [发生了什么 / 哪些数据已就绪] | [类型与格式] |
| `[状态名称 B]` | `Interrupt` | [错误或需要立即处理的中断事件描述] | 错误信息 `(Plain String)` |

> - 使用 **`Status`** 表示正常的、预期中的状态转换。
> - 使用 **`Interrupt`** 表示需要立即关注的错误或事件。
> - 如果模块不广播任何状态，请删除本章节。

---

## 配置说明

### 前面板参数

| 控件名称 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| `[控件名称]` | [LabVIEW 类型] | [默认值] | [配置作用] |

### INI 文件配置

如果模块读取 INI 文件，在此处记录节名和键值。

```ini
[[模块名称]]
[键名]   = [默认值]    ; [说明]
[键名2]  = [默认值]    ; [说明]
```

> 如果模块没有可配置参数，请删除本章节。

---

## 调用限制与注意事项

- [ ] `API: Initialize` **必须**在其他任何 API 之前调用。
- [ ] 本模块为**单例**——同一时间不可运行多个实例。
- [ ] [在此添加其他顺序要求、线程安全说明或生命周期约束。]

---

## 使用示例

> 将 `[模块名称]` 替换为启动模块 VI 时实际使用的名称。

### 基本生命周期

```text
// 初始化并启动模块
API: Initialize >> C:\config\settings.ini -> [模块名称]
API: Start -> [模块名称]

// ... 执行操作 ...

// 停止模块
API: Stop -> [模块名称]
```

### 订阅状态广播

```text
// 将 [模块名称] 的 "[状态名称 A]" 路由到另一个模块的处理 API
[状态名称 A]@[模块名称] >> API:On[状态名称 A]@[处理模块] -><register>

// 取消订阅
[状态名称 A]@[模块名称] >> API:On[状态名称 A]@[处理模块] -><unregister>
```

### 同步调用

```text
// 同步查询当前状态（调用方等待响应）
API: Get Status -@ [模块名称]
```

---

## 模块交互图

```mermaid
stateDiagram-v2
direction LR
[模块名称] --> [消费模块] : "[状态名称 A] >> API:Handler"
[调用模块] --> [模块名称] : "API: Start"
[调用模块] --> [模块名称] : "API: Stop"
```

> 根据实际模块交互关系更新或删除此图。

---

## 备注

- [任何实现说明、已知限制或开发者需要注意的事项。]

---

*完整 CSM 语法参考：<https://github.com/NEVSTOP-LAB/Communicable-State-Machine/blob/main/.doc/Syntax.md>*  
*CSM Wiki：<https://nevstop-lab.github.io/CSM-Wiki/>*
