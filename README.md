# Superpowers (surgical fork)

Fork of [obra/superpowers](https://github.com/obra/superpowers) with two key changes:

1. **Ceremony at sprint boundaries, not per commit** — explicit Risk Tiers (trivial / standard / critical) decide how much process applies. One plan = one sprint, reviews happen at entry + exit + real mid-sprint signals, not between every step.
2. **The Golden Rule (surgical edits) is the default** — MINIMAL / SURGICAL / REUSE > CREATE / AGGREGATE > FRAGMENT / DIRECTED REFACTOR / UNCERTAINTY = ASK. Enforced per task, per subagent, per refactor, regardless of tier.

Two variants ship from this repo:

| Plugin | Target model | What's trimmed |
|---|---|---|
| `superpowers-surgical` | Any Claude model | Nothing — full skeleton with all rationalization tables and anti-cheat prose from upstream. |
| `superpowers-smart` | Opus 4.7+ / Sonnet 4.6+ | Same skeleton with tautological "don't rationalize" prose stripped. Careful-by-default models don't need the pedagogy. |

`superpowers-smart` also adds **Autonomy on Clear Asks** — batched questions with recommended defaults, single architecture-level approval (no per-section gates), inline cosmetic defaults. Turns "9 approval hops per brainstorming" back into "one run, end-to-end".

## Install

```
/plugin marketplace add yourimerad/superpowers-marketplace
/plugin install superpowers-smart@yourimerad-superpowers
```

Or `superpowers-surgical` for the non-compressed variant.

**Uninstall upstream `superpowers` first** — the skill namespace collides.

Marketplace repo: [yourimerad/superpowers-marketplace](https://github.com/yourimerad/superpowers-marketplace).

## What changed vs upstream

### New: Risk Tiers

Classify the work before picking ceremony. Default when ambiguous: **standard**. Anything touching a Non-Negotiable is **critical**, regardless of surface signals.

| Tier | Signals | Ceremony |
|---|---|---|
| **trivial** | Typo, config tweak, copy change, single-line fix, comment, rename | Skip brainstorming/plan; code + final verification |
| **standard** | New feature in existing module, bug fix needing investigation, 1-3 file refactor | Light brainstorming if unclear, plan optional, TDD, review at sprint end |
| **critical** | Security/auth, migrations, RLS, destructive ops, cross-cutting arch, new dep, >5 files or >200 LoC | Full brainstorming, full plan, subagent-driven-dev with per-batch reviews, full final review |

### New: Non-Negotiables

Bypass tiering — always critical:

- Secrets / credentials / `.env`
- Auth, authorization, session
- Supabase RLS or data migrations
- Destructive ops (`rm -rf`, `DROP TABLE`, force-push to main)
- New external dependencies
- CI/CD hooks, deploy scripts

### New: Sprint Mode

A plan (or ad-hoc task batch) = one sprint. Ceremony at **boundaries** + **critical checkpoints**, not between every step.

- **Sprint entry (once):** acknowledge Golden Rule, confirm tier, skim plan for non-negotiables.
- **Sprint exit (once):** full verification, tier-scaled final review, `finishing-a-development-branch`.
- **Mid-sprint triggers** (mini-review only when these fire): 3+ consecutive failed fix attempts, scope creep beyond plan, touching a Non-Negotiable, batch of 3-5 tasks complete in subagent-driven-dev.

Per-task / per-commit / per-claim ceremony is **not** a mid-sprint trigger.

### New: Subagent Directive Block

Paste verbatim at the top of every subagent prompt — enforces the Golden Rule + scope guard:

```
## Directive — Surgical Edits (non-negotiable)

1. MINIMAL: change only what the task requires
2. SURGICAL: no opportunistic refactor, no style churn, no unrelated cleanup
3. REUSE > CREATE: extend existing code; don't duplicate
4. AGGREGATE > FRAGMENT: one solution over several
5. DIRECTED REFACTOR: factor only across sites you already touch
6. UNCERTAINTY = ASK: new lib / pattern / folder → report BLOCKED with a scope concern

Scope guard: if the task would produce more than 2 new files, more than ~150 LoC,
or require a new external dependency, STOP and report DONE_WITH_CONCERNS with a
scope note before implementing the oversized version.
```

### Smart variant only: Autonomy on Clear Asks

When the request is unambiguous and tier is trivial/standard, execute end-to-end in a single run. Explicit don't-ask / do-stop lists replace the "ask-per-section" default.

**Don't ask for:** cosmetic defaults (colors, copy, icons), reversible conventions already visible in the codebase, routine section walkthroughs once architecture is approved, confirmation that the obvious next step is obvious.

**Do stop for:** Non-Negotiables, scope creep beyond the request, genuine ambiguity with no reasonable default, real tradeoff with no obvious winner.

## Workflow

Unchanged from upstream:

1. **brainstorming** → design doc
2. **using-git-worktrees** → isolated branch
3. **writing-plans** → bite-sized tasks
4. **subagent-driven-development** / **executing-plans** → sprint
5. **test-driven-development** → RED-GREEN-REFACTOR inside each task
6. **requesting-code-review** → batch-end review
7. **finishing-a-development-branch** → merge / PR / keep / discard

What changed is *how much ceremony each step carries* — scaled to tier, concentrated at sprint boundaries.

## Version

Current release: `v5.0.7-surgical.1` (surgical) / `v5.0.7-surgical-smart.2` (smart).

See [RELEASE-NOTES.md](RELEASE-NOTES.md) for per-tag deltas.

## Credits & license

Upstream: [obra/superpowers](https://github.com/obra/superpowers) by [Jesse Vincent](https://blog.fsck.com) and [Prime Radiant](https://primeradiant.com). All core skill architecture, TDD rigor, and systematic-debugging patterns are theirs.

This fork: [@yourimerad](https://github.com/yourimerad). Ceremony refactor + smart compression.

MIT License — inherits from upstream. See [LICENSE](LICENSE).

## Upstream contribution note

New skills or cross-agent changes should go upstream ([obra/superpowers](https://github.com/obra/superpowers)), not here. This fork stays focused on the Sprint Mode / Risk Tiers / Golden Rule deltas and their compressed variant.
