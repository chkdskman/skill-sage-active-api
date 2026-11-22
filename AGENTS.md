# AGENTS.md

Repositorio público con la skill/plugin de la Sage Active Public API V2. Lee `README.md` y `skills/` antes de cambiar nada.

## Umpa Factory (shared project index)

This repo belongs to Umpa Lumpa Technologies. The shared index lives in `../Umpa Factory` ([chkdskman/umpa-factory](https://github.com/chkdskman/umpa-factory)) and its rules (`../Umpa Factory/AGENTS.md`) apply here too:

- **When you start:**
  - read `../Umpa Factory/projects/skill-sage-active-api.md`;
  - check who else is working with `uf status`;
  - register your session with `uf start --task "…"`. With the Claude Code hooks the session is already registered: just set the task with `uf task`.
- **If it answers *Bloqueado* (locked):** do not use `--force`. Work on another branch or in your own worktree, or ask the user.
- **When you finish:** `uf end --summary "…"`.
- **If this project's** stage, distribution, Firebase projects or entry docs change, update `../Umpa Factory/projects/skill-sage-active-api.json`.
