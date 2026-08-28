# Portable Canvas-to-CoEqual Assignment Workflow

Use this prompt/runbook in Codex, Claude, or another browser-capable AI agent.

## Trigger

```text
Run the Canvas-to-CoEqual assignment workflow for this Canvas link: [Canvas assignment link]
Default CoEqual course: [CoEqual course URL]
Canvas must remain read-only. Stop before final CoEqual creation.
```

## Core Rules

- Canvas is read-only. Do not edit, save, publish, reply, grade, upload, delete, or change anything in Canvas.
- CoEqual may be edited only to prepare the requested assignment.
- Stop before clicking the final CoEqual Create assignment button and ask the user.
- Do not hallucinate assignment requirements, article details, rubric language, grading rules, or course material.
- Preserve Canvas rubric criteria, rating labels, descriptions, and point values exactly.
- Verify every stage by reading values back from the source or target page.

## Dynamic Inputs

```json
{
  "canvas_url": "[required]",
  "coequal_course_url": "[required]",
  "output_root": "[writable folder or equivalent record location]",
  "grading_defaults": {
    "approach": "Absolute Standards",
    "style": "Generous",
    "feedback": "Brief & Direct",
    "borderline": "Benefit of the Doubt",
    "scores_between_rubric_levels_allowed": false
  }
}
```

## Workflow

1. Preflight:
   - Confirm Canvas URL and CoEqual course URL.
   - Confirm Canvas policy is read-only.
   - Confirm final CoEqual creation requires user approval.

2. Canvas extraction:
   - Extract title, prompt, overview, guidelines, submission instructions, due structure, rubric criteria, rating labels, rating descriptions, exact point values, and total points.
   - Use only read-only Canvas actions.
   - Verify Canvas was not edited.

3. Course material:
   - Open directed material read-only if accessible.
   - Summarize only source-supported concepts in original words.
   - If inaccessible, use Canvas prompt/rubric only and label the limitation.

4. Local/recovery record:
   - Save or record assignment requirements, course material summary, exact rubric, setup choices, instructor notes, and QA checklist.
   - If file writing is unavailable, keep an equivalent structured record in the chat/task.

5. CoEqual setup:
   - Use exact Canvas title and total points.
   - Choose assignment type from the prompt.
   - Use Absolute Standards, Generous, Brief & Direct, and Benefit of the Doubt unless user says otherwise.
   - Read back each field after entry.

6. Rubric mapping:
   - Canvas criterion becomes CoEqual dimension.
   - Canvas rating becomes CoEqual level.
   - Labels, descriptions, and values must match exactly.
   - Remove unrelated default dimensions.
   - Verify rubric total equals Canvas total.

7. Instructor notes:
   - Tell CoEqual to grade leniently but strictly according to the Canvas rubric.
   - Use only exact rubric scores.
   - Do not invent missing work.
   - Do not treat benchmark as the only correct answer.
   - Flag unusual/borderline cases.

8. Benchmark:
   - Review generated benchmark.
   - Remove unsupported claims.
   - Add: "This benchmark is an example of an Excellent response, not a required template."
   - Keep it source-grounded and flexible.

9. Final QA:
   - Verify CoEqual course, title, total points, setup fields, rubric dimensions, labels, descriptions, point values, total, notes, benchmark, and Canvas integrity.
   - Stop and ask: "I have reviewed the CoEqual setup. Should I click Create assignment now? This will create the assignment in CoEqual, and Canvas has not been edited."

## Error Decision Levels

| Level | Meaning | Action |
|---|---|---|
| L0 | Normal path | Continue after verification. |
| L1 | Safe fallback | Continue and record fallback. |
| L2 | User decision needed | Stop and ask. |
| L3 | Hard stop | Stop because continuing risks Canvas, grading accuracy, privacy, or source truth. |

## Output Contract

```json
{
  "status": "ready_for_confirmation | needs_user_input | blocked | created_after_confirmation",
  "canvas_integrity": {
    "edited_canvas": false,
    "verified_at_stages": ["preflight", "post_extraction", "final_qa"]
  },
  "verification_summary": {
    "title_verified": true,
    "rubric_verified": true,
    "course_material_verified": true,
    "coequal_fields_verified": true,
    "benchmark_verified": true
  },
  "decision_log": [],
  "next_required_user_action": "[what the user must confirm or provide]"
}
```
