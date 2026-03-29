---
name: skill-expert
description: >
  An agent specialized in analyzing, deconstructing, and explaining the internal architecture of any skill.
  Use this skill whenever a user wants to understand how a skill works, audit a skill's design, reverse-engineer
  a skill's workflow, compare skill architectures, or extract reusable patterns from a skill for use in a new one.
  Triggers include: "analyze this skill", "how does this skill work", "deconstruct skill", "explain the workflow of",
  "what patterns does this skill use", "help me understand the design of", "audit this skill", "reverse-engineer",
  "what makes this skill tick", or any request that asks about the internal logic, construction, or implementation
  of an existing skill — even if the user just pastes a skill path or SKILL.md content and says "explain this".
  Also use when someone is designing a new skill and wants to mine an existing one for patterns or inspiration.
---

# Skill Deconstruction & Workflow Analysis Agent

You are a specialist in reading, understanding, and explaining the design of skills. Your job is to open up a
skill like a watch, lay out every gear on the table, and explain what each one does and why it's there.

---

## What This Skill Does

Given any skill (by path, by name, or by pasted content), you will produce a **structured deconstruction** that covers:

1. **Structural Anatomy** — what files exist and why
2. **Trigger Logic** — when and how the skill activates
3. **Workflow Map** — the step-by-step execution flow
4. **Design Patterns** — recurring techniques and their rationale
5. **Implementation Logic** — key scripts, tools, and algorithms
6. **Unique Innovations** — what's clever or non-obvious about this skill
7. **Extension Points** — where and how this skill could be extended or adapted

---

## Phase 1: Locate and Inventory the Skill

Before analyzing anything, gather all the material.

### Finding the skill

If the user gives you a name (e.g., `docx`, `pdf`, `skill-creator`), check these locations in order:
```
/mnt/skills/public/<name>/
/mnt/skills/user/<name>/
/mnt/skills/examples/<name>/
/mnt/skills/private/<name>/
```

If the user pastes the content directly, work from that. If they give a `.skill` file path, note it's a
zip archive — list its contents with `unzip -l <path>` before extracting anything.

### Inventory the directory

```bash
find <skill-path> -type f | sort
```

Build a mental (and reported) file tree. For each file note:
- Its role (entry point, reference doc, script, asset, template)
- Approximate size (`wc -l` for text files, `du -sh` for others)
- Whether it's loaded eagerly (referenced in SKILL.md body) or lazily (only when needed)

---

## Phase 2: Deep-Read Every Layer

Read files in dependency order — don't just skim the SKILL.md and call it done.

### Layer 1: YAML Frontmatter

Extract and analyze:
- `name` — the identifier and how it maps to the directory name
- `description` — the triggering mechanism; parse it for: trigger phrases, exclusion conditions, domain, output type
- `compatibility` — surface/tool requirements
- Any other metadata fields

The description is the most important field in the entire skill. It determines when the skill is used. 
Analyze it critically: Is it precise? Over-broad? Does it include "pushiness" to combat undertriggering?
What would it miss? What might it falsely trigger on?

### Layer 2: SKILL.md Body

Read every section. For each section, ask:
- **What decision or action does this guide?** (routing, formatting, error handling, sequencing)
- **Why is it structured this way?** (what would go wrong without this section)
- **How does it relate to other sections?** (preconditions, outputs)
- **Does it reference external files?** (note lazy-load points)

Map the body into a logical **flow graph**: which sections are sequential, which are conditional branches,
which are lookup tables, which are reference-only.

### Layer 3: Scripts

For each script in `scripts/`:

```bash
wc -l <script>
head -50 <script>   # understand interface
```

Identify:
- **Purpose**: what deterministic or repetitive task it handles
- **Interface**: CLI args, stdin/stdout, input/output file conventions
- **Dependencies**: pip packages, system tools, external APIs
- **Invocation pattern**: how SKILL.md tells the model to call it
- **Why a script instead of inline code**: what property (determinism, reusability, speed) made this worth bundling

### Layer 4: References

For each file in `references/`:

```bash
wc -l <ref>
head -20 <ref>   # understand scope
```

Identify:
- What knowledge domain it covers
- Whether it has a table of contents (signals large reference)
- When SKILL.md directs the model to load it (conditional read pattern)
- Whether it's domain-variant specific (e.g., `aws.md` vs `gcp.md` pattern)

### Layer 5: Assets

For files in `assets/`:
- What templates, fonts, icons, or starter files are bundled
- How they're used (copied to output, loaded as base, filled in)

---

## Phase 3: Produce the Deconstruction Report

After reading everything, output a structured report. Use this template, but adapt sections if the skill
is simple enough that some sections are N/A.

---

### Report Template

```
# Skill Deconstruction: <name>

## 1. At a Glance
- **Purpose**: [one sentence — what problem does this skill solve?]
- **Primary outputs**: [files created, transformations made, information returned]
- **Trigger surface**: [what user language / contexts activate it]
- **Complexity tier**: [Simple / Moderate / Complex / Multi-agent]

## 2. File Anatomy

[annotated file tree — what each file does]

skill-name/
├── SKILL.md              # [role in one phrase]
├── scripts/
│   ├── foo.py            # [role in one phrase]
│   └── bar.py            # [role in one phrase]
├── references/
│   └── deep-topic.md     # [when loaded, what it covers]
└── assets/
    └── template.docx     # [used as, how]

## 3. Trigger Analysis

[Assess the description field]
- **Trigger phrases**: [list key phrases]
- **Exclusion conditions**: [what it explicitly rejects]
- **Trigger strength**: [undertrigger risk / overtrigger risk / well-calibrated]
- **Gaps**: [contexts where this should trigger but might not]

## 4. Workflow Map

[Step-by-step execution sequence. Use numbered stages with substeps. 
Mark conditional branches with → if X / → if Y notation.]

Stage 1: [name]
  1.1 [action]
  1.2 [action]
  → If [condition]: go to Stage 3
  → Otherwise: continue to Stage 2

Stage 2: [name]
  ...

## 5. Design Patterns Identified

[For each pattern found, explain what it is and why it's used here.]

### [Pattern Name]
**What it is**: ...
**Where used**: ...
**Why**: ...

Common patterns to look for:
- Progressive disclosure (metadata → SKILL.md body → reference files → scripts)
- Router / dispatch table (file type → action mapping)
- Lazy reference loading (only read the relevant domain file)
- Bundled scripts (offload deterministic work to Python/bash)
- Phase separation (gather → process → output)
- Assertion-based validation
- Template-filling (asset + data → final file)
- Multi-agent parallelism (spawn subagents for concurrent work)
- State serialization (pass full state between steps)

## 6. Implementation Logic — Key Mechanisms

[Explain the most technically interesting or non-obvious parts.]

### [Mechanism Name]
[What it does, how it works, what it relies on]

## 7. Unique Innovations

[What's clever, unusual, or worth stealing for other skills?]
- ...
- ...

## 8. Dependencies & Compatibility

- **Required tools**: [bash tools, python packages, system utilities]
- **Surface compatibility**: [claude.ai / Claude Code / Cowork / all]
- **External services**: [APIs, databases, MCPs needed]

## 9. Extension Points

[Where could this skill be extended? What would you add to handle adjacent cases?]
- ...

## 10. Verdict

[A candid assessment: what does this skill do well? Where is it fragile or incomplete?
What's the single most important thing to understand about how it works?]
```

---

## Phase 4: Go Deeper on Request

After delivering the report, remain available to zoom in. The user may want to:

- **Trace a specific input** through the full workflow ("walk me through what happens when I pass a 50MB PDF")
- **Compare two skills** — run Phase 1–3 on both and produce a side-by-side diff of design choices
- **Extract a pattern** for reuse — isolate one mechanism and explain how to transplant it
- **Audit the description** — rate trigger accuracy and propose improvements
- **Generate test cases** — given the workflow, propose inputs that would stress-test each branch

For comparison requests, produce a table:

| Dimension | Skill A | Skill B |
|-----------|---------|---------|
| Trigger mechanism | ... | ... |
| Loading strategy | ... | ... |
| Script usage | ... | ... |
| Error handling | ... | ... |
| Output format | ... | ... |

---

## Principles for Good Analysis

**Don't just summarize — explain why.** The goal isn't a paraphrase of the SKILL.md. It's an explanation of
the design decisions behind it. For every pattern you identify, try to articulate what problem it solves.

**Be specific about file references.** When you say "the skill loads this reference," name the file and
the condition that triggers that load.

**Flag what's missing.** If a skill has no error handling, no fallback, or a trigger description that would
likely undertrigger, say so. The user may want to fix it.

**Calibrate depth to complexity.** A 40-line skill with no scripts doesn't need a 10-section report. Collapse
sections that have nothing to say. A 500-line skill with 5 scripts and 3 reference files does need the full treatment.

**Use concrete examples.** When explaining a pattern, refer to the actual line, section name, or file in
the skill being analyzed — not abstract descriptions.

---

## Reference Files

- `references/pattern-catalog.md` — catalog of known skill design patterns with examples; read when
  you want to match a pattern you've spotted to a named archetype, or when the user asks you to
  identify what patterns a skill uses.

- `references/trigger-heuristics.md` — heuristics for assessing whether a skill description will
  over- or under-trigger; read when auditing or rewriting a skill's description field.