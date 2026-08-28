# Error Handling and Backup Scenarios

## Core Principle

When the workflow is uncertain, risky, or unable to verify source data, stop and ask the user. Never compensate by guessing, inventing, rounding, or editing Canvas.

No list can predict every possible UI, access, source, or browser failure. For any unlisted case, classify it by the decision levels below and choose the safest action that preserves Canvas integrity and grading truth.

## Decision Levels

| Level | Meaning | Agent action |
|---|---|---|
| L0 | Normal path | Continue after verification. |
| L1 | Safe autonomous fallback | Continue, but record the fallback in the QA decision log. |
| L2 | Needs user decision | Stop and ask the user before continuing. |
| L3 | Hard stop | Stop because continuing would risk Canvas integrity, grading accuracy, privacy, or unsupported content. |

Use L1 only when the fallback does not change grading truth. Use L2 or L3 when the issue affects rubric values, source accuracy, Canvas integrity, or final creation.

## Error Taxonomy

| Error class | Examples | Default response |
|---|---|---|
| Access/auth | Canvas login, CoEqual login, library login, expired session | Ask user to sign in or provide source material. |
| Source mismatch | Wrong assignment, duplicate title, conflicting totals | Stop and ask user. |
| Extraction uncertainty | Missing rubric, hidden rubric, unreadable prompt, collapsed content | Use read-only routes, then ask if still unavailable. |
| Source fidelity risk | Unsupported summary claim, generated benchmark detail, outside knowledge | Remove unsupported content. |
| Rubric integrity risk | Value changed, total mismatch, missing criterion, rounded value | Stop before creation and ask user. |
| Canvas mutation risk | Edit, save, publish, reply, grade, delete, upload | Do not click; find read-only path or ask user. |
| CoEqual UI drift | Form changed, button missing, default dimensions reappear | Re-inspect and verify before continuing. |
| Browser/session failure | Tab crash, page refresh, lost form state | Rebuild from local recovery files when complete. |
| File/output failure | Cannot write record files, missing QA file | Stop or use an approved writable location. |
| Privacy/sensitive data | Student data, personal files, grades, identifiers | Minimize and avoid unnecessary transfer. |
| User interruption | User changes instruction mid-run | Follow newest instruction and preserve completed records. |

## Common Backups

| Scenario | Backup behavior |
|---|---|
| Canvas page does not load | Retry once. If still blocked, ask the user to refresh/sign in or provide screenshots/text. |
| Canvas asks for login | Ask the user to sign in. Do not bypass authentication. |
| Canvas page is not the requested assignment | Stop and ask for the correct link. |
| Canvas rubric is hidden | Use only read-only rubric display options. Do not edit rubric settings. |
| Canvas rubric cannot be opened | Ask the user for a rubric screenshot/file before creating the CoEqual assignment. |
| Canvas assignment has student posts visible | Do not reply, mark, grade, or alter discussion state intentionally. |
| Rubric total conflicts with Canvas total points | Stop and ask the user. |
| Rubric has decimal values | Preserve exact decimals. Add notes that no scores between listed values are allowed. |
| CoEqual unexpectedly changes or fails to preserve an exact value during verification | Stop and ask the user how to handle it. Do not round or continue silently. |
| Directed material is inaccessible | Use Canvas prompt/rubric only and label the material as Canvas-grounded. |
| CoEqual benchmark hallucinates | Rewrite benchmark using only verified context. |
| Final Create assignment button is reached | Stop and ask for explicit confirmation. |

## Recovery Artifacts

For every assignment, maintain local records so work can be resumed:

- Assignment requirements file
- Course material text file
- QA checklist file

If the browser or CoEqual session fails, use local records to rebuild the CoEqual setup without re-extracting from Canvas unless verification is needed.
