# Prompts

`NEXT.md` is the always-current continuation prompt. A new chat in a fresh context window reads this file first and knows exactly where to continue from.

---

## Rules

- `NEXT.md` is updated after every meaningful action - never let it go stale
- If context is running low, update `NEXT.md` before stopping
- Format: current status, in-progress work, numbered next steps, blockers, recently changed files
- Never delete `NEXT.md` - only update it
