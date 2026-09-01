# Agentic Architecture

This workflow should stay dynamic across courses, Canvas pages, CoEqual courses, rubrics, point scales, assignment types, and host AI models.

The only value that may be configured as a stable default is the target CoEqual course URL. Everything else must be extracted, verified, or supplied by the user for each run.

## Recommended Pattern

Use a framework-neutral state machine with guardrails, checkpoints, specialist agents, human-in-the-loop gates, and a structured error escalation contract.

This pattern can be implemented in Codex, ChatGPT, Claude, LangGraph, OpenAI Agents SDK, CrewAI, Microsoft Agent Framework, or another agent runtime.

## Dynamic State

Every run should maintain a state object like this:

```json
{
  "canvas_url": "[provided by user]",
  "coequal_course_url": "[configured default or user-provided]",
  "assignment": {
    "title": null,
    "type": null,
    "prompt": null,
    "submission_requirements": null,
    "due_structure": null,
    "total_points": null
  },
  "rubric": {
    "criteria": [],
    "ratings": [],
    "total_points": null,
    "exact_values_only": true
  },
  "course_material": {
    "links_found": [],
    "accessible_sources": [],
    "summary": null,
    "limitations": []
  },
  "coequal_setup": {
    "fields_entered": {},
    "readback_verified": false,
    "final_create_clicked_by": null,
    "created": false
  },
  "qa": {
    "canvas_read_only_verified": false,
    "rubric_verified": false,
    "coequal_verified": false,
    "final_user_handoff_complete": false,
    "user_clicked_final_create": false
  },
  "decision_log": []
}
```

## Agent Roles

The workflow can run as one agent for simple assignments. For complex assignments, use specialist agents or subagents.

| Agent | Responsibility | Cannot do |
|---|---|---|
| Orchestrator | Owns run state, resolves conflicts, asks user when required. | Cannot ignore failed verification. |
| Canvas Reader | Extracts Canvas prompt, requirements, rubric, and links in read-only mode. | Cannot edit, save, publish, reply, grade, upload, delete, or change Canvas. |
| Course Material Grounder | Summarizes accessible directed materials using source evidence only. | Cannot invent article or course details. |
| Rubric Mapper | Converts Canvas rubric into CoEqual dimensions and exact levels. | Cannot round, average, or add in-between scores unless the Canvas rubric has them. |
| CoEqual Builder | Fills CoEqual fields and reads them back. | Cannot click final Create assignment; must leave final click to the user. |
| Benchmark Reviewer | Removes unsupported benchmark claims and keeps benchmark flexible. | Cannot turn benchmark into the only correct answer. |
| Final Verifier | Checks all critical fields before final user handoff. | Cannot pass the run if source/rubric/Canvas checks fail. |
| Error Escalation Agent | Classifies failures and returns structured recovery decisions. | Cannot override Canvas read-only or source-truth rules. |

## Canvas Read-Only Guardrail

Before every Canvas browser action, classify it:

```json
{
  "site": "Canvas",
  "intended_action": "[click | scroll | read | download | open link | type | submit]",
  "read_only_safe": true,
  "mutation_risk": false,
  "allowed": true
}
```

Allowed Canvas actions:

- Open Canvas assignment/discussion pages.
- Scroll.
- Expand read-only content.
- Open rubric display/view.
- Open/download directed course material when it is a normal read/download action.
- Copy visible text for extraction.

Forbidden Canvas actions:

- Edit.
- Save.
- Publish or unpublish.
- Reply/post/comment.
- Grade or change grades.
- Upload files.
- Delete files or content.
- Change due dates, modules, rubrics, assignments, discussions, pages, or SpeedGrader state.
- Submit forms on Canvas.

If the action is not clearly read-only, decision level is L3 for that action and the agent must stop.

## Error Handling Standard

It is not realistic to enumerate every possible browser, Canvas, CoEqual, source, or model failure. Instead, the workflow handles all cases by enforcing invariants.

Non-negotiable invariants:

- Canvas remains read-only.
- Rubric labels, descriptions, point values, and totals are exact.
- Course material is source-grounded.
- No missing student/assignment work is invented.
- CoEqual final creation is handed off to the user on the final review screen.
- Any unverified source/rubric/final-handoff issue stops the workflow.

For unlisted failures, the host model must:

1. Identify the workflow stage.
2. Identify the risk area.
3. List safe actions.
4. List forbidden actions.
5. Choose L0, L1, L2, or L3.
6. Record the decision.
7. Ask the user if the issue affects Canvas integrity, source truth, rubric truth, privacy, or final creation ownership.

## Framework Mapping

| Framework/pattern | Useful part for this workflow | Suggested use |
|---|---|---|
| LangGraph-style state graph | Durable state, checkpoints, interrupts, human-in-the-loop gates. | Best pattern if building a real coded workflow runner. |
| OpenAI Agents SDK-style guardrails | Tool guardrails, handoffs, tracing, and tripwire stops. | Best pattern for OpenAI/Codex implementation. |
| CrewAI-style crews and flows | Specialist agents, structured flows, memory, guardrails, observability. | Good if implementing a Python multi-agent version. |
| Microsoft Agent Framework-style workflows | Agent orchestration, checkpoints, resuming, human-in-the-loop. | Good for enterprise or cross-runtime implementation. |
| AutoGen-style multi-agent chat | Conversable specialist agents and tool-using workflows. | Useful conceptually; for new Microsoft-based builds, prefer Microsoft Agent Framework. |

## Recommended Implementation Choice

Keep this repository framework-neutral for portability.

Use these concepts immediately:

- State machine stages.
- Canvas read-only guardrail before every Canvas action.
- Checkpoint after every stage.
- Specialist agents/subagents only when complexity requires them.
- Structured escalation input/output.
- Final user handoff gate.
- Decision log and verification checklist.

If this becomes a coded product later, implement it first as a LangGraph-style state graph because this task needs checkpoints, resumability, verification gates, and human-in-the-loop approvals.

If staying inside OpenAI/Codex, model it after OpenAI Agents SDK guardrails, tracing, and handoffs.

If transferring to Claude, paste the portable workflow plus the model-escalation contract; Claude becomes the host escalation agent and must follow the same state, guardrail, and verification rules.

## Supporting Files

- [canvas-read-only-policy.md](canvas-read-only-policy.md): exact Canvas action rules.
- [comprehensive-error-handling-matrix.md](comprehensive-error-handling-matrix.md): failure classes, decision levels, safe responses, and forbidden responses.
- [run-state-schema.json](run-state-schema.json): portable state schema for coded or no-code implementations.
