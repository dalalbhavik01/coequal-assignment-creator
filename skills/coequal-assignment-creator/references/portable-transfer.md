# Portable Transfer Guide

## Purpose

Use this guide to transfer the Canvas-to-CoEqual workflow to another Codex account, Claude account, or generic browser-capable AI agent.

## What Must Transfer

- Trigger phrase or user command
- Canvas read-only safety rules
- CoEqual prepare-and-stop-before-create rule
- Source-grounded course material policy
- Exact Canvas rubric mapping rules
- Instructor grading notes format
- Benchmark review rules
- Stage-by-stage verification
- Error taxonomy and decision levels
- Local recovery artifact pattern

## Account-Specific Configuration

Replace these values in every new account:

```json
{
  "default_coequal_course_url": "[new CoEqual course URL]",
  "default_output_root": "[local writable output folder]",
  "browser_surface": "[Codex in-app browser, Chrome, Claude computer use, or other]",
  "user_grading_defaults": {
    "approach": "Absolute Standards",
    "style": "Generous",
    "feedback": "Brief & Direct",
    "borderline": "Benefit of the Doubt"
  }
}
```

Never transfer private login cookies, passwords, student data, downloaded submissions, or local absolute paths as defaults.

## Codex Transfer

1. Copy `skills/coequal-assignment-creator` into the target Codex skills folder.
2. Configure the intended CoEqual course URL.
3. Keep all references together.
4. Start a new task and invoke:

```text
\createAssignment [Canvas assignment link]
```

or:

```text
$coequal-assignment-creator [Canvas assignment link]
```

## Claude or Generic Agent Transfer

Paste `docs/portable-workflow.md` into the target agent as the operating prompt.

Use:

```text
Run the Canvas-to-CoEqual assignment workflow for this Canvas link: [Canvas assignment link]
Default CoEqual course: [CoEqual course URL]
Canvas must remain read-only. Stop before final CoEqual creation.
```

The agent must adapt browser actions to its own tools, but the safety and verification rules stay the same.

## Verification Before First Use

- Confirm the agent can open Canvas read-only.
- Confirm it can view but not edit the rubric.
- Confirm it can open the intended CoEqual course.
- Confirm it can create local records or another recovery artifact.
- Confirm it stops before final Create assignment.
- Confirm it reports Canvas integrity at the end.

## Portability Rule

The workflow should depend on source evidence and verification, not on a specific model, browser tool, account, local path, or previous conversation memory.
