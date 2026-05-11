# Contributing

感谢你改进 CSM 模块仓库模板。

## 修改原则

- 保持 `README.md`、`module-template.md` 与 `docs/csm-module-skill.md` 中的术语和规则一致。
- 每个 CSM 模块应有一个与模块 VI 同名的 Markdown 接口文档。
- 接口文档中的 `String` 参数统一标注为 `APIString`，不要直接标注为 `SafeStr`。
- 属性接口使用 LabVIEW 数据类型，不使用 CSM 参数类型。

## 提交前检查

- 确认示例消息语法可读且一致。
- 确认可选章节在不适用时有明确删除提示。
- 确认新增链接有效，且未提交 LabVIEW 生成文件或本地临时文件。
