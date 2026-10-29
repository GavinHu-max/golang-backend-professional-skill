# Professional Go Backend Skill

A reusable Codex skill for Go backend architecture, API contracts, durable request history, observability, and external-service reliability. It preserves each project's chosen stack and authorization boundaries.

## Install in Codex

Clone this repository and copy it to your global skills directory:

```sh
git clone https://github.com/GavinHu-max/golang-backend-professional-skill.git
mkdir -p ~/.codex/skills/golang-backend-professional
cp -R golang-backend-professional-skill/SKILL.md golang-backend-professional-skill/agents golang-backend-professional-skill/references ~/.codex/skills/golang-backend-professional/
```

If you use a custom `CODEX_HOME`, use its `skills/golang-backend-professional` directory instead. Start a new Codex session after installation. Ask for `$golang-backend-professional` explicitly, or let Codex select it for a substantial Go backend task.

The entry point is [SKILL.md](SKILL.md). The references describe API contracts, durable history, failure handling, and selected public design sources. They are original guidance informed by official source code and documentation, not copied framework implementations. See [reference patterns](references/reference-patterns.md) for links and limitations.
