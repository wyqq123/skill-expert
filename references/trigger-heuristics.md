# Trigger Heuristics for Skill Descriptions

A reference for assessing and improving the `description` field in a skill's YAML frontmatter.
Load this file when you are auditing a skill's trigger behavior, diagnosing undertriggering or
overtriggering, or rewriting a skill's description.

---

## Why the Description Field Is Critical

The description is the **only signal** Claude uses to decide whether to consult a skill. Claude sees
the skill name + description in an `<available_skills>` list and decides from that alone whether this
skill is relevant to the current task. The skill body, scripts, and references are invisible until
after the trigger fires.

This means:
- An accurate but boring description → undertriggering (Claude handles it itself instead)
- A vague, over-broad description → overtriggering (Claude consults the skill for unrelated tasks)
- A well-calibrated description → the skill fires exactly when it should

---

## Anatomy of a Good Description

A well-formed description contains all of these components:

```
[What the skill does] + [When to trigger] + [Specific trigger phrases] + [Exclusion conditions]
```

Example (file-reading):
> "Use this skill when a file has been uploaded but its content is NOT in your context — only its path
> at /mnt/user-data/uploads/ is listed in an uploaded_files block. This skill is a router: it tells you
> which tool to use for each file type (pdf, docx, xlsx, csv, json, images, archives, ebooks) so you
> read the right amount the right way instead of blindly running cat on a binary. Triggers: any mention
> of /mnt/user-data/uploads/, an uploaded_files section, a file_path tag, or a user asking about an
> uploaded file you have not yet read. Do NOT use this skill if the file content is already visible in
> your context inside a documents block — you already have it."

What makes this good:
- States the exact condition ("content is NOT in your context")
- Names the artifact that signals the condition ("uploaded_files block")
- Lists specific trigger signals ("any mention of /mnt/user-data/uploads/")
- Includes a hard exclusion ("Do NOT use if… already visible")

---

## Undertrigger Heuristics

A skill is likely to **undertrigger** (not fire when it should) if the description:

| Symptom | Example | Fix |
|---------|---------|-----|
| Too abstract | "Helps with file processing" | Name concrete file types, extensions, user phrasings |
| No trigger phrases | Just describes the output, not when to use | Add "Triggers include: X, Y, Z" |
| Passive voice | "Can be used when..." | Rewrite as "Use this skill when..." |
| Too narrow | Mentions only one specific phrasing | Add paraphrases, synonyms, adjacent contexts |
| No "pushiness" | Neutral, informational tone | Add "Make sure to use this skill even if the user doesn't explicitly ask for it" |
| Missing implicit triggers | Skill is for PDFs but doesn't mention "document", "report", "file" | Add domain synonyms |
| Doesn't name the output | User asks for "a chart" but description says "data visualization" | Match the user's vocabulary |

---

## Overtrigger Heuristics

A skill is likely to **overtrigger** (fire when it shouldn't) if the description:

| Symptom | Example | Fix |
|---------|---------|-----|
| Too broad | "Use for any data task" | Narrow to specific file types or workflows |
| Missing exclusions | No "Do NOT use when..." clauses | Add explicit exclusion for adjacent cases |
| Overlaps with built-in Claude capability | "Use for writing Python code" | Skills shouldn't compete with things Claude does natively — scope to the specific pipeline |
| Trigger phrases too generic | "word", "document", "file" | Add qualifiers: "Word .docx files", "uploaded file at /mnt/user-data/uploads/" |
| Doesn't distinguish from similar skills | Two skills both claim "PDF handling" | Each should carve out its specific domain (creation vs. reading) |

---

## Heuristics for Specific Skill Types

### File transform skills (docx, xlsx, pdf, pptx)
Good triggers:
- Named file extension (`.docx`, `.xlsx`)
- Upload path (`/mnt/user-data/uploads/`)
- Action verbs specific to this format ("convert to Word", "add a sheet", "fill in the PDF form")
- The format's colloquial name ("Word doc", "Excel file", "spreadsheet")

Common undertrigger gap: User says "report" or "document" without saying "Word" or ".docx".
Fix: Add "word document, report, memo, letter, template" to trigger list.

### Analysis/research skills
Good triggers:
- Task type ("research", "summarize", "compare", "audit")
- Subject matter (the domain the skill is specialized in)
- Explicit output format ("produce a report", "create a summary")

Common overtrigger gap: Research skill triggers on any "summarize" request, even ones Claude can handle inline.
Fix: Scope to "multi-source research", "cross-reference multiple documents", "requires web search + synthesis".

### Agent/workflow skills (skill-creator, multi-step pipelines)
Good triggers:
- Phase names ("create a skill", "run evals", "optimize the description")
- Workflow vocabulary ("iteration", "test cases", "benchmark")
- Output artifact types ("produce a .skill file", "package the skill")

Common undertrigger gap: User says "make a skill" but description says "create new skills" — slight phrasing mismatch.
Fix: Add "make", "build", "design", "set up" as synonyms for "create".

### Meta/analysis skills (like this one)
Good triggers:
- Introspective verbs ("analyze", "deconstruct", "explain how", "understand", "audit", "reverse-engineer")
- Target noun ("skill", "SKILL.md", "workflow", "design", "implementation")
- Comparative requests ("compare these two skills", "what patterns does X use")

Common undertrigger gap: User pastes a SKILL.md and just says "explain this" — too short to match.
Fix: Add "even if the user just pastes a skill path or SKILL.md content and says 'explain this'."

---

## Testing a Description (Quick Audit)

To audit a description, mentally simulate 5 real user prompts that should trigger the skill, and 5 that
shouldn't, then check whether the description would distinguish them.

**Should trigger (test for undertrigger)**:
- Can you find the user phrases that match the description?
- If the user phrases their request differently, would it still match?

**Should not trigger (test for overtrigger)**:
- Is there anything in the description vague enough to match unrelated tasks?
- Could the adjacent skill or built-in Claude capability handle this instead?

---

## Rewriting a Description: Template

Use this template as a starting point when rewriting:

```
[Skill type verb: "Use this skill" / "An agent specialized in"] [primary action] [on/for] [primary artifact or domain].

[Optional: what the skill uniquely does that Claude alone cannot.]

Use when [concrete condition 1], [concrete condition 2], or [concrete condition 3].

Triggers include: "[phrase 1]", "[phrase 2]", "[phrase 3]", or any request that [abstract description of intent]
— even if [common edge case that should still trigger].

Do NOT use [exclusion 1] or [exclusion 2].
```

---

## Quick Reference: Trigger Strength Ratings

| Rating | Description |
|--------|-------------|
| ✅ Well-calibrated | Specific trigger conditions, clear exclusions, good phrase coverage |
| ⚠️ Undertrigger risk | Too abstract, missing trigger phrases, no "pushiness" |
| ⚠️ Overtrigger risk | Too broad, missing exclusions, overlaps with built-ins or adjacent skills |
| ❌ Needs rewrite | Describes output only, no trigger conditions at all |