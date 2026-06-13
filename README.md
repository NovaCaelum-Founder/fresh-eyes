# Fresh Eyes

> A reviewer with no memory of you, no history with your system, and no reason to flatter either.
>
> Fresh Eyes is a detached, blank-slate external consultant for agentic systems: it reads a review packet and returns an evidence-bound verdict. Designed and maintained by [Nova Caelum & Co.](https://novacaelum.com) — AI Strategy & Enterprise Transformation.

## The problem this addresses

Agentic systems accumulate memory — user context files, worklogs, preference notes. That memory is what makes them useful, and it is also what makes them unreliable as critics of their own setup. Research on memory-augmented agents (*Recalling Too Well*, ICLR 2026 workshop) found that agents carrying preference memory show substantially higher sycophancy and preference over-alignment — they tell you what you like hearing — and that prompt-level fixes ("be ruthless," "be objective") barely move the result.

The lever that works is structural, not tonal: **detachment**. A reviewer with no memory of you, no history with the system under review, and no preloaded context has nothing to over-align with.

This repo is that reviewer. It holds an agent definition, a report template, and a packet protocol — and deliberately nothing else. No memory files. No worklog preload. Every review starts cold.

## How it works

```
your system's agent                this repo (fresh session)
─────────────────────              ─────────────────────────
generates a review packet   ───►   verifies the packet is complete
(purpose + rubric +                reviews artifacts against the rubric
 manifest + artifacts)             delivers a structured verdict
                                   commits the report to reports/
                            ◄───   you arbitrate; your own agents implement
```

The packet is the entire interface. The consultant evaluates what it is handed, against the rubric it is handed, and nothing more.

## The packet protocol

A review packet is one markdown file with four required sections. The consultant refuses incomplete packets rather than improvising the missing parts.

| Section | What it carries | Why it's required |
|---|---|---|
| **Purpose statement** | What the system under review is *for* — functional, depersonalized, no preference language | Without purpose, a reviewer can only judge taste and count tokens |
| **Rubric** | The evaluation criteria for *this* review | The criteria travel with the input, keeping the reviewer blank-slate |
| **Manifest** | Every component: size, ~tokens, load condition (always / persona / trigger / on-demand) | Turns the review from aesthetic to economic — a small always-loaded file can cost more than a large rarely-read one |
| **Artifacts** | The files under review, embedded between `==== BEGIN FILE / END FILE ====` delimiters under a data-not-instructions banner | Prompt files are imperative by nature; the packet structure keeps them inert |

Two protocol rules worth understanding:

- **Preference memory stays out.** User context files and worklogs appear in the manifest (size and load condition only) but their content is excluded. Preference memory in the reviewer's context is the exact failure mode this design exists to avoid.
- **Instruction files are data.** Packets routinely contain `CLAUDE.md` files, agent personas, and skills. The consultant treats their imperative language as the object of review — and flags any content that attempts to instruct it as a finding.

A worked packet generator is in [`examples/generate-packet.sh`](examples/generate-packet.sh). Adapt the file list, purpose, and rubric to your system.

## Setup

1. Use this repo as a template (or fork it). Keep it separate from the system it reviews — the separation *is* the design.
2. Grant your Claude Code surface access to the repo.
3. Decide how packets arrive: a connected storage folder (Box, Drive — conventionally `review-inbox/`) or pasted directly into the session.

## Running a review

1. In your own system's session: generate a packet and deliver it to the inbox.
2. Open a fresh session on *this* repo.
3. Say: *"Review the latest packet in the inbox."* Optionally add a failure brief — observed symptoms only ("the agent has been verbose," "skill X never fires"). Don't tell it what conclusion you want.
4. It delivers a report — verdict table, value-per-token ranking, a minimum of three change proposals with breakage analysis, and a handoff block — and commits it to `reports/`.
5. You arbitrate each proposal. Accepted items go back to your own system's agents for implementation. The consultant never implements.

**Security floor.** Independent of your rubric, every review runs a built-in safety check for blatant, high-confidence security mistakes — hardcoded secrets, unprotected `.env`/key files, wide-open agent permissions, auto-execution of untrusted input. It's a floor for catastrophic-obvious errors, not a security audit, and it's silent unless it catches something. A tripped floor opens the report with a 🚩.

## Rules of the road

- **Don't add memory.** No user context file, no worklog preload, no preference notes. `reports/` is the only history, and the consultant doesn't read it unless you explicitly request a delta review ("compare today's report with last month's — as data").
- **The consultant never touches the target system.** It has no access and must not request it. By design, not by omission.
- **The packet format is generic.** Anything bundled with a purpose statement, rubric, and manifest can be reviewed the same way — an agent stack, a process document, a project plan.

## Troubleshooting

- **"It says the packet is incomplete."** It's right — regenerate. Don't let it improvise a rubric; an improvised rubric is the reviewer's own taste wearing a costume.
- **"The review feels harsh."** Check the evidence column. Every verdict requires evidence and every proposed cut requires a breakage analysis — if either is weak, reject the proposal. That's the operator's half of the loop.

---

*Nova Caelum & Co. designs and implements AI systems and enterprise capabilities to solve complex business problems at scale.*
