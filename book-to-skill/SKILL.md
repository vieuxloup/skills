---
name: book-to-skill
description: >
  Convert a book (text file) into a reusable AI skill by extracting its core methodology, principles, thinking logic, and practical methods. Use when the user wants to: (1) Turn knowledge from a book into a callable AI assistant/skill, (2) Create a skill from a .txt file containing a book's full content, (3) Distill a book's framework, principles, and methods into structured SKILL.md format, (4) Make a book's wisdom actionable rather than just summarized — preserving the author's logic chain, cross-chapter relationships, and original method descriptions.
---

# Book To Skill

## Overview

Turn any book into a **practical AI skill** — not a summary, but a callable assistant that applies the book's thinking logic, principles, and methods to solve real problems. This skill preserves the author's original logical chain rather than fragmenting it into disjointed summaries.

## Prerequisites

- The book in **plain text format** (.txt). See Step 1 for conversion.
- Access to a **long-context LLM** (recommended: DeepSeek V4 Pro / 100万+ token context window). A long context window is critical — it lets the model read the entire book in one pass, preserving cross-chapter causal relationships.
- **skill-creator** tool available (to generate the final skill package).

## Process Overview

```
书(.txt) → 长上下文模型通读 → 全局结构分析 → 场景拆解 → skill生成 → 测试验证
```

## Step 1: Prepare the Book as Plain Text

Convert the book to `.txt` format:

| Source Format | Tool | Method |
|---|---|---|
| Word (.docx) | Microsoft Word / LibreOffice | 打开 → 另存为 → .txt |
| PDF | Calibre | 添加书籍 → 转换书籍 → 输出格式: TXT |
| EPUB | Calibre | 同上 |

> **Note**: Calibre is free and cross-platform. After conversion, verify the text is complete and encoding is UTF-8.

## Step 2: Feed the Whole Book to a Long-Context LLM

Place the `.txt` file in the working directory and send a prompt like this:

```
这是《[书名]》全文。先不要摘要，通读全书，给我一张全局结构图：
- 核心论点是什么？
- 围绕它有哪些技能/方法/原则？
- 它们之间是什么关系？
- 我想用它做一个能帮我[应用场景]的skill
```

> **Why the whole book?** A book's logic chain spans across chapters. If you split and batch-read, cross-chapter causal relationships break. The long context window lets the model see everything at once, preserving the full reasoning structure.

## Step 3: Analyze the Book's Structure

After the model reads the book, have it produce:

1. **Core thesis** — the central argument or framework
2. **Key methods/principles** — actionable techniques organized by category
3. **Relationships** — how methods connect, sequence, and build on each other
4. **Application scenarios** — real-world situations where each method applies

## Step 4: Design the Skill Scenarios

Based on the book's structure, identify **scenarios** — distinct use cases where a reader would apply the book's knowledge on real tasks.

For each scenario, have the AI output:

- **Scenario name** (e.g., "结构化表达", "解决问题的逻辑")
- **Trigger condition** — what user request should invoke this scenario
- **Step-by-step workflow** — each step maps to a specific method from the book
- **Key methods/principles** — preserve original descriptions, do NOT summarize or generalize

Prompt template:

```
根据[书名]的结构，分解为[N]个核心场景。每个场景：
1. 输出逻辑流程
2. 每个步骤对应书中的具体方法
3. 保留原始方法描述，不要概括
确认后，再进行下一步
```

## Step 5: Generate the Skill

Once scenarios and workflows are confirmed:

```
把[N]个场景写入一个skill
```

This invokes the **skill-creator** tool to generate a proper skill package with:
- **YAML frontmatter**: name + description (critical — description determines if AI auto-recalls the skill)
- **Workflow body**: step-by-step instructions for each scenario
- **Trigger patterns**: embed natural language patterns in description so the AI knows when to call this skill

### Polish the Description

The description in frontmatter is the **only thing** the AI reads to decide whether to trigger the skill. Get it right:

```
打磨 description——把「用户在什么场景、说什么话时需要它」全写进去，
因为这决定它会不会被 AI 自动召回，写不好等于白做。
```

## Step 6: Test and Iterate

### Recall Test

Check if the description correctly triggers the skill:

```
只看这个skill的description，用户说这些句子时，你会不会调用这个skill：
1. [测试用例1]
2. [测试用例2]
3. [测试用例3]
```

### Invocation Test

Call the skill to complete a real task and evaluate:

- Does it correctly apply the book's methods?
- Are the workflows clear and actionable?
- Does it degrade gracefully when input doesn't match expected scenarios?

### Iterate

- Weak recall → revise description
- Wrong behavior → fix workflows in the skill body
- Missing methods → return to Step 3 and add scenarios

## Which Books Work Best

| 类型 | 推荐 | 不适合 |
|---|---|---|
| 方法论类 | ✅《金字塔原理》《高效能人士的七个习惯》 | ❌ 纯故事/小说 |
| 管理/职场技能 | ✅《非暴力沟通》《影响力》《关键对话》 | ❌ 纯数据/手册 |
| 认知/思维 | ✅《认知天性》《思考，快与慢》 | ❌ 编年史/传记 |
| 原则/框架 | ✅《原则》《孙子兵法》 | ❌ 纯技术文档 |

**Criteria**: the more structured and method-driven a book is, the better it translates to a skill.

## Cost & Time

- **Prep time**: 5 min (if ebook ready) to 30 min (need to find + convert)
- **Process time**: ~30 min after familiar with the flow
- **Token cost**: ~几十万tokens, < 1 RMB (DeepSeek V4 pricing)

## Reference

See `references/book-analysis-patterns.md` for structured templates of analyzing different book genres (methodology, management, cognitive science, philosophy). Each template includes proven prompt patterns and scenario decomposition examples.
