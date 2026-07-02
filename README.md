# Fable Advisor

**Your cheap model does the work. The smartest model makes the calls.**

Claude Code lets every subagent run on a different model. This pattern exploits that: your session runs on Sonnet (fast, cheap), and at *commitment boundaries* — architecture decisions, migrations, refactors, debugging that resists two attempts — it consults an advisor pinned to **Fable 5**, Anthropic's most capable model. The advisor reads your actual code, returns a verdict in under 300 words, and never touches a file.

Tokens route by volume: the expensive model emits the fewest tokens (judgment), the cheap model emits the most (code). A typical advisor consult costs cents; running your whole session on Fable costs dollars.

## Install

**Option A — one file, 30 seconds.** Copy [`agents/fable-advisor.md`](agents/fable-advisor.md) into `~/.claude/agents/`. Done — your next session can consult it.

**Option B — plugin (gets you updates + the implementer agent):**

```
claude plugin marketplace add DannyMac180/fable-advisor
claude plugin install fable-advisor
```

## Requirements

- **Claude Code ≥ 2.1.170** with a subscription that includes Fable 5 (Pro, Max, Team, or Enterprise — all current consumer plans qualify).
- **No Fable access** (e.g. API-key billing)? Change one line in the agent file: `model: fable` → `model: opus`. Same pattern, still a full tier above your working model.
- Heads-up: if the pinned model isn't available on your account, Claude Code silently falls back to your session model — the pattern degrades quietly rather than erroring. If verdicts feel unremarkable, check your plan.

## Use it

Just ask for a consult at the moment that matters:

```
Migrate our checkout sessions from Postgres to Redis — plan it,
consult your advisor before committing, then implement.
```

Or make it automatic: add a line to your project's `CLAUDE.md`:

```
Before committing to any architecture decision, migration, or refactor
touching 3+ files, consult the fable-advisor agent and act on its verdict.
```

## The full pattern (plugin install)

The plugin also ships an **implementer** agent, so you can run the complete cost-routing loop:

| Task | Producer | Invocation |
|---|---|---|
| Judgment calls (architecture, migrations, "am I about to waste an hour?") | Fable 5 | `fable-advisor` agent |
| Routine, well-specified implementation | Sonnet | `implementer` agent (default) |
| Subtle or high-stakes implementation | Opus | `implementer` with `model="opus"` |

Model resolution order in Claude Code: `CLAUDE_CODE_SUBAGENT_MODEL` env var → per-invocation `model` parameter → agent frontmatter → session model.

**Advanced mode** (inverts the pattern): run the *session itself* on Fable (`/model fable`) as a full-time architect and delegate all implementation to the `implementer` agent. Highest quality, still ~60% cheaper than Fable-for-everything, because implementation tokens dominate.

## FAQ

**Is this Anthropic's "advisor tool"?** No — that's a server-side API feature. This is a plain Claude Code subagent: readable, editable, no beta flags.

**Does this work on claude.ai?** No — subagent model routing is Claude Code only (CLI, desktop, VS Code, web).

**Why not just run everything on Fable?** You can. It's excellent. It's also ~3× the per-token cost of Sonnet, and 90% of a session's tokens are implementation mechanics that Sonnet handles at near-parity. Spend the premium where judgment lives.

## License

MIT
