# Repository Guidelines

This repository documents and compares monorepo and multi-repo architectural patterns. Keep contributions focused, evidence-based, and practically useful for teams making architecture decisions.

## Project Structure & Module Organization

- Place primary content under `docs/`, grouping by topic (for example, `docs/monorepo/`, `docs/multi-repo/`, `docs/case-studies/`).
- Put reusable diagrams, images, and charts in `assets/` or `docs/assets/` and use descriptive, kebab-case filenames (for example, `monorepo-ci-flow.png`).
- Store runnable code samples in `examples/<language>/` and keep them self-contained with a short README.

## Build, Test, and Development Commands

- Preview Markdown locally using your editor’s Markdown preview or a static viewer (for example, `markdown foo.md` if available).
- When possible, run Markdown linting and formatting before opening a PR (for example, `markdownlint '**/*.md'` or `prettier --check '**/*.md'` if installed globally or in your environment).

## Coding Style & Naming Conventions

- Write in clear, neutral, and professional English; prefer short paragraphs and bullet lists over long prose.
- Use title case for top-level headings and sentence case for subheadings where it improves readability.
- Use fenced code blocks with language tags (for example, ```bash, ```ts) and prefer kebab-case for file and directory names in examples.

## Testing Guidelines

- Treat link checking, spelling, and style linting as “tests” for this documentation repo.
- Before submitting, scan for broken links, run any available Markdown linters, and ensure code samples compile or run as written.

## Commit & Pull Request Guidelines

- Write concise commit messages in the form `area: short description` (for example, `docs: add monorepo ci section`, `examples: update nx workspace sample`).
- For pull requests, include: a short summary, the motivation or problem, key changes by section or file, and any follow-up work or open questions.
- Link to related issues or discussions when relevant and add screenshots for visual changes (diagrams, architecture overviews, etc.).

## Agent-Specific Instructions

- Claude-based tools should review `CLAUDE.md` before making changes.
- GPT-based tools should review this `AGENTS.md` file and keep new content aligned with the structure described in `README.md`.
