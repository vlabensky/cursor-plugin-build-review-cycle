# Overview

You are orchestrating implementation of a multi-step plan by delegating each step to the coder subagent, then validating with code-reviewer and scope-reviewer in parallel; loop on feedback until no improvements, then advance to the next step.

Follow This Order:

## To-Do List

Track progress with this checklist. Complete each step fully (including the review loop) before moving to the next.

Plan execution progress:

- [ ] For each phase in the plan:
 - [ ] For each step in that phase:
   - [ ] Step N: Delegate to /coder with scope and requirements → wait for completion
   - [ ] Step N: Invoke /code-reviewer and /scope-reviewer in parallel with same scope → wait for both
   - [ ] Step N: If any improvement suggestions → verify correctness → if confirmed, re-delegate to /coder and repeat from "Delegate to /coder" until no suggestions
   - [ ] Step N: Mark step complete; proceed to next step
 - [ ] Phase complete; proceed to next phase
- [ ] Plan complete

## Per-Step Workflow

For **one** step in the plan:

1. **Delegate implementation**

- Invoke the **coder** subagent (`/coder`).
- Pass the exact scope and requirements for this step (phase name, step description, files or areas in scope, acceptance criteria).
- Wait for the coder to finish (code applied, build/lint addressed as per project rules).

2. **Run reviews in parallel**

- Invoke **code-reviewer** (`/code-reviewer`) and **scope-reviewer** (`/scope-reviewer`) in parallel.
- Hand each the **same** scope (same step, same phase, same files/areas).
- Wait for both responses.

3. **Handle feedback**

- If either reviewer returns improvement suggestions:
  - Verify that each suggestion is correct (relevant to the scope, factual, not redundant).
  - For each confirmed suggestion: go back to step 1 and delegate the **fixes** to the coder with clear instructions; then repeat from step 2 (re-run both reviewers).
- If both responses contain no improvement suggestions (positive result only), consider the step done.

4. **Advance**

- Mark the current step complete.
- Move to the next step in the same phase, or to the next phase if the phase is complete.
- Repeat the same process (steps 1–4) for the next step.

## Scope Handoff

When calling subagents, always supply:

- **Phase and step identifier** – So reviewers and coder refer to the same slice of the plan.
- **Relevant files or areas** – Paths or components that were (or should be) changed for this step.
- **Requirements for this step** – What “done” means for scope (scope-reviewer uses this to judge completeness).

## Correctness of Suggestions

Before re-delegating to the coder:

- **Code-reviewer** suggestions: Check that they apply to the given scope, match project conventions or docs, and are not style nitpicks that contradict project rules.

- **Scope-reviewer** suggestions: Check that the reported gap really is a requirement from the plan for this step and that the implementation indeed misses or misimplements it.

If a suggestion is incorrect or out of scope, do not send it to the coder; note why and proceed. Only confirmed, correct suggestions become follow-up work for the coder.

## Summary

- One step at a time; finish the full review loop for that step before moving on.
- Coder implements; code-reviewer and scope-reviewer run in parallel on the same scope.
- Loop: suggestions → verify → fix via coder → re-review until no suggestions.
- Use the to-do list above to avoid skipping steps or phases.