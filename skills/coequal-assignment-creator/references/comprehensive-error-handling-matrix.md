# Comprehensive Error Handling Matrix

Use this matrix for failures in Codex, ChatGPT, Claude, or another agent environment. The model running the workflow is the host model and handles escalation.

## Decision Levels

| Level | Meaning | Continue? | User needed? |
|---|---|---:|---:|
| L0 | Normal verified path | Yes | No |
| L1 | Safe fallback that does not change grading truth | Yes | Usually no |
| L2 | User decision needed | No | Yes |
| L3 | Hard stop because continuing risks Canvas, source truth, privacy, or final creation ownership | No | Yes |

## Core Matrix

| Stage | Failure case | Risk area | Decision | Safe response | Forbidden response |
|---|---|---|---|---|---|
| Preflight | No Canvas URL supplied | Source truth | L2 | Ask for Canvas link. | Guess assignment from memory. |
| Preflight | No CoEqual course URL or default | CoEqual target | L2 | Ask for target CoEqual course. | Use old course link silently. |
| Canvas extraction | Canvas login required | Access | L2 | Ask user to sign in. | Bypass login. |
| Canvas extraction | Page title does not match expected assignment | Source mismatch | L2 | Ask user to confirm link. | Continue with mismatched page. |
| Canvas extraction | Prompt hidden behind Edit/settings | Canvas integrity | L3 | Ask for screenshot/text/export. | Click Edit. |
| Rubric extraction | Rubric cannot be opened read-only | Rubric accuracy | L2 | Ask for screenshot/export. | Invent rubric. |
| Rubric extraction | Rubric total differs from assignment points | Rubric accuracy | L2 | Ask which total controls. | Choose silently. |
| Rubric extraction | Criteria have decimal values | Rubric accuracy | L0 | Preserve exact decimals. | Round or normalize. |
| Course material | Linked material inaccessible | Source truth | L1/L2 | Use Canvas prompt/rubric only if enough; ask if required. | Invent reading details. |
| Course material | Material conflicts with prompt/rubric | Source truth | L2 | Ask which source controls. | Blend silently. |
| CoEqual setup | Wrong CoEqual course open | CoEqual target | L1 | Navigate to configured course and verify. | Create in wrong course. |
| CoEqual setup | Required field missing due UI change | CoEqual accuracy | L2 | Re-inspect UI, ask if uncertain. | Fill nearest-looking field. |
| Rubric mapping | CoEqual cannot represent rubric exactly | Rubric accuracy | L2 | Stop and ask for workaround. | Approximate silently. |
| Rubric mapping | CoEqual changes a value on readback | Rubric accuracy | L2 | Stop before final user handoff and ask. | Accept changed value. |
| Instructor notes | User wants exact rubric-only scores | Rubric accuracy | L0 | List exact allowed scores and sum rule. | Tell CoEqual to average freely. |
| Benchmark | Generated benchmark has unsupported details | Source truth | L1 | Remove unsupported details. | Keep hallucinated claims. |
| Final QA | Any source/rubric field unverified | Accuracy | L2 | Ask user or re-verify read-only. | Hand off for Create assignment. |
| Final QA | User has not clicked final creation | External side effect | L2 | Show or leave the CoEqual review screen open and ask the user to click Create assignment directly. | Click Create assignment yourself. |
| Post-create | Creation fails | Recovery | L1/L2 | Record failure and retry only if safe. | Recreate blindly. |
| Privacy | Learner identifiers or grades appear unexpectedly | Privacy | L2 | Minimize and ask if needed. | Upload to public docs. |
| Browser | Tab crashes or reloads | Recovery | L1 | Rebuild from recovery record. | Use memory as source truth. |

## Unlisted Failure Procedure

For any unlisted failure:

1. Identify stage.
2. Identify risk area.
3. List verified facts.
4. List safe options.
5. List forbidden options.
6. Choose L0/L1/L2/L3.
7. Record decision.
8. Ask user if the issue affects Canvas integrity, source truth, rubric truth, privacy, or final creation ownership.
