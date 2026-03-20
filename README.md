# CSM-Module-Repo-Template

[English](./README.md) | [中文](./README(CN).md)

This is a **template repository** for building reusable [CSM (Communicable State Machine)](https://nevstop-lab.github.io/CSM-Wiki/) modules. It provides guidelines, file templates, and documentation standards so that every CSM module can be clearly described, easily integrated, and AI-friendly.

## What is CSM?

**Communicable State Machine (CSM)** is a LabVIEW application framework built on [JKI State Machine (JKISM)](http://jki.net/state-machine/). It extends JKISM with inter-module message communication mechanisms, including:

| Mechanism | Syntax | Description |
| --- | --- | --- |
| Async call | `API: Xxx >> args -> TargetModule` | Fire-and-forget, no wait |
| Async call (no reply) | `API: Xxx >> args ->| TargetModule` | Explicitly suppress any response |
| Sync call | `API: Xxx >> args -@ TargetModule` | Caller blocks until response arrives |
| Status broadcast | `Status >> args -><status>` | Notify all subscribers of a normal state change |
| Interrupt broadcast | `Interrupt >> args -><interrupt>` | Notify all subscribers of an interrupt-level event |
| Subscribe | `Status@Src >> API:Handler@Dst -><register>` | Route Src's status into Dst's handler API |
| Unsubscribe | `Status@Src >> API:Handler@Dst -><unregister>` | Remove the subscription |

📖 Full documentation: <https://nevstop-lab.github.io/CSM-Wiki/>

## Repository Structure

```text
.
├── README.md                  # Project overview (English)  ← you are here
├── README(CN).md              # Project overview (Chinese)
├── module-template.md         # Template for CSM module interface documentation
└── docs/
    └── csm-module-skill.md    # AI skill guide: rules for writing/reading module docs
```

## How to Use This Template Repo

1. Click **"Use this template"** on GitHub to create your module repository.
2. Name the repository after your module (e.g., `CSM-DataLogger`).
3. For **every CSM module** in the repository, create a **same-name `.md` file** that describes its interface (e.g., a VI module named `DataLogger` → `DataLogger.md`).
4. Use [`module-template.md`](./module-template.md) as the starting point for each module doc.
5. Update `README.md` with project-level information (badges, overview, links).

---

## Writing a CSM Module Interface Document

Each module's `.md` file must cover the following sections:

### 1. Brief Description

A concise (1–3 sentence) summary of what the module does and its main responsibility.

### 2. Dependencies

List any required CSM add-ons or external libraries (e.g., MassData support, INI Static Variable support).

### 3. API Interface (Message Interface)

All `API:` messages the module **accepts** from the outside world.

| API | Description | Arguments | Response |
| --- | --- | --- | --- |
| `API: Initialize` | Initialize the module | Configuration path `(Plain String)` | N/A |
| `API: Start` | Begin the main task | N/A | N/A |
| `API: Stop` | Stop gracefully | N/A | N/A |

> **Argument types**: Use `Plain String`, `HexStr`, `MassData`, `Safe String`, or `${variable}` (INI Static Variable) as appropriate.

### 4. Status Broadcast Interface

All **status** or **interrupt** messages the module **emits** (its observable outputs).

| Status | Type | Description | Arguments |
| --- | --- | --- | --- |
| `Data Ready` | Status | New data is available | 1D Waveform array `(MassData)` |
| `Error` | Interrupt | An unrecoverable error occurred | Error description `(Plain String)` |

### 5. Configuration

Describe front-panel controls, INI file keys, or any static configuration the module reads at startup.

```ini
[DataLogger]
OutputFolder = C:\Data
MaxFileSizeMB = 100
```

### 6. Usage Constraints

Document initialization order, thread-safety notes, singleton restrictions, or any other limitations callers must observe.

### 7. Usage Examples

Provide concrete message-string snippets.

```text
// Initialize and start
API: Initialize >> C:\config\mymodule.ini -> DataLogger
API: Start -> DataLogger

// Subscribe to output
Data Ready@DataLogger >> Process:OnData@Processor -><register>

// Stop and clean up
API: Stop -> DataLogger
```

---

## CSM Message Syntax Quick Reference

```text
// Async call
API: Start -> TargetModule

// Async call with arguments
API: Configure >> argument -> TargetModule

// Async call, no reply expected
API: Log >> data ->| TargetModule

// Sync call (caller waits for response)
API: GetValue -@ TargetModule

// Broadcast normal status
Status >> data -><status>

// Broadcast interrupt status
Error >> details -><interrupt>

// Subscribe: route SourceModule's status into HandlerModule's API
Status@SourceModule >> API:Handler@HandlerModule -><register>

// Unsubscribe
Status@SourceModule >> API:Handler@HandlerModule -><unregister>
```

Full syntax reference: <https://github.com/NEVSTOP-LAB/Communicable-State-Machine/blob/main/.doc/Syntax.md>

---

## Parameter Types Reference

| Type | Built-in? | Description |
| --- | --- | --- |
| `Plain String` | ✅ | Use [API String Arguments](https://github.com/NEVSTOP-LAB/CSM-API-String-Arugments-Support) add-on |
| `Safe String` | ✅ | Special characters encoded as `%[HEXCODE]` |
| `HexStr` | ✅ | Data serialized to Variant then hex string |
| `MassData` | Add-on | Memory-mapped buffer; pass `Start:N,Size:M` as parameter |
| `${variable}` | Add-on | INI-backed static variable via [INI Static Variable](https://github.com/NEVSTOP-LAB/CSM-INI-Static-Variable-Support) |

---

## Resources

- 📖 [CSM Wiki](https://nevstop-lab.github.io/CSM-Wiki/)
- 🔧 [CSM Core Framework](https://github.com/NEVSTOP-LAB/Communicable-State-Machine)
- 📦 [CSM on VIPM](https://www.vipm.io/package/nevstop_lib_communicable_state_machine/)
- 💡 [CSM Continuous Measurement Example](https://github.com/NEVSTOP-LAB/CSM-Continuous-Meausrement-and-Logging)
- 📄 [Module Documentation Template](./module-template.md)
- 🤖 [AI Skill Guide](./docs/csm-module-skill.md)