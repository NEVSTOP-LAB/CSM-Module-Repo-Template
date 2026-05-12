# Copilot instructions

This repository is a template for CSM (Communicable State Machine) LabVIEW module documentation.

When generating or reviewing CSM module interface documents:

- Follow `module-template.md` as the starting structure.
- Use `.github/copilot/skills/csm-module-docs/SKILL.md` as the detailed rule set.
- Keep one Markdown document per CSM module VI.
- Document public `API:` messages, status/interrupt broadcasts, attributes, configuration, limitations, examples, and optional interaction diagrams.
- Mark `String` parameters as `APIString`; do not directly expose `SafeStr` in interface documentation.
- Use LabVIEW data types for attributes, not CSM parameter types.
- Prefer `csm` fenced code blocks for message examples.
