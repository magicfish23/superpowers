# graphify setup (fork customization)

This fork wires several skills to consult a [graphify](https://github.com/safishamsi/graphify)
knowledge graph when one exists in the repo you're working in. Those additions
are **conditional** — they only do anything once graphify is installed and a
graph has been built. Without that, the skills fall back to normal file
exploration and behave exactly like upstream.

This file is the reminder to set graphify up so the graph-aware paths actually
fire.

## 1. Install the graphify CLI

The PyPI package is `graphifyy` (double-y); the command it installs is `graphify`.

```bash
uv tool install graphifyy      # or: pipx install graphifyy  /  pip install graphifyy
```

If `graphify` isn't found afterward, add the install dir to your PATH
(`uv tool update-shell` for uv; usually `~/.local/bin` for pipx/pip).

## 2. Build a graph in the repo

Run this at the root of a project you want the skills to navigate. Code is
parsed locally with tree-sitter — no API key and no LLM cost for a code repo:

```bash
graphify .                     # or type "/graphify ." inside your AI assistant
```

This creates `graphify-out/` (containing `graph.json` and `GRAPH_REPORT.md`),
which is the trigger the skills look for. Keep it current after edits with:

```bash
graphify update .              # AST-only, free
```

## 3. What activates

Once `graphify-out/graph.json` exists, these skills take their graph-first paths
automatically:

| Skill | What it does with the graph |
|-------|------------------------------|
| `brainstorming` | Reads `GRAPH_REPORT.md` (god nodes + communities) first to ground the design in real architecture |
| `systematic-debugging` | `graphify path`/`explain` for backward call-chain tracing and dependency lookup |
| `writing-plans` | `graphify query`/`explain`/`path` to scope which files a change touches and what depends on them |
| `requesting-code-review` | Optional blast-radius step: `graphify explain`/`path` on changed symbols to surface out-of-diff callers |

No `graphify-out/`? Every one of them falls back to grepping/reading files as
before — nothing breaks.

---

*This is a fork-specific customization (it depends on a third-party tool), kept
out of the skill bodies to preserve the "conditional, not a dependency" design.
It is intentionally not intended for upstream.*
