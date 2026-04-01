# Claude Code Prompts — Improvement Plan
*Source: https://github.com/repowise-dev/claude-code-prompts (cloned to /tmp/claude-code-prompts)*
*Created: 2026-04-02 by Nix*

## Summary
Analyzed the repowise-dev/claude-code-prompts repo — reverse-engineered Claude Code prompt patterns.
Identified 5 concrete improvements to import into our system.

---

## Action Items (in priority order)

### 1. Patch context_compressor.py — add analysis phase + user messages section
**File:** `/home/koichi/.hermes/hermes-agent/agent/context_compressor.py`
**What:** Their conversation-summary prompt has two things we don't:
- An explicit `<analysis>` reasoning phase before writing the summary (walk through messages chronologically, identify intent, capture pivotal decisions)
- A dedicated "All User Messages" section that preserves verbatim user requests

**Why:** Improves recall during context handoff — assistant doesn't lose what the user originally asked for.

**How:** Add to both the `_generate_summary` prompts (first compaction + iterative update):
- Prepend analysis phase instructions before the structured sections
- Add "All User Messages" as section 8 (between Next Steps and Critical Context)

---

### 2. Upgrade hermes-memory-pruning skill — 4-phase "dream consolidation"
**Skill:** `hermes-memory-pruning`
**What:** Their memory-consolidation prompt uses a 4-phase "dream" structure:
- Phase 1 Orient: list memory dir, read index, skim topic files
- Phase 2 Gather Recent Signal: look for new info in daily logs, find drifted memories contradicting current state
- Phase 3 Consolidate: merge new signal, convert relative→absolute dates, delete contradicted facts
- Phase 4 Prune and Index: refresh index, remove stale pointers, shorten verbose entries, resolve contradictions

Also add Statement/Evidence/Confidence format to memory entries (high/medium/low).

---

### 3. Create session-notes skill — auto-maintained structured file per task
**What:** New skill that maintains a structured notes file per session with fixed sections:
- Session Title
- Current State (ALWAYS update — vital for continuity)
- Task Specification
- Files and Functions
- Workflow
- Errors & Corrections
- Codebase and System Documentation
- Learnings
- Key Results
- Worklog (chronological)

**Rules from their spec:**
- Edit tool only, never alter headings or italic descriptions
- Issue all Edit calls in parallel
- Write thorough, information-dense entries (file paths, function names, exact commands)
- Keep each section under ~2000 tokens

---

### 4. Install verification-agent skill
**What:** Their verification-agent SKILL.md is clean and fills a gap our systematic-debugging doesn't cover — explicit PASS/FAIL/INCONCLUSIVE verdicts with evidence + residual risk.

**Core workflow:**
1. Define verification target (what must be true)
2. Identify highest-risk failure modes
3. Select strategy (quick / targeted / deep)
4. Execute checks, capture concrete evidence
5. Report: Status, Evidence, Risk, Next Step

**Minimal output contract:**
- Status: pass / fail / inconclusive
- Evidence: commands, tests, observable behavior
- Risk: what could still be wrong
- Next step: exact follow-up action if needed

---

### 5. Update prompt-architect skill with patterns reference
**What:** The 9 pattern files are good reference material for our prompt-architect skill.
Fold in as a reference doc covering: system prompt architecture, behavioral rules, safety tiers, tool routing, agent delegation, verification, memory, multi-agent coordination, auxiliary prompts.

---

## Source Files Reference
All source material cloned to `/tmp/claude-code-prompts/` (ephemeral — may be gone after reboot).

Key files:
- `complete-prompts/memory-prompts/conversation-summary.md` — 9-section summary with analysis phase
- `complete-prompts/memory-prompts/memory-extraction.md` — two-turn efficiency + Statement/Evidence/Confidence
- `complete-prompts/memory-prompts/memory-consolidation.md` — 4-phase dream consolidation
- `complete-prompts/memory-prompts/session-notes.md` — 10-section session notes spec
- `skills/verification-agent/SKILL.md` — verification workflow skill
- `skills/verification-agent/strategies.md` — verification strategy guide
- `patterns/01-09-*.md` — pattern analyses with templates

## Status
- [ ] 1. Patch context_compressor.py
- [ ] 2. Upgrade hermes-memory-pruning skill
- [ ] 3. Create session-notes skill
- [ ] 4. Install verification-agent skill
- [ ] 5. Update prompt-architect skill
