# Canvas-to-CoEqual Workflow

## Purpose

Create a CoEqual assignment from a Canvas assignment/discussion link accurately, with no Canvas edits and no hallucinated grading content.

## Dynamic Configuration

Treat the workflow as configurable for every run. Do not assume the same course, LMS page shape, rubric size, point scale, assignment type, browser tool, account, or model will repeat.

Run configuration:

```json
{
  "canvas_url": "[required]",
  "coequal_course_url": "[required unless configured default applies]",
  "output_root": "outputs",
  "canvas_mode": "read_only",
  "coequal_mode": "prepare_and_stop_before_create",
  "grading_defaults": {
    "approach": "Absolute Standards",
    "style": "Generous",
    "feedback": "Brief & Direct",
    "borderline": "Benefit of the Doubt",
    "scores_between_rubric_levels_allowed": false
  },
  "course_material_policy": "source_grounded_summary_only",
  "subagent_mode": "auto_when_complex"
}
```

## Operating Modes

Single-agent mode is suitable for simple assignments with short prompts and clear rubrics.

Subagent mode is suitable when the assignment is complex, the reading is long, the rubric has many criteria, the page has multiple sections, or grading risk is high.

Recommended subagents:

- Canvas Extraction Subagent: extracts prompt, requirements, and rubric only.
- Course Material Grounding Subagent: summarizes directed materials from visible/source-supported content only.
- Rubric QA Subagent: compares Canvas rubric against CoEqual dimensions and scores.
- Benchmark Review Subagent: removes unsupported benchmark details and prevents answer-key overfitting.
- Final QA Subagent: reviews the completed setup before the main agent asks for creation approval.

The main agent remains responsible for resolving conflicts, keeping Canvas read-only, and asking before final CoEqual creation.

## Canvas Read-Only Extraction

Extract:

- Course name and section
- Assignment title
- Assignment type
- Overview
- Prompt
- Guidelines
- Submission instructions
- Due structure, if visible
- Rubric criteria
- Rating labels
- Rating descriptions
- Exact rating point values
- Total points

Verify:

- The Canvas assignment title matches the user's requested link.
- The rubric total is captured.
- Canvas was not edited.
- The exact Canvas URL, page title, extracted assignment title, total points, criteria count, and read-only actions are recorded.

## Directed Course Material Grounding

If directed study material is linked and accessible:

- Open it read-only.
- Create a short summary in original words.
- Include only source-supported concepts.
- Do not copy long passages.
- Do not invent examples, frameworks, or article details.

If material is not accessible:

- Use only the Canvas prompt/rubric and user-provided files.
- Mark the course material as Canvas-grounded only.

Course material format:

```text
Course Material Summary:
[Short source-grounded summary from the directed material.]

Key Concepts Present in the Material:
- [Concept verified from source]
- [Concept verified from source]
- [Concept verified from source]

Use for Grading:
Use this only as background context. Do not require students to mention every point unless the assignment prompt or rubric explicitly requires it. Students may earn full credit through different thoughtful interpretations.

Do Not Assume:
Do not add facts beyond the assignment prompt, rubric, student submission, and provided course material. Do not treat this summary as a checklist unless the assignment says so.
```

## CoEqual Setup

Typical choices:

- Assignment title: exact Canvas title
- Total points: exact Canvas rubric total
- Cohort size: based on enrollment count or visible assignment context
- Level: MBA unless source context says otherwise
- Subject type: Qualitative for discussion/reflection/case/strategy/marketing/OB/negotiation/open-ended work
- Assignment type: Discussion Post for Canvas discussions with initial post and replies
- Grading approach: Absolute Standards
- Grading style: Generous when the user asks for lenient grading
- Feedback depth: Brief & Direct unless the user requests otherwise
- Borderline cases: Benefit of the Doubt

After filling each field, read it back from CoEqual.

## Rubric Mapping

- Canvas criterion = CoEqual dimension.
- Canvas criterion max points = CoEqual dimension points.
- Canvas rating = CoEqual level.
- Rating label, description, and point value must match Canvas exactly.
- Remove default CoEqual dimensions that are not in Canvas.
- Add CoEqual levels when Canvas has more levels than the default.
- CoEqual rubric total must equal Canvas rubric total.
- Do not round rubric values.
- Do not create in-between scores.
- CoEqual is expected to support Canvas decimal rubric values. If any value is unexpectedly changed during readback, stop and ask the user.

## Instructor Grading Notes

Use this base structure and add assignment-specific requirements:

```text
Grade strictly according to the Canvas rubric, but be lenient in interpretation. Use only the rubric rating scores listed below; do not assign grades between rubric levels. Final score must be the sum of the selected rubric ratings.

For each rubric criterion, grade only the work that is actually visible in the submitted/extracted student data. Do not invent missing posts, replies, files, calculations, citations, examples, or explanations.

Award full credit when the response meaningfully satisfies the rubric, even if the wording, organization, style, formatting, or grammar is not perfect. Do not penalize minor grammar, spelling, formatting, tone, or organization issues when the substance is clear.

Use Sufficient/Minimal/Unacceptable only when the submission clearly falls into those rubric descriptions. Give students the benefit of the doubt at category boundaries, but do not ignore missing required work.

This is an open-ended assignment. Do not require one single benchmark interpretation as the only correct answer unless the prompt explicitly requires a specific answer.

Flag unusual, incomplete, contradictory, or borderline cases for instructor review instead of making a harsh deduction.
```

## Benchmark Control

If CoEqual generates a benchmark:

- Review it before continuing.
- Remove unsupported article or course details.
- Keep it broad enough for open-ended responses.
- Do not let it become an answer key.

Add this line at the top:

```text
This benchmark is an example of an Excellent response, not a required template. Grade students against the Canvas rubric and instructor grading notes, not against this wording as the only correct answer.
```

## Verification Stages Summary

| Stage | Verification output |
|---|---|
| Preflight | Canvas URL, CoEqual URL, account/course target, grading defaults, read-only policy |
| Canvas extraction | Title, prompt captured, rubric captured, total points, no Canvas edits |
| Course material | Accessible/inaccessible, source-grounded summary, unsupported claims removed |
| Local records | Requirements file, material file, QA checklist created and checked |
| CoEqual setup | All setup fields read back and matched |
| Rubric mapping | Criteria count, labels, descriptions, point values, total verified |
| Instructor notes | Exact-score rule, leniency rule, no-invention rule verified |
| Benchmark | Source-grounded, flexible, not an answer key |
| Final QA | Status, decision log, recovery files, final user confirmation request |
| Post-create, only after confirmation | Assignment URL/id, created status, Canvas unchanged |
