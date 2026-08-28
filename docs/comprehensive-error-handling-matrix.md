# Comprehensive Error Handling Matrix

No workflow can list every possible UI, login, source, or model failure. This matrix covers the important classes of failure and gives the host model a repeatable way to make safe decisions.

The host model is the model running the workflow. If the workflow is running in Claude, Claude is the host model. If it is running in Codex or ChatGPT, Codex/ChatGPT is the host model.

## Decision Levels

| Level | Meaning | Continue? | User needed? |
|---|---|---:|---:|
| L0 | Normal verified path | Yes | No |
| L1 | Safe fallback that does not change grading truth | Yes | Usually no |
| L2 | User decision needed | No | Yes |
| L3 | Hard stop because continuing risks Canvas, source truth, privacy, or final creation | No | Yes |

## Master Matrix

| Stage | Failure case | Risk area | Decision | Safe response | Forbidden response |
|---|---|---|---|---|---|
| Preflight | No Canvas URL supplied | Source truth | L2 | Ask for Canvas link. | Guess assignment from memory. |
| Preflight | No CoEqual course URL or configured default | CoEqual target | L2 | Ask for target CoEqual course. | Use old course link silently. |
| Preflight | User asks to edit Canvas as part of assignment creation | Canvas integrity | L2/L3 | Ask for separate explicit Canvas-edit confirmation or refuse unsafe edit. | Treat CoEqual creation as Canvas edit permission. |
| Preflight | Browser has multiple Canvas tabs | Source truth | L2 | Ask or use the URL provided in the trigger. | Pick a random tab. |
| Canvas extraction | Canvas login required | Access | L2 | Ask user to sign in. | Bypass login or use unrelated source. |
| Canvas extraction | Page does not load | Browser/session | L1 then L2 | Retry once read-only; ask if still blocked. | Keep retrying indefinitely. |
| Canvas extraction | Page title does not match expected assignment | Source mismatch | L2 | Ask user to confirm correct link. | Continue with mismatched page. |
| Canvas extraction | Assignment prompt is partially hidden behind read-only expansion | Extraction | L1 | Expand/read visible content. | Edit page to inspect source. |
| Canvas extraction | Prompt hidden behind Edit or settings | Canvas integrity | L3 | Stop and ask for screenshot/text/export. | Click Edit. |
| Canvas extraction | Discussion thread has student content | Privacy/Canvas state | L1 | Read assignment prompt/rubric only if needed; avoid posting/grading. | Reply, mark, grade, or alter thread state. |
| Rubric extraction | Rubric visible | Rubric accuracy | L0 | Extract criteria, labels, descriptions, points, total. | Summarize loosely. |
| Rubric extraction | Rubric missing or cannot be opened read-only | Rubric accuracy | L2 | Ask for screenshot/export or confirm no rubric. | Invent rubric. |
| Rubric extraction | Rubric total differs from assignment points | Rubric accuracy | L2 | Ask which value should drive CoEqual. | Choose silently. |
| Rubric extraction | Criteria have decimal values | Rubric accuracy | L0 | Preserve exact decimals. | Round or normalize. |
| Rubric extraction | Criteria have nonstandard labels | Rubric accuracy | L0 | Preserve labels exactly. | Rename to generic labels. |
| Course material | Linked material accessible | Source truth | L0 | Summarize only verified content in original words. | Copy long passages or add outside facts. |
| Course material | Linked material inaccessible | Source truth | L1/L2 | Use Canvas prompt/rubric only if enough; ask if material is required. | Invent reading details. |
| Course material | Material requires separate library login | Access/source truth | L2 | Ask user to sign in or provide file/text. | Use web summaries as substitute without approval. |
| Course material | Material is very long | Source truth | L1 | Create concise source-grounded gist and label scope. | Treat gist as exhaustive answer key. |
| Course material | Material conflicts with prompt/rubric | Source truth | L2 | Ask user which source controls grading. | Blend conflicting requirements silently. |
| Local records | Output folder unavailable | Recovery | L1/L2 | Use approved writable folder or keep structured chat record. | Write to unauthorized/private location. |
| Local records | Existing recovery file with same assignment | Recovery/source truth | L1 | Version the new record or ask before overwrite. | Overwrite without checking. |
| CoEqual setup | Wrong CoEqual course open | CoEqual target | L1 | Navigate to configured CoEqual URL and verify. | Create in visible wrong course. |
| CoEqual setup | CoEqual login required | Access | L2 | Ask user to sign in. | Bypass login. |
| CoEqual setup | Required CoEqual field missing due UI change | CoEqual accuracy | L2 | Re-inspect UI, then ask if mapping is uncertain. | Fill nearest-looking field without verification. |
| CoEqual setup | Assignment type uncertain | Grading setup | L2 if material | Infer only when prompt is clear; otherwise ask. | Hard-code previous assignment type. |
| CoEqual setup | Cohort size unknown | Low grading risk | L1 | Choose best available option from enrollment/context; record assumption. | Pretend exact enrollment is verified. |
| Rubric mapping | CoEqual default rubric dimensions present | Rubric accuracy | L1 | Remove or replace only if safe and verified. | Leave unrelated dimensions. |
| Rubric mapping | CoEqual cannot represent rubric shape exactly | Rubric accuracy | L2 | Stop and ask for workaround. | Approximate silently. |
| Rubric mapping | CoEqual changes a value on readback | Rubric accuracy | L2 | Stop before final creation and ask. | Accept changed value. |
| Rubric mapping | CoEqual allows decimals but Canvas rubric uses whole numbers | Rubric accuracy | L0 | Enter whole numbers exactly. | Add decimal/in-between grades. |
| Instructor notes | User wants lenient grading | Grading setup | L0 | Add lenient interpretation while staying rubric-bound. | Let leniency override required work. |
| Instructor notes | User wants no in-between scores | Rubric accuracy | L0 | List exact allowed scores and sum rule. | Tell CoEqual to average freely. |
| Instructor notes | Prompt is open-ended | Source truth | L0 | Say benchmark is not the only correct answer. | Require one exact answer path. |
| Benchmark | Generated benchmark includes unsupported details | Source truth | L1 | Remove unsupported details. | Keep hallucinated claims. |
| Benchmark | Benchmark overfits to one answer | Grading fairness | L1 | Add example-not-template framing. | Make benchmark a hidden answer key. |
| Final QA | Any source/rubric field unverified | Accuracy | L2 | Ask user or return to read-only verification. | Click Create assignment. |
| Final QA | User has not confirmed final creation | External side effect | L2 | Ask final confirmation. | Click Create assignment. |
| Post-create | CoEqual creation succeeds | Reporting | L0 | Record URL/id and Canvas integrity statement. | Claim Canvas was updated. |
| Post-create | CoEqual creation fails | Recovery | L1/L2 | Record failure, preserve local state, retry only if safe. | Recreate blindly and risk duplicate assignments. |
| Privacy | Learner identifiers or grades appear unexpectedly | Privacy | L2 | Minimize, avoid uploading, ask if needed. | Put learner data into public docs. |
| Browser | Tab crashes or reloads | Recovery | L1 | Rebuild from recovery record. | Re-extract by editing Canvas. |
| User interruption | User changes instructions mid-run | Control | L0/L2 | Follow newest instruction; preserve state. | Continue old plan. |

## Unlisted Failure Procedure

For any failure not listed above:

```json
{
  "stage": "[current stage]",
  "observed_problem": "[specific problem]",
  "risk_area": "[canvas_integrity | rubric_accuracy | source_truth | privacy | coequal_creation | browser_session | recovery]",
  "verified_facts": ["[fact 1]", "[fact 2]"],
  "safe_options": ["[option 1]", "[option 2]"],
  "forbidden_options": ["edit Canvas", "invent content", "change rubric silently", "final create without confirmation"],
  "decision_level": "L0 | L1 | L2 | L3",
  "next_action": "[specific next action]"
}
```

If the issue touches Canvas integrity, source truth, rubric truth, privacy, or final creation, the next action must be to ask the user or stop.

## Duplicate Prevention

Before final CoEqual creation:

- Search/inspect the target CoEqual course for an existing assignment with the same title when possible.
- If a likely duplicate exists, stop and ask.
- If the browser refreshes after final creation, do not click Create again unless CoEqual clearly shows no assignment was created and the user confirms.

## Recovery Rule

If a run fails after Canvas extraction but before CoEqual creation, restart from the local recovery record, not from memory.

If the recovery record is incomplete or untrusted, re-verify from Canvas in read-only mode.
