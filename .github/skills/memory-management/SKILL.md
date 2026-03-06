# Skill: Memory Management

This skill defines the rules for interacting with the `.agent-memory/` directory. It ensures that the project's long-term "brain" remains consistent, clean, and useful.

---

## 1. Directory Structure

- `.agent-memory/`
  - `project_decisions.md`: High-level architectural and design decisions.
  - `error_patterns.md`: Recurring bugs and their solutions.
  - `archive/`: Compressed or outdated entries.

---

## 2. Durable vs Runtime Memory

- Durable project knowledge belongs in `.agent-memory/`.
- Temporary execution notes, brainstorm pads, command scratchpads, and transient reports do NOT belong in `.agent-memory/`.
- Put transient state in runtime files such as `/.tmp/`, task-local notes, or other execution artifacts.
- If a detail is only useful for the current run, keep it out of durable memory.

---

## 3. Formatting Rules

### Markdown Precision

- Use clear headers for each entry.
- Separate **Facts** from **Inferences** whenever there is any uncertainty.
- Keep statements durable and repo-specific; do not paste long narrative or chat-style prose.
- Date every entry in ISO format (YYYY-MM-DD).

### Atomic Updates

- Never overwrite the entire file unless performing Garbage Collection.
- Append new entries to the bottom or merge into existing relevant sections.
- Ensure no duplicate entries exist for the same problem/decision.

---

## 4. Entry Shapes

### `project_decisions.md`

Prefer this shape for durable decisions:

```md
## <Decision Title> — YYYY-MM-DD

### Facts
- Verified repo facts with file/path references.

### Inferences
- Marked assumptions or interpretations that still need validation.

### Decision
- The durable rule, invariant, or operating choice.

### Consequences
- What this changes, constrains, or requires going forward.
```

### Onboarding Snapshot

For onboarding / project familiarization runs, append a compact baseline snapshot:

```md
## Onboarding Snapshot — YYYY-MM-DD

### Facts
- Major modules / packages
- Run / build / test commands
- Key conventions and invariants
- Top risks or TODOs worth remembering

### Inferences
- Only if necessary, and clearly marked
```

### `error_patterns.md`

Prefer this shape for recurring issues:

```md
## <Pattern Title> — YYYY-MM-DD

### Reproduction Signal
- Test name, stack trace, failing command, or clear repro steps.

### Root Cause
- The actual repeated failure mode.

### Fix
- What resolved it.

### Prevention
- Guardrail: test, lint, invariant, review rule, or coding constraint.
```

---

## 5. Conflict Resolution (Multi-Hive)

- In Multi-Hive mode, always work on the local branch copy.
- If a merge conflict occurs in memory files, prioritize the most descriptive and recent information.
- Use bullet points to list alternative approaches if consensus is not possible.

---

## 6. Memory Sync After Meaningful Changes

After any non-trivial feature, bugfix, refactor, onboarding scan, review-driven change, or CI/dependency update:

1. Update the relevant durable memory file(s).
2. Run this checklist:
   - update `project_decisions.md` if there is a new invariant, decision, onboarding snapshot, or behavior change worth keeping
   - update `error_patterns.md` if the run exposed a repeatable failure mode with a clear fix and prevention guardrail
   - keep only durable knowledge; move scratch notes, temporary plans, and verbose reports out of `.agent-memory/`
   - separate `Facts` from `Inferences` when the statement is not fully verified from the repo
   - avoid duplicate entries; merge into an existing entry if it describes the same rule or pattern
   - re-read the updated file and verify the new entry still matches the codebase and does not contradict higher-priority memory
   - if a file grows too large or stale, archive the oldest low-value entries to `.agent-memory/archive/`
3. Ensure the new entry is concise, non-duplicative, and still true after the change.
4. If the memory has drifted from reality, fix the stale entry immediately while context is fresh.

---

## 7. Smart Garbage Collection (Archiving)

- **Audit Trigger**: Perform a check when a memory file exceeds 500 lines.
- **Archiving Logic**:
  - Move the oldest 20% of entries to `.agent-memory/archive/`.
  - Name archive files as `[filename]-YYYY-MM-DD.md`.
  - Leave a "Tombstone" entry in the main file mentioning where the old data was moved.

---

## 8. Drift Recovery

If `.agent-memory/` is stale, contradictory, or mostly wrong:

1. Use the smallest recovery level that restores trust:
   - cosmetic drift: fix stale wording, duplication, missing sections, and obvious contradictions in place
   - structural drift: run a fresh onboarding / architecture scan and supersede stale entries using verified repo facts
   - rebuild from fresh evidence: if most memory is untrusted, rebuild the baseline from a fresh repo analysis and archive obsolete entries
2. Prefer repair over deletion; preserve history unless the content is clearly misleading and superseded.

---

## 9. Transaction Verification (Critical)

After every write or modification to `.agent-memory/`:

- **Read-Back**: You MUST read the file back to verify the entry was correctly appended/merged.
- **Consistency Check**: Ensure the new entry doesn't contradict existing high-priority decisions.
- **Success Report**: Explicitly state "Memory Transaction Successful: [Reason]" in your output. The Orchestrator relies on this to close the task.
