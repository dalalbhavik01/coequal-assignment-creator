# Canvas Read-Only Policy

Canvas must always remain intact. This workflow may read Canvas, but it must not change Canvas.

Allowed actions:

- Open Canvas pages, assignments, discussions, rubrics, files, and linked readings.
- Scroll.
- Expand visible read-only content.
- View rubric displays.
- Download accessible course files for extraction.
- Copy visible text.

Forbidden actions:

- Edit, save, submit, post, reply, publish, unpublish, delete, remove, upload, update, grade, or change Canvas state.
- Change due dates, modules, rubrics, assignments, discussions, pages, files, groups, sections, or SpeedGrader.
- Enter text into Canvas forms.
- Accept Canvas warnings that indicate content/settings will change.

Before every Canvas action, classify it:

```json
{
  "site": "Canvas",
  "stage": "[workflow stage]",
  "intended_action": "[specific action]",
  "read_only_safe": true,
  "mutation_risk": false,
  "allowed": true,
  "reason": "[why it is read-only]"
}
```

If the action is not clearly read-only, stop with L3 for that action.

Final statement:

```text
Canvas was used only for read-only extraction. I did not edit, save, publish, reply, grade, upload, delete, or change anything in Canvas.
```
