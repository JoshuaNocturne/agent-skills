# Generate agents_ref.md

Use this skill when the user asks to create, update, or regenerate `agents_ref.md`, or wants a project reference file for AI assistants.

Generate a concise `agents_ref.md` that tells an AI agent **how to use** the target project. Place it at the target project root.

## Constraints

- **Only write `agents_ref.md`**. Do not modify any other files.
- **External usage only**. No internal architecture, no build instructions for contributors, no project structure trees. Every line must answer: "How do I use this?"

## Steps

1. **Scan the target project** — read exported APIs, public interfaces, README, and docs to understand what users consume.

2. **Generate `agents_ref.md`** at the target project root with these sections. If the file already exists, overwrite it entirely with a fresh version based on the current codebase.

```markdown
# Project Reference

## Overview
<1-2 sentences: what this project is and what it provides>

## Prerequisites
<minimal requirements to use: language version, platform, dependencies>

## Quick Start
<shortest code to include and use the project — copy-paste ready>

## Public API
<types, functions, and concepts the user calls, with brief signatures and one-line descriptions>

## Usage Patterns
<common patterns users need: how to create X, how to compose Y, how to handle errors>

## Pitfalls
<non-obvious behaviors, silent failures, semantic surprises that bite users>
```

3. **Keep it under 200 lines.** This file lives in the context window — every line must earn its place. Prefer signatures + one-liners over paragraphs.

4. **Derive facts from code.** Read the actual source to confirm signatures and semantics. Do not hallucinate APIs.

5. **After writing**, report the file path and line count.
