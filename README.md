# English Version
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

# Chinese Version
# 技能专家（skill-expert）
一款专注于**分析、解构与解释任意技能内部架构**的智能体。
当用户希望理解某项技能的工作原理、审核技能设计、逆向推导技能工作流、对比技能架构，或从现有技能中提取可复用模式用于新建技能时，使用本技能。

# 本技能的功能
接收任意技能（通过路径、名称或粘贴内容），并生成一份**结构化解构报告**，内容涵盖：
1. **结构解剖**——包含哪些文件及其作用
2. **触发逻辑**——技能在何时、以何种方式激活
3. **工作流图谱**——分步执行流程
4. **设计模式**——复用技术及其设计原理
5. **实现逻辑**——核心脚本、工具与算法
6. **独特创新点**——该技能中巧妙或非显而易见的设计
7. **扩展点**——技能可扩展与适配的方向与方式

# 内部文件说明
## SKILL.md —— 主入口文件，分为4个阶段：
1. **定位与盘点**：按名称或路径查找技能，列出所有文件及其大小与角色
2. **深度阅读每一层**：逐层级阅读YAML前置元数据、SKILL.md正文、脚本、参考文件与资源，并按指定维度分析
3. **生成解构报告**：采用10章节模板，涵盖结构、触发分析、工作流、设计模式、实现逻辑、创新点、依赖、扩展点与客观评价
4. **按需深入分析**：支持后续扩展模式——追踪特定输入、对比两项技能、提取模式、审核触发逻辑或生成压力测试用例

## references/pattern-catalog.md
收录**15种命名式技能设计模式**（如渐进式披露、路由/调度表、懒加载引用、脚本剥离等），每种模式均包含特征、设计原理与来自公共技能库的真实案例。在分析需要时**懒加载**。

## references/trigger-heuristics.md
专门用于**审核描述字段**的参考文档：包含触发不足/触发过度问题对照表、格式专属判定规则、重写模板与快速审核清单。在用户需要触发审核时加载。
