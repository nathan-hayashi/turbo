---
name: peer-interpret-feedback
description: "Run an independent peer interpretation of third-party feedback via codex. Returns structured intent interpretations without applying changes. Use when the user asks to \"peer interpret feedback\", \"get a second opinion on this feedback\", or \"interpret feedback independently\"."
---

# Peer Interpret Feedback

Independent feedback interpretation via codex. Delegates to `/codex-exec` for an interpretation of third-party feedback where the author's intent is ambiguous or the correctness of suggestions is uncertain.

## Step 1: Identify Feedback Items

Determine the feedback to interpret:

- If **feedback items** were provided, use them
- If a **file path or URL** was provided, read or fetch the content
- If **neither** was provided, look for feedback in the current conversation context

## Step 2: Run `/codex-exec` Skill

Run the `/codex-exec` skill in read-only mode with the feedback items, all available context, and this prompt:

```
<task>
Interpret the following third-party feedback items. For each item, determine: what the author most likely wants changed and why, whether the suggestion is technically sound, and where the phrasing is ambiguous enough to support multiple valid readings.
</task>

<dig_deeper_nudge>
Do not take feedback at face value. Check whether the author's stated concern matches the code reality. Look for cases where the reviewer misread the code, confused two similar constructs, or applied a general rule that does not fit this specific context.
</dig_deeper_nudge>

<structured_output_contract>
For each feedback item, return:
1. Intent — what the author most likely wants changed and why (one to two sentences)
2. Correctness — whether the suggestion is technically sound. If not, explain what the reviewer likely misunderstood, with evidence from the code
3. Ambiguity — if the intent supports multiple valid readings, list each reading and which has stronger evidence
4. Confidence — high (clear intent, sound suggestion), medium (likely intent but some uncertainty), or low (genuinely ambiguous or likely incorrect)
</structured_output_contract>
```

## Step 3: Return Interpretation

Return the codex interpretation output. The caller determines what to do with the interpretation.
