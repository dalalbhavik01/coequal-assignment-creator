# Model-Agnostic Error Escalation

This workflow is designed to run in Codex, ChatGPT, Claude, or another browser-capable AI environment.

The workflow does not call a separate external model by default. The model currently running the workflow is the escalation agent.

If the workflow is running in Claude, Claude handles the escalation. If it is running in Codex or ChatGPT, that model handles the escalation. If the environment supports subagents, the main agent may create a specialist subagent, but the same rules below still apply.

## Escalation Rule

When something goes wrong, the host model must classify the issue using the decision levels and produce a structured recovery decision.

It must not:

- Guess missing Canvas content.
- Invent course material.
- Round or change rubric values silently.
- Edit Canvas to solve an access or extraction problem.
- Continue to final CoEqual creation when verification failed.

## Escalation Input

Use this structure whenever the workflow needs smart error handling:

```json
{
  "stage": "[preflight | canvas_extraction | course_material | coequal_setup | rubric_mapping | instructor_notes | benchmark | final_qa | post_create]",
  "observed_problem": "[what failed or looked inconsistent]",
  "source_evidence": "[what was actually visible/readable]",
  "risk_area": "[canvas_integrity | rubric_accuracy | source_truth | privacy | coequal_creation | browser_session]",
  "available_safe_actions": [
    "[read-only retry]",
    "[use local recovery record]",
    "[ask user for source]",
    "[stop before creation]"
  ],
  "forbidden_actions": [
    "edit Canvas",
    "invent missing content",
    "change rubric values silently",
    "click final Create assignment without confirmation"
  ]
}
```

## Escalation Output

The host model must return:

```json
{
  "decision_level": "L0 | L1 | L2 | L3",
  "decision": "[continue | retry_read_only | use_safe_fallback | ask_user | hard_stop]",
  "reason": "[short explanation grounded in evidence]",
  "next_action": "[specific next action]",
  "canvas_integrity_preserved": true,
  "requires_user_input": true,
  "user_question": "[exact question to ask, or null]"
}
```

## Model-Specific Behavior

| Host environment | Escalation behavior |
|---|---|
| Codex | Main Codex agent classifies the issue, may use subagents when available, and records the decision. |
| ChatGPT | Main ChatGPT agent classifies the issue and asks the user when L2/L3 applies. |
| Claude | Main Claude agent classifies the issue using the same input/output contract and asks the user when L2/L3 applies. |
| Other agent | The agent must follow the same decision levels, input/output contract, and Canvas read-only rules. |

## When To Ask The User

Ask the user when:

- The issue affects rubric totals, rating labels, point values, or allowed scores.
- Canvas content cannot be verified in read-only mode.
- Directed course material is inaccessible and the assignment depends on it.
- CoEqual changes or fails to preserve entered values.
- The workflow reaches the final Create assignment action.

## When The Model Can Decide Alone

The host model may decide alone only when the fallback does not alter grading truth or Canvas integrity.

Examples:

- Retry a read-only page load once.
- Use Canvas prompt/rubric only when directed material is inaccessible and record that limitation.
- Remove unsupported details from a generated benchmark.
- Rebuild a CoEqual form from local recovery records after a browser refresh.

## Core Principle

Escalation means structured reasoning by the model currently running the workflow. It does not mean silently overriding the workflow. If the model cannot verify a safe path, it must stop and ask.
