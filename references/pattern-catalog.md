# Skill Design Pattern Catalog

A reference catalog of recurring design patterns found in well-constructed skills.
Load this file when you want to name a pattern you've identified, or when the user
asks "what design patterns does this skill use?"

---

## Table of Contents

1. Progressive Disclosure
2. Router / Dispatch Table
3. Lazy Reference Loading
4. Bundled Script Offload
5. Phase Separation
6. Domain Variant Fan-out
7. Template Filling
8. State Serialization
9. Multi-Agent Parallelism
10. Assertion-Based Validation
11. Conditional Early Exit
12. Pushiness Anti-Undertrigger
13. Mandatory Pre-Read Gate
14. Sampling Before Slurping
15. Annotated File Tree

---

## 1. Progressive Disclosure

**What it is**: Skill content is organized in three layers of increasing detail and decreasing likelihood of
being needed: (1) YAML frontmatter metadata always in context, (2) SKILL.md body loaded when skill triggers,
(3) reference/script files loaded only when needed.

**Signature**: SKILL.md says "read `references/foo.md` when you need to handle X" — the instruction to
read appears inline, but the content is deferred.

**Why it works**: Keeps the always-in-context footprint small (~100 words) while allowing deep knowledge
to be accessible on demand. Prevents context bloat for simple cases.

**Example**: `pdf/SKILL.md` references `pdf/REFERENCE.md` for advanced options, `pdf/FORMS.md` for form
handling. Most invocations never need those files.

---

## 2. Router / Dispatch Table

**What it is**: A lookup table (usually markdown table) that maps an input property (file extension, platform,
content type) to a specific action or sub-path.

**Signature**: A markdown table with one column as the key (e.g., Extension) and another as the value
(e.g., First move).

**Why it works**: Replaces complex if/else logic with a scannable reference the model can consult at a glance.
Reduces ambiguity for decision points that have many cases.

**Example**: `file-reading/SKILL.md` has a dispatch table mapping `.pdf`, `.docx`, `.xlsx`, `.csv`, `.zip`
etc. to specific read strategies and whether a dedicated skill exists.

---

## 3. Lazy Reference Loading

**What it is**: Reference files exist in `references/` but are not read eagerly. The SKILL.md body contains
an explicit instruction: "if the user needs X, read `references/x.md` first."

**Signature**: Phrases like "Read this file when…", "Before handling Y, consult…", "Only load if…"

**Why it works**: Large reference files (300+ lines) would bloat context if always loaded. Deferring load
until needed balances depth with efficiency.

**Example**: `skill-creator/SKILL.md` defers to `agents/grader.md`, `agents/comparator.md`, and
`agents/analyzer.md` — each read only when the corresponding sub-workflow is needed.

---

## 4. Bundled Script Offload

**What it is**: Deterministic, repetitive, or computationally intensive work is extracted into a Python or
bash script in `scripts/`, rather than described as inline instructions.

**Signature**: `scripts/` directory with `.py` or `.sh` files; SKILL.md contains invocation examples like
`python scripts/foo.py --input <path> --output <path>`.

**Why it works**: Scripts are faster than re-inventing logic each time, more reliable (no LLM hallucination
of the algorithm), reusable across test cases, and testable independently. The model just calls the script;
it doesn't have to re-derive the logic.

**Example**: `docx/scripts/create_docx.py` handles all the python-docx boilerplate. Every invocation
uses the same script rather than regenerating the document-building code.

**When to use**: When the same multi-step algorithm (file parsing, format conversion, data aggregation)
would be written identically in every invocation. Rule of thumb: if 3 test cases would all independently
produce the same helper script, bundle it.

---

## 5. Phase Separation

**What it is**: The skill explicitly labels distinct stages of its workflow (e.g., Gather → Process → Output)
and keeps them cleanly separated so the model can orient itself and the user can follow along.

**Signature**: Sections named "Phase 1: ...", "Stage 1: ...", or sequential headers that map to distinct
execution moments.

**Why it works**: Without phase labels, models tend to conflate information gathering with action, or start
outputting before they have everything they need. Explicit phases enforce the right order.

**Example**: `skill-creator/SKILL.md` separates: Capture Intent → Interview → Write SKILL.md → Test →
Grade → Improve → Package. Each has its own section with clear entry conditions.

---

## 6. Domain Variant Fan-out

**What it is**: A single skill supports multiple platforms, frameworks, or target types. The SKILL.md
handles routing and common logic; domain-specific details live in separate reference files, only one of
which is loaded per invocation.

**Signature**: `references/aws.md`, `references/gcp.md`, `references/azure.md` — or similar parallel files.
SKILL.md contains a selection step: "ask the user which platform, then read only that file."

**Why it works**: Avoids a monolithic SKILL.md that tries to cover every variant in one document. Keeps
context clean — you only load the knowledge you need for this invocation.

**Example**: A hypothetical `cloud-deploy` skill fans out to `aws.md`, `gcp.md`, `azure.md`. The pptx
skill separates `pptxgenjs.md` (generation) from `editing.md` (modification).

---

## 7. Template Filling

**What it is**: A pre-built template file (`.docx`, `.html`, `.xlsx`, `.pptx`) lives in `assets/` and
is used as the starting point. The skill fills in data rather than building from scratch.

**Signature**: `assets/template.*`; instructions like "copy the template, then fill in sections X, Y, Z."

**Why it works**: Templates encode formatting, branding, and structural decisions that would be tedious
to specify via instructions. The model only needs to handle the variable content.

**Example**: `skill-creator/assets/eval_review.html` — a pre-built HTML evaluation viewer that just has
two placeholders swapped in (`__EVAL_DATA_PLACEHOLDER__`, `__SKILL_NAME_PLACEHOLDER__`).

---

## 8. State Serialization

**What it is**: For multi-step or multi-agent workflows, the full application state is serialized to JSON
and passed explicitly with each step. No implicit memory is assumed between turns or agents.

**Signature**: Instructions include example JSON blobs representing state; the skill tells the model to
"include all relevant state in each request."

**Why it works**: LLMs have no persistent memory between API calls. Explicit state serialization ensures
that each step has everything it needs to proceed correctly, regardless of context window history.

**Example**: Game loop skills pass `{ player: {...}, history: [...] }` with every turn. `skill-creator`
passes the full conversation history when using MCP servers.

---

## 9. Multi-Agent Parallelism

**What it is**: Independent subtasks are dispatched to subagents in the same turn, so they run concurrently
rather than sequentially.

**Signature**: "Spawn all runs in the same turn"; "Launch everything at once so it all finishes around
the same time."

**Why it works**: For skills with multiple independent test cases or processing branches, parallelism
dramatically reduces wall-clock time. A 5-test suite that takes 30s each completes in 30s total rather than 150s.

**When not to use**: On Claude.ai (no subagents). The skill should degrade gracefully: "no subagents
means run in series."

**Example**: `skill-creator` spawns with-skill AND baseline runs simultaneously for each test case.

---

## 10. Assertion-Based Validation

**What it is**: Outputs are validated against a set of explicit, machine-checkable assertions rather than
purely human review. Assertions are stored as structured data and graded by a grader agent or script.

**Signature**: `assertions` field in eval JSON; `grading.json` output with `passed`, `text`, `evidence` fields.

**Why it works**: Human review is qualitative and subjective. Assertions add an objective signal: does
the output file exist? Does it have the right number of rows? Does it contain the expected header? This
enables quantitative tracking across iterations.

**Example**: `skill-creator` defines assertions per eval and grades them to produce benchmark pass rates.

---

## 11. Conditional Early Exit

**What it is**: The skill contains explicit "stop here" instructions for common simple cases, preventing
the model from running a heavy multi-phase workflow when a lightweight response suffices.

**Signature**: "If X, just do Y and stop." "For simple factual queries, answer directly — no tools needed."

**Why it works**: Prevents over-engineering. A skill optimized for complex cases shouldn't force that
full apparatus on simple requests.

**Example**: `file-reading/SKILL.md` — "Do NOT use this skill if the file content is already visible
in your context inside a documents block — you already have it."

---

## 12. Pushiness Anti-Undertrigger

**What it is**: The description field is deliberately worded to be proactive about triggering — it includes
extra trigger phrases, adjacent contexts, and affirmative statements to combat the model's tendency to
not use a skill when it would be helpful.

**Signature**: Phrases like "Use this skill even when…", "Also trigger when…", "Make sure to use this skill
whenever…" in the description.

**Why it works**: Models tend to undertrigger skills — to handle things themselves rather than consulting
a skill. A pushy description overcorrects for this bias.

**Example**: `file-reading` description: "Triggers: any mention of /mnt/user-data/uploads/, an uploaded_files
section, a file_path tag, or a user asking about an uploaded file you have not yet read."

---

## 13. Mandatory Pre-Read Gate

**What it is**: The SKILL.md opens with an explicit instruction to read a specific file before doing
anything else. This forces context loading upfront rather than discovering missing information mid-task.

**Signature**: "Before writing any code, read `references/foo.md`." "Your first action must be to view X."

**Why it works**: Prevents the model from starting a task with incomplete information and then having to
backtrack. The pre-read gate ensures the model has everything it needs before the first output.

**Example**: The skill system's own meta-instruction: "please begin the response to each request by using
the `view` tool to read the appropriate SKILL.md files."

---

## 14. Sampling Before Slurping

**What it is**: Before reading a potentially large file, the skill instructs the model to first check
the file size and read only as much as needed to answer the question.

**Signature**: `stat -c '%s'` or `wc -l` before `cat`; `head -N` to preview; `nrows=` in pandas reads.

**Why it works**: Large files can blow the context window or generate irrelevant noise. Sampling first
lets the model make an informed decision about how much to read.

**Example**: `file-reading/SKILL.md` — "Stat before you read. Large files need sampling, not slurping."

---

## 15. Annotated File Tree

**What it is**: The skill presents the directory structure as an annotated tree, with each file labeled
with its purpose in a comment or inline note.

**Signature**: ASCII tree followed by inline `#` comments explaining each file's role.

**Why it works**: Gives the model (and human readers) an immediate structural overview without having
to open every file. Acts as a map for the rest of the skill.

**Example**:
```
skill-name/
├── SKILL.md              # entry point, routing logic
├── scripts/
│   └── build.py          # deterministic file construction
└── references/
    └── api-options.md    # lazy-loaded when advanced options needed
```