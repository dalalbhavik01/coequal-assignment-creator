# Failure Drills and Workflow I/O

## Purpose

Use these drills to test whether the workflow can handle failure without editing Canvas, hallucinating content, or silently changing grading rules.

## Decision Levels

| Level | Meaning | Agent action |
|---|---|---|
| L0 | Normal path | Continue after verification. |
| L1 | Safe autonomous fallback | Continue, but record the fallback in the decision log. |
| L2 | Needs user decision | Stop and ask the user before continuing. |
| L3 | Hard stop | Stop because continuing would risk Canvas integrity, grading accuracy, privacy, or unsupported content. |

## Workflow Input Contract

```json
{
  "trigger": "\\createAssignment",
  "canvas_url": "[Canvas assignment URL]",
  "coequal_course_url": "[CoEqual course URL]",
  "canvas_policy": "read_only",
  "coequal_policy": "prepare_verify_and_handoff_for_user_create",
  "grading_preferences": {
    "approach": "Absolute Standards",
    "style": "Generous",
    "borderline": "Benefit of the Doubt",
    "scores_between_rubric_levels_allowed": false
  }
}
```

## Workflow Output Contract

```json
{
  "status": "ready_for_user_create_click | needs_user_input | blocked | created_after_user_click",
  "canvas_integrity": {
    "edited_canvas": false,
    "actions_taken": ["read_assignment", "view_rubric"],
    "risky_actions_avoided": ["edit", "reply", "publish", "save", "grade"]
  },
  "coequal_setup_summary": {
    "title": "[Exact Canvas title]",
    "total_points": "[Exact Canvas total]",
    "rubric_dimensions": "[Count]",
    "rubric_total_verified": true
  },
  "decision_log": [],
  "next_required_user_action": "Click Create assignment directly in CoEqual after reviewing the final screen."
}
```

## Drill 1: Directed Course Material Is Inaccessible

Smart decision:

- Decision level: L1.
- Do not invent article details.
- Continue using Canvas prompt, rubric, and visible assignment context only.
- Mark course material as Canvas-grounded only.
- Record the limitation in the decision log.

## Drill 2: Canvas Rubric Total Conflicts With Assignment Total

Smart decision:

- Decision level: L2.
- Stop before CoEqual setup.
- Do not choose one total silently.
- Ask the user which total should be used.

## Drill 3: CoEqual Unexpectedly Changes an Exact Rubric Value

Smart decision:

- Decision level: L2.
- Stop before final user handoff.
- Do not round or accept the changed value silently.
- Ask the user how to handle it.

## Drill 4: CoEqual Benchmark Hallucinates Reading Details

Smart decision:

- Decision level: L1.
- Rewrite the benchmark using only verified context.
- Add the standard note that the benchmark is an example, not a required template.

## Drill 5: A Canvas Control Could Modify the Page

Smart decision:

- Decision level: L3 for that action.
- Do not click the risky control.
- Find a read-only route if available.
- If no read-only route exists, ask the user for screenshots/text.

## Drill 6: Wrong CoEqual Course Is Open

Smart decision:

- Decision level: L1 if the correct course URL is known.
- Navigate to the configured CoEqual course.
- Verify course title before creating.

## Drill 7: Browser Session Fails Mid-Workflow

Smart decision:

- Decision level: L1 if local source records are complete.
- Rebuild CoEqual from local record files.
- Re-open Canvas only if source verification is needed.
- Keep Canvas read-only.
