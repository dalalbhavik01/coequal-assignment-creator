# Canvas Read-Only Policy

Canvas must always remain intact. This workflow may read Canvas, but it must not change Canvas.

## Non-Negotiable Rule

If a Canvas action could edit, submit, save, post, publish, grade, upload, delete, or change state, the action is forbidden unless the user separately gives explicit permission for that exact Canvas edit. Assignment creation in CoEqual does not grant permission to edit Canvas.

## Allowed Canvas Actions

These actions are read-only and normally allowed:

- Open a Canvas course, assignment, discussion, rubric, module item, page, file preview, or linked reading.
- Scroll the page.
- Expand visible read-only sections.
- Open a rubric display/view.
- Open a linked source or file in read-only mode.
- Download a linked course file for local text extraction when the user has access.
- Copy visible text for extraction.
- Use browser find/search inside the current page.

## Forbidden Canvas Actions

These actions are forbidden during this workflow:

- Click Edit.
- Click Save, Submit, Post, Reply, Publish, Unpublish, Delete, Remove, Upload, or Update.
- Change due dates, availability dates, points, titles, descriptions, modules, rubrics, pages, assignments, files, discussions, groups, sections, or SpeedGrader state.
- Enter text into Canvas forms.
- Attach or upload files to Canvas.
- Change grades, feedback, comments, rubrics, or submission status.
- Trigger bulk actions or settings changes.
- Accept a Canvas warning that says the action will change content or settings.

## Action Gate

Before every Canvas action, the agent must classify it:

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

If any field cannot be confidently completed, use:

```json
{
  "site": "Canvas",
  "stage": "[workflow stage]",
  "intended_action": "[specific action]",
  "read_only_safe": false,
  "mutation_risk": true,
  "allowed": false,
  "decision_level": "L3",
  "next_action": "Stop and ask the user for a read-only source or confirmation."
}
```

## Safe Alternatives

If needed information is behind a risky Canvas control:

- Try a read-only page view.
- Try the rubric display view.
- Try an accessible file preview or download.
- Ask the user for a screenshot, copied text, exported rubric, or assignment PDF.
- Stop and record the limitation.

Do not click the risky control just to inspect what is behind it.

## Final Canvas Integrity Statement

Every run must end with this statement:

```text
Canvas was used only for read-only extraction. I did not edit, save, publish, reply, grade, upload, delete, or change anything in Canvas.
```
