# Prebaked plugin reference

This is a **rendered, nonfunctional reference** for the Energon agent plugin in this starter. It was generated with the source patch's own render script, using reserved `.example` hosts and a placeholder environment-variable name. It is intentionally nested under `examples/` and is not a marketplace entry or a connection to any server. Do not install it as an operational plugin, point it at an existing service, or supply real credentials to it.

- [Example plugin package](plugins/example-energon/)
- [Rendered skill](plugins/example-energon/skills/example-energon/SKILL.md)
- [Rendered API reference](plugins/example-energon/skills/example-energon/references/api.md)
- [Example renderer inputs](instance-skill.example.json)

The skill contains the standard feedback workflows: publish public HTML, ensure a review, look up its room and read feedback, or start a new review when authorized. The examples are illustrative only. A real plugin must be rendered inside your own approved deployment checkout with your own instance name, hub origin, repository and token environment name. Follow the [main README](../../README.md#install-the-agent-skill) and your patched `INSTALL.md`; do not copy the placeholder identity into production.
