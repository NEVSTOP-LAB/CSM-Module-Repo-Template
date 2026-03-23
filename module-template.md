# `[模块名称]` — CSM 模块接口文档

> **使用说明**
> 1. 复制本文件并将其重命名为你的模块名（例如 `DataLogger.md`）。
> 2. 将所有 `[占位符]` 替换为实际内容。
> 3. 删除不适用的章节。
> 4. 完成后删除本说明块。
> 5. **⚠️ 一模块一文件原则**：每个 CSM 模块必须对应一个独立的 `.md` 文件，禁止将多个模块合并到同一个文件中描述。即使多个模块同属一个 `lvlib`/`lvlibp`，也须分别建立文件；模块所属的模块组信息填写在下方"模块信息"表格的 **所属模块组** 字段中。

---

## 功能简述

`[模块名称]` 是一个 CSM 模块，用于 [一句话描述模块的功能]。

[可选：1～2 句补充说明主要设计目标或所解决的问题。]

---

## 模块信息

| 属性 | 值 |
| --- | --- |
| LabVIEW 版本 | ≥ [版本号，例如 2019] |
| 支持的操作系统 | [Windows / Linux / macOS] |
| 支持 RT | [✅ 支持 / ❌ 不支持] |
| 支持 64-bit | [✅ 支持 / ❌ 不支持] |
| 所属模块组 | [lvlib/lvlibp 名称，若不属于任何模块组则填 N/A] |

---

## 依赖项

| 依赖 | 类型 |
| --- | --- |
| [Communicable-State-Machine](https://github.com/NEVSTOP-LAB/Communicable-State-Machine) | 必须 |
| [CSM-API-String-Arguments-Support](https://github.com/NEVSTOP-LAB/CSM-API-String-Arguments-Support) | 可选 |
| [CSM-MassData-Parameter-Support](https://github.com/NEVSTOP-LAB/CSM-MassData-Parameter-Support) | 可选 |
| [CSM-INI-Static-Variable-Support](https://github.com/NEVSTOP-LAB/CSM-INI-Static-Variable-Support) | 可选 |
| [CSM-Mermaid-Plugin](https://github.com/NEVSTOP-LAB/CSM-Mermaid-Plugin) | 可选 |
| [CSM-TCP-Router-App](https://github.com/NEVSTOP-LAB/CSM-TCP-Router-App) | 可选 |

> 删除不需要的行。

---

## API 接口（消息接口）

以下是外部调用者可以发送给本模块的消息。

### `API: Initialize`

初始化内部资源。必须在其他 API 之前调用。

- **参数**：`APIString` — `String`：配置文件路径
- **响应**：N/A

### `API: Start`

启动模块的主要操作。

- **参数**：N/A
- **响应**：N/A

### `API: Stop`

优雅地停止主要操作。

- **参数**：N/A
- **响应**：N/A

### `API: [示例：传递结构体数据]`

[描述]

- **参数**：`HexStr` — `Cluster`：
  - `[字段名]`：[类型，例如 Boolean]
  - `[字段名]`：[类型，例如 DBL]
- **响应**：N/A

### `API: [示例：传递大块数据]`

[描述]

- **参数**：`MassData` — `Waveform[]`：一维波形数组
- **响应**：N/A

### `API: Get Status`

查询当前状态。

- **参数**：N/A
- **响应**：`APIString` — `String`：状态描述

> 根据需要增减段落。请记录每一个对外可调用的 `API:` 消息。

### 参数类型说明

| 类型 | 说明 | 链接 |
| --- | --- | --- |
| `APIString` | 支持嵌套键值对的纯文本字符串，需要 CSM API String Arguments Support 插件 | [GitHub](https://github.com/NEVSTOP-LAB/CSM-API-String-Arguments-Support) |
| 用户自定义 | 由模块自行解析的字符串，无需额外插件 | — |
| `HexStr` | 将 LabVIEW Variant 序列化为十六进制字符串，内置支持 | — |
| `SafeStr` | 将特殊字符编码为 `%[HEXCODE]`，内置支持 | — |
| `MassData` | 内存映射缓冲区，传递 `Start:N,Size:M`，需要 CSM MassData Parameter Support 插件 | [GitHub](https://github.com/NEVSTOP-LAB/CSM-MassData-Parameter-Support) |

---

## 状态广播接口

以下是本模块**发出**的消息，用于通知订阅者内部状态变化。

### `[状态名称 A]`

**广播类型**：`Status`

[发生了什么 / 哪些数据已就绪]

- **参数**：`HexStr` — `Cluster`：
  - `[字段名]`：[类型，例如 DBL]

### `[状态名称 B]`

**广播类型**：`Interrupt`

[错误或需要立即处理的中断事件描述]

- **参数**：`APIString` — `String`：错误信息

> - 使用 **`Status`** 表示正常的、预期中的状态转换。
> - 使用 **`Interrupt`** 表示需要立即关注的错误或事件。
> - 如果模块不广播任何状态，请删除本章节。

---

## 属性接口

以下是本模块对外暴露的属性，可通过 `CSM - Get Module Attribute.vi` 和 `CSM - Set Module Attribute.vi` 直接读写，无需发送消息。

> - 属性的参数类型为 **LabVIEW 数据类型**，不是 CSM 的参数类型（如 `APIString`、`HexStr` 等）。
> - 推荐只使用简单数据类型（如 `String`、`Boolean`、`DBL`、`I32` 等）作为属性类型。
> - 属性是**可选的**：读取时属性可能不存在（`CSM - Get Module Attribute.vi` 的 `Found` 输出为 FALSE），此时返回指定的默认值。

### `[属性名称 A]`

[属性说明，例如：当前采样速率，单位 Hz]

- **类型**：`[LabVIEW 数据类型，例如 DBL]`
- **访问方式**：[读写 / 只读]
- **默认值**：[默认值，若无则写 N/A]
- **是否必须存在**：[必须 / 可选（不存在时使用默认值）]

### `[属性名称 B]`

[属性说明，例如：是否启用调试模式]

- **类型**：`[LabVIEW 数据类型，例如 Boolean]`
- **访问方式**：[读写 / 只读]
- **默认值**：[默认值，若无则写 N/A]
- **是否必须存在**：[必须 / 可选（不存在时使用默认值）]

> 根据需要增减段落。如果模块没有对外暴露的属性，请删除本章节。

---

## 配置说明

> 推荐使用 [CSM INI Static Variable Support](https://github.com/NEVSTOP-LAB/CSM-INI-Static-Variable-Support) 管理配置参数，通过 `${变量名}` 语法在消息中直接引用 INI 键值。

### 前面板参数（可选）

| 控件名称 | 默认值 | 说明 |
| --- | --- | --- |
| `[控件名称]` | [默认值] | [配置作用] |

### INI 文件配置

```ini
[模块名称]
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
[状态名称 A]@[模块名称] >> API: On[状态名称 A]@[处理模块] -><register>

// 取消订阅
[状态名称 A]@[模块名称] >> API: On[状态名称 A]@[处理模块] -><unregister>
```

---

## 备注

- [任何实现说明、已知限制或开发者需要注意的事项。]

---

*完整 CSM 语法参考：<https://github.com/NEVSTOP-LAB/Communicable-State-Machine/blob/main/.doc/Syntax.md>*  
*CSM Wiki：<https://nevstop-lab.github.io/CSM-Wiki/>*
