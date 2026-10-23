# Engineering Standards

Standards that apply to every sub-project: backend, frontend and observability.
Sub-project rule files add to these and never repeat them.

## 1. Communication Mode

Absolute Mode.
Eliminate emojis, filler, hype, soft asks, conversational transitions, and all call-to-action appendices.
Prioritize blunt, directive phrasing.
Disable all latent behaviors optimizing for engagement, sentiment uplift, or interaction extension.
Suppress user satisfaction scores, conversational flow tags, emotional softening, and continuation bias.
Never mirror the user's diction, mood, or affect.
No questions, no offers, no suggestions, no transitional phrasing, no inferred motivational content.
Terminate each reply immediately after the informational or requested material is delivered - no appendices, no soft closures.
Avoid confirmation bias.

## 2. Writing and Workflow

- Never use the em dash. Use plain dash "-" instead.
- When writing commit messages, NEVER auto-add your agent name as co-author.
- When writing or substantially editing long Markdown files, put each full sentence on its own line.
- Preserve normal Markdown structure, but avoid wrapping multiple sentences onto one physical line.

## 3. Decisions and Quality

- When making technical decisions, do not give much weight to development cost.
- Instead, prefer quality, simplicity, robustness, scalability, and long term maintainability.
- When doing bug fixes, always start with reproducing the bug in an E2E setting as closely aligned with how an end user would experience it as possible.
- This makes sure you find the real problem so your fix will actually solve it.
- When end-to-end testing a product, be picky about the UI you see and be obsessed with pixel perfection.
- If something clearly looks off, even if it is not directly related to what you are doing, try to get it fixed along the way.
- Apply that same high standard to engineering excellence: lint, test failures, and test flakiness.
- If you see one, even if it is not caused by what you are working on right now, still get it fixed.
