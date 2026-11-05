# contextium

External multi-repo context management for AI agents: `ctxium` Rust CLI
(hooks + hookserver), SessionStart sub-agents, and Scripts-to-Rule-Them-All
installers for companion tools. Product intent: [context/purpose.md](context/purpose.md);
agent details: [agents/README.md](agents/README.md).

## Layout

- `ctxium/` — Rust workspace (clap CLI: `hook`, `hookserver`)
- `agents/` — SessionStart shell agents (`initializer`, `task-determiner`,
  `context-fetcher`) + prompts; driven by `agents/run-startup.sh`
- `script/` — `bootstrap` / `setup` / `update` / `install/<tool>`
- `context/` — purpose + project notes (not runtime state)
- `.claude/` — settings.json hooks and skills (SessionStart installs tools,
  runs startup agents, and calls `ctxium hook …`)

## Required

- Session start runs `script/bootstrap` in the background (worktrunk, memvid,
  gibram, adversarial-spec) and `agents/run-startup.sh`. Do not block on
  bootstrap; logs land in `/tmp/contextium-bootstrap.log` and
  `/tmp/contextium-startup.log`.
- Runtime state lives outside the repo in `.contextium/` (`state.json`,
  `tasks.json`, `context-cache/`) and `~/.contextium/hooks.log` — never commit
  it.
- Do not use the `AskUserQuestion` tool (broken on Claude Code iOS); ask
  questions in plain response text.
- Hooks in `.claude/settings.json` forward every lifecycle event to
  `ctxium hook <Event>`; the hookserver default port is `6160`.
- Pre-commit (pre-push): typos, mdformat, `cargo fmt`, `cargo clippy -D
  warnings` on `ctxium/Cargo.toml`. CI mirrors that (fmt/clippy/test/release
  build).

```bash
cargo test --manifest-path ctxium/Cargo.toml
cargo clippy --manifest-path ctxium/Cargo.toml --all-targets --all-features -- -D warnings
cargo fmt --manifest-path ctxium/Cargo.toml --
script/bootstrap          # install companion tools
```

## Key libraries

clap, tokio, reqwest, axum, serde_json (see `ctxium/Cargo.toml`).

## Maintaining this index

- Update the affected `AGENTS.md` files in the same change when paths,
  responsibilities, commands, dependencies, or conventions change.
- Keep indexes brief: record semantic entry points and non-obvious
  constraints; link to existing documentation instead of duplicating it.
- Add a directory index only when it provides useful navigation beyond its
  parent; omit generated, vendored, and fixture trees.
- Do not add `CLAUDE.md` files; Claude Code reads `AGENTS.md` natively, and
  a `CLAUDE.md` anywhere in the tree stops every `AGENTS.md` from loading.
