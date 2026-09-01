---
name: coequal-assignment-creator
description: Create CoEqual assignments from Canvas assignment links with Canvas kept read-only. Use when the user writes "\createAssignment <Canvas link>", invokes "$coequal-assignment-creator", or asks to run the Canvas-to-CoEqual assignment workflow.
---

# CoEqual Assignment Creator

Use this skill to create a CoEqual assignment from a Canvas assignment or discussion page while preserving Canvas exactly as-is.

The workflow is dynamic. Use the user's current Canvas link, CoEqual course link, rubric structure, assignment type, grading preferences, and available browser/tooling. Do not hard-code assumptions beyond the configured CoEqual course profile.

## Required Configuration

For each account or course, configure the intended CoEqual course URL.

If no default CoEqual course URL is configured and the user does not provide one, ask for it before editing CoEqual.

## Hard Safety Rules

- Canvas is read-only. Do not edit, save, publish, reply, grade, upload, delete, or change anything in Canvas.
- CoEqual may be edited only for creating the requested assignment.
- Stop on the final CoEqual review screen, make the browser visible when possible, and ask the user to click `Create assignment` directly on the CoEqual website. Do not click the final create button yourself.
- Do not hallucinate assignment content, article details, rubric language, course material, grading rules, or student requirements.
- Preserve Canvas rubric criteria, rating labels, descriptions, and point values exactly.
- Verify after entry that CoEqual preserved the exact Canvas rubric values and descriptions.
- If CoEqual unexpectedly changes a rubric value, description, or total, stop and ask the user.

## Invocation Pattern

When the user writes:

```text
\createAssignment [Canvas assignment link]
```

run the workflow using that Canvas link and the configured CoEqual course.

If the user provides a different CoEqual course link, use that instead.

## Required Procedure

Before running browser actions for an actual assignment creation request, read:

- [Workflow](references/workflow.md)
- [Error Handling](references/error-handling.md)
- [Canvas Read-Only Policy](references/canvas-read-only-policy.md)
- [Comprehensive Error Handling Matrix](references/comprehensive-error-handling-matrix.md)

If the user asks to test, simulate, audit, or improve failure handling, also read:

- [Failure Drills](references/failure-drills.md)

If the user asks to transfer this workflow to another Codex account, Claude account, or generic agent environment, also read:

- [Portable Transfer Guide](references/portable-transfer.md)

When implementing this as a coded or semi-coded workflow, use:

- [Run State Schema](references/run-state-schema.json)

Then perform the workflow end to end:

1. Extract Canvas assignment details read-only.
2. Extract Canvas rubric read-only.
3. Read directed study material only if accessible in read-only mode.
4. Prepare source-grounded course material context.
5. Create local record files or an equivalent recovery record.
6. Fill CoEqual setup fields.
7. Convert the Canvas rubric into CoEqual dimensions.
8. Add instructor grading notes.
9. Review or rewrite benchmark answer to avoid hallucinations.
10. Run final QA.
11. Stop on the final CoEqual review screen and ask the user to click `Create assignment` directly.

At every stage, record a short verification result in the QA checklist. For normal runs, concise verification is enough; for failures or fallbacks, record the observed input, chosen action, decision level, and resulting output.

## Final Handoff Wording

Use this wording after final QA, with the CoEqual review screen open:

```text
I have reviewed the CoEqual setup and left the CoEqual review screen open. Please click Create assignment directly in CoEqual when you are ready. Canvas has not been edited.
```

## Canvas Integrity Statement

Repeat this statement in the final response:

```text
Canvas was used only for read-only extraction. I did not edit, save, publish, reply, grade, upload, delete, or change anything in Canvas.
```
