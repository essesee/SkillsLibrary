---
name: code-writer
description: "Generate, review, explain, and evaluate code with repo-aware context, automatic local environment setup, and inline learning comments. Use this skill whenever the user needs to write code from scratch, review a PR, understand unfamiliar code, generate from a screenshot or mockup, set up a local dev environment, or get a code evaluation. Trigger on phrases like 'write code for,' 'review this PR,' 'explain this code,' 'build this feature,' 'set up the project locally,' 'what does this code do,' 'generate from this screenshot,' or any coding task beyond simple one-liners."
---

# Code Writer & Evaluator

## Purpose
The user understands code well enough to know what's needed but can't always write from scratch. They also need to review team code and give meaningful feedback. This skill handles code generation, evaluation, explanation, and local environment management.

## Dependencies
- **Voice Analyzer (Skill 0):** For review comments only (so feedback sounds like the user).
- **GitHub API:** Repo read, PR review, push.

## Inputs
- GitHub repo URL or local codebase
- Plain-language description of what to build
- Screenshots or mockups (converted to structured text)
- Code snippets for review or explanation

## Outputs
- Working code with optional inline comments
- Running local preview
- Review summaries in user's voice
- Plain-language explanations

## Modes

### Repo Ingestion (runs once per repo, cached)
1. Read full codebase — file structure, dependencies, naming conventions, patterns, tech stack
2. Generate a **repo profile** — a cached summary for future sessions
3. Lazy loading: first pass reads only file tree + config files. Specific source files loaded on demand per task.
4. Re-ingest only when repo changes significantly

### Writer Mode
1. Generate working code based on the task description
2. Follow the repo's existing patterns, naming conventions, and style
3. Include proper error handling by default
4. Inline comments togglable: ON for learning (explains what each section does), OFF for speed (saves 30–40% output tokens)
5. Auto-apply the repo's linting and formatting rules before outputting
6. After code is confirmed correct, generate unit tests as a **follow-up step** — never in the same call as code generation

### Screenshot-to-Code
1. Parse screenshot into a structured text description (layout, components, styles, interactions)
2. Drop the image from context after parsing
3. Generate code from the structured description, not from the image directly

### Evaluator Mode
Review PRs or code snippets for:
- Bugs and logic errors
- Performance inefficiencies
- Readability issues
- Security vulnerabilities
- Style guide violations
Output the review summary in the user's voice (loads Voice Profile for this).

### Explain Mode
- Line-by-line plain-language walkthrough
- Flag unusual or risky patterns
- Highlight non-obvious side effects or dependencies

### Auto Local Environment
After code generation, spin up local dev server:
- Handle dependency installation
- Set up environment variables
- Start the dev server
- Verify it runs correctly

## Context Rules
- **Lazy repo loading:** File tree + configs first. Load specific files only as needed for the task.
- **Cache repo profile** after first ingestion. Don't re-read the whole repo each time.
- **Screenshot → structured text → drop image.** Never keep screenshots in context after parsing.
- **Inline comments togglable** to save tokens when the user doesn't need explanations.
- **Unit tests in follow-up call**, not same call as code generation — keeps each call focused.
