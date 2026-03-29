# skillexpert
An agent specialized in analyzing, deconstructing, and explaining the internal architecture of any skill.   Use this skill whenever a user wants to understand how a skill works, audit a skill's design, reverse-engineer   a skill's workflow, compare skill architectures, or extract reusable patterns from a skill for use in a new one.

# What This Skill Does
Given any skill (by path, by name, or by pasted content), you will produce a structured deconstruction that covers:
1. Structural Anatomy — what files exist and why
2. Trigger Logic — when and how the skill activates
3. Workflow Map — the step-by-step execution flow
4. Design Patterns — recurring techniques and their rationale
5. Implementation Logic — key scripts, tools, and algorithms
6.Unique Innovations — what's clever or non-obvious about this skill
7. Extension Points — where and how this skill could be extended or adapted

# What's inside
## SKILL.md — The main entry point, structured into 4 phases:
1. Locate & Inventory — finds the skill by name or path, lists all files with sizes and roles
2. Deep-Read Every Layer — reads YAML frontmatter, SKILL.md body, scripts, references, and assets with specific questions to ask at each layer
3. Produce the Deconstruction Report — a 10-section template covering anatomy, trigger analysis, workflow map, design patterns, implementation logic, innovations, dependencies, extension points, and a candid verdict
4. Go Deeper on Request — handles follow-up modes: tracing a specific input, comparing two skills, extracting patterns, auditing triggers, or generating stress-test cases

## references/pattern-catalog.md 
A catalog of 15 named skill design patterns (Progressive Disclosure, Router/Dispatch Table, Lazy Reference Loading, Bundled Script Offload, etc.), each with a signature, rationale, and real example from the public skill library. Loaded lazily when the analysis calls for it.

## references/trigger-heuristics.md 
A focused reference for auditing the description field: undertrigger/overtrigger symptom tables, format-specific heuristics, a rewrite template, and a quick-audit checklist. Loaded when the user wants a trigger audit.
