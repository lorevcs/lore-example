# lore-example

A tiny demonstration of [lore](https://github.com/lorevcs/lore) — version control for
**intent, not code**.

This repository contains **no website**. It contains the *intent* that produces one.
The actual site lives in `code/`, which is deliberately `.gitignore`d: in lore, code is
build output and the intent is the source. You regenerate the code by *materializing* the
intent and handing it to a coding agent.

## What it builds

Four commits of intent, each a phase in the life of a little page with three balls in a
horizontal row:

| Phase | Intent | Result (left → right) |
|-------|--------|------------------------|
| 1 | Three balls in a row, all blue | 🔵 🔵 🔵 |
| 2 | Make the rightmost ball red | 🔵 🔵 🔴 |
| 3 | Swap the rightmost and leftmost balls | 🔴 🔵 🔵 |
| 4 | Make the rightmost ball green | 🔴 🔵 🟢 |

The final materialized state is **🔴 🔵 🟢**. Run `lore log` to see the four phases.

## Use it

**1. Install lore**

```sh
curl -fsSL https://lorevcs.com/install.sh | sh
```

(or, with Rust already installed: `cargo install --git https://github.com/lorevcs/lore`)

**2. Materialize the intent and pass it into Claude**

```sh
lore materialize | claude
```

`lore materialize` prints the brief — the complete, chronological record of every prompt
and decision in this repo. Piping it into [`claude`](https://github.com/anthropics/claude-code)
(Claude Code) lets the agent reconcile the working tree to match the brief: it writes the
website to `code/index.html`. Open that file in a browser and you should see **🔴 🔵 🟢**.

> Prefer a single argument? `claude "$(lore materialize)"` does the same thing.

## Explore the history

Because each phase is its own commit, you can materialize any point in time and rebuild
just that version of the page:

```sh
lore log                                  # list the four phases, newest first
lore materialize --ref <commit>           # print the brief as of that phase
lore materialize --ref <commit> | claude  # rebuild that phase's page
```

For example, materializing the Phase 2 commit yields 🔵 🔵 🔴.

## How it works

- `.lore/` holds the committed intent (prompts, notes, decisions). **This** is what's
  version-controlled.
- `code/` holds the generated website — `.gitignore`d, never committed, always rebuildable
  from the intent.
- `AGENTS.md` (dropped by `lore init`) tells the coding agent to record intent before it
  writes code.

The same brief can produce different bytes each time it's materialized — lore reproduces
*behavior*, not exact files, the way two engineers given one spec write two programs that
pass the same tests.

See [lorevcs.com](https://lorevcs.com) for the full story.
