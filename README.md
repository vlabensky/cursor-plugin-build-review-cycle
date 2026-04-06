# Cursor Plugin: Build-Review Cycle with Cursor Subagents

A [Cursor plugin](https://cursor.com/docs/plugins#creating-plugins) that implements a multi-agent build-and-review cycle for code generation. When invoked, it orchestrates three specialized subagents that iteratively implement, review, and refine code until it meets both quality and requirement standards.

## What it does

After you provide a detailed implementation plan, invoke `/build-with-subagent-review`. The main agent will process each step of your plan through a loop and invoke subagents:

1. **Coder** — implements the scoped task
2. **Code Reviewer** + **Scope Reviewer** — run in parallel to assess code quality and requirement completeness
3. If either reviewer finds issues, the Coder fixes them and the reviewers re-run
4. Once both reviewers are satisfied, the main agent moves to the next step

## Agents

| Agent | Role | Remarks |
|---|---|---|
| `coder` | Implements exactly what is in scope — no more, no less | Use cheap and fast model |
| `code-reviewer` | Read-only; checks style, readability, and best practices | Frontier-level model |
| `scope-reviewer` | Read-only; verifies all requirements from the plan are correctly implemented | Frontier-level model |

## Usage

1. Write out a detailed implementation plan in your Cursor conversation. Make sure your plan is detailed and is broken into distinct and clear implementation steps. Quality of the entire flow depends largely on the plan's quality.
2. Run `/build-with-subagent-review` with your plan as input.
3. The plugin handles the rest — implementing and reviewing each step until done.

## Installation

Install from the [Cursor Community](https://cursor.directory/) or clone this repo and load it locally.

## License

MIT
