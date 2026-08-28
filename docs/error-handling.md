# Error Handling

When uncertain, stop and ask. Do not guess, invent, round, or edit Canvas.

## Decision Levels

| Level | Meaning | Agent action |
|---|---|---|
| L0 | Normal path | Continue after verification. |
| L1 | Safe autonomous fallback | Continue, but record the fallback in the decision log. |
| L2 | Needs user decision | Stop and ask the user before continuing. |
| L3 | Hard stop | Stop because continuing would risk Canvas integrity, grading accuracy, privacy, or unsupported content. |

Use L1 only when the fallback does not change grading truth. Use L2 or L3 when the issue affects rubric values, source accuracy, Canvas integrity, privacy, or final creation.

## Host Model Escalation

The model currently running this workflow is responsible for smart error handling. If the workflow runs in Claude, Claude applies the decision levels. If it runs in Codex or ChatGPT, Codex/ChatGPT applies them.

Escalation does not mean guessing or overriding safeguards. The host model must classify the issue, choose the safest allowed action, record the decision, and ask the user when the issue affects Canvas integrity, rubric truth, source accuracy, privacy, or final CoEqual creation.

Use [model-escalation.md](model-escalation.md) for the exact input/output contract.

## Canvas Read-Only Guardrail

Before every Canvas action, classify the action as read-only or mutation-risk.

Allowed Canvas actions are opening pages, scrolling, expanding read-only content, viewing rubrics, opening/downloading directed material, and copying visible text.

Forbidden Canvas actions are editing, saving, publishing, replying, grading, uploading, deleting, changing due dates, changing modules, changing rubrics, changing assignments, changing discussions, changing pages, changing SpeedGrader state, or submitting Canvas forms.

If an action is not clearly read-only, treat it as L3 for that action and stop.

## General Error Taxonomy

| Error class | Examples | Default response |
|---|---|---|
| Access/auth | Canvas login, CoEqual login, library login, expired session | Ask user to sign in or provide source material. |
| Source mismatch | Wrong assignment, duplicate assignment title, conflicting totals | Stop and ask user. |
| Extraction uncertainty | Missing rubric, hidden rubric, unreadable prompt, collapsed content | Use read-only routes, then ask if still unavailable. |
| Source fidelity risk | Unsupported summary claim, generated benchmark detail, outside knowledge | Remove unsupported content. |
| Rubric integrity risk | Value changed, total mismatch, missing criterion, rounded value | Stop before creation and ask user. |
| Canvas mutation risk | Edit, save, publish, reply, grade, delete, upload | Do not click; find read-only path or ask user. |
| CoEqual UI drift | Form changed, button missing, default dimensions reappear | Re-inspect and verify before continuing. |
| Browser/session failure | Tab crash, page refresh, lost form state | Rebuild from recovery records when complete. |
| File/output failure | Cannot write record files, missing QA file | Stop or use an approved writable location. |
| Privacy/sensitive data | Student data, personal files, grades, identifiers | Minimize and avoid unnecessary transfer. |
| User interruption | User changes instruction mid-run | Follow newest instruction and preserve completed records. |

## Common Scenarios

| Scenario | Behavior |
|---|---|
| Canvas page does not load | Retry once, then ask the user to refresh/sign in or provide screenshots/text. |
| Canvas asks for login | Ask the user to sign in. Do not bypass authentication. |
| Canvas rubric cannot be opened | Ask the user for a rubric screenshot/file before creating the CoEqual assignment. |
| Rubric total conflicts with assignment total | Stop and ask which total to use. |
| Rubric has decimal values | Preserve exact decimals. Add notes that no scores between listed values are allowed. |
| CoEqual unexpectedly changes an exact value during readback | Stop and ask. Do not round or continue silently. |
| Directed material is inaccessible | Use Canvas prompt/rubric only and label the course material as Canvas-grounded. |
| Directed material is copyrighted/long | Summarize briefly in original words. |
| CoEqual benchmark hallucinates | Rewrite using only verified context. |
| Browser loses form state | Rebuild from local recovery records. |
| Final Create assignment button is reached | Stop and ask for explicit confirmation. |
