---
name: open-code-review
description: Run the Alibaba open-code-review OCR CLI during code review. Use for Specs 05-code-review, pull request review, staged or unstaged changes, commit review, or branch comparison. Produces priority-ranked findings and can help prepare line-level review comments without applying fixes unless explicitly requested.
compatibility: Requires the ocr CLI from @alibaba-group/open-code-review or a GitHub release binary, plus a configured LLM provider unless the local OCR mode delegates to the active coding agent.
metadata:
  source: https://github.com/alibaba/open-code-review
  stage: 05-code-review
---

# Open Code Review

This Skill is the default Harness reviewer for Specs stage 05-code-review.

## Scope

Use this Skill when:

- The user asks to review code, a PR, a branch range, a commit, or local Git changes.
- A feature reaches specs/features/<feature-id>/code-review.md.
- The 05-code-review template names open-code-review as the review tool.

Do not apply fixes unless the user explicitly asks for review-and-fix. Review-only requests must leave source files unchanged.

## Preflight

1. Read project instructions: AGENTS.md or CLAUDE.md, .harness/project-profile.md, .harness/engineering-rules.md, and specs/AGENTS.md when the task is part of a feature flow.
2. Read the feature context when present: requirements.md, solution-design.md, implementation.md, and unit-test.md.
3. Check whether the CLI is available with command -v ocr and then run ocr llm test.
4. If ocr is missing, tell the user to install it with npm install -g @alibaba-group/open-code-review or use a release binary. Do not install global packages unless the user has authorized it.
5. If LLM connectivity fails, ask the user to configure their provider or record the review as blocked. Never invent or store API keys in the repository.

## Review target

Choose the narrowest target that matches the task:

| Situation | OCR target |
|---|---|
| User says review my changes | ocr review --audience agent |
| Feature stage 05 without a target | ocr review --audience agent |
| User gives a commit | ocr review --audience agent --commit <sha> |
| User gives branches | ocr review --audience agent --from <base> --to <head> |
| User wants a dry run | ocr review --preview |

When feature docs are available, summarize the business context in one or two sentences and pass it with --background.

## Findings

Classify OCR output into Harness priorities:

- P0: security exploit, data loss, irreversible destructive behavior, credential leak, or release blocker.
- P1: correctness bug, regression, permission bypass, unsafe migration, or broken required path.
- P2: maintainability, reliability, performance, observability, or test gap that should be fixed soon.
- P3: style, clarity, minor refactor, or low-risk suggestion.

For each finding, record priority, file and line or range, evidence from the diff or source, proposed fix, and status (open, fixed, waived, or false-positive).

## 05-code-review output

Update specs/features/<feature-id>/code-review.md when the feature directory exists:

- Set Review tool to open-code-review / ocr.
- Record the exact command or manual fallback used.
- Fill the Findings table.
- Set decision to blocked if any P0/P1 finding remains open.
- Set decision to pass_with_followups if only P2/P3 findings remain open with clear follow-up ownership.
- Set decision to pass only when no blocking findings remain.

If OCR is unavailable and the user still needs progress, perform a manual review using the same priority schema and clearly mark the tool as manual fallback.
