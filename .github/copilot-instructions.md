# Copilot Instructions for johnvo402/copilot-prompt

## Project Overview
This repository is a reference and template for optimizing GitHub Copilot through custom instructions and prompt files. It documents best practices for encoding team workflows, standards, and reusable AI tasks.

## Architecture & Key Components
- **Documentation-driven:** The README.md is the central source of truth, explaining Copilot customization, usage patterns, and examples.
- **Prompt Library:** Intended structure includes `.github/prompts/` for reusable slash commands (see README for examples).
- **Instruction Files:** Supports both global (`.github/copilot-instructions.md`) and path-specific (`.github/instructions/*.instructions.md`) context files.

## Developer Workflows
- **Customization-first:** All Copilot usage should be guided by explicit instructions and prompt files. See README for the additive context system.
- **Prompt File Usage:** Place prompt files in `.github/prompts/NAME.prompt.md` with YAML frontmatter. These files define custom slash commands for Copilot chat.
- **Instruction File Usage:** For path-specific rules, use `.github/instructions/NAME.instructions.md` with YAML frontmatter specifying `applyTo` globs.
- **Verification:** To confirm instructions are applied, check the "References list" in Copilot Chat responses.

## Project-Specific Conventions
- **Documentation:** All new features and workflows should be documented in README.md and, where relevant, in prompt/instruction files.
- **Examples:** Use the provided examples in README.md as templates for new prompt and instruction files.
- **Additive Context:** Both global and path-specific instructions are combined when relevant; do not override, but extend.

## Integration Points
- **Copilot Chat:** Custom slash commands are registered from `.github/prompts/` files.
- **VS Code/JetBrains:** Only these IDEs support prompt file registration as described.

## External Dependencies
- No code dependencies; this repo is documentation and template focused.

## Key Files & Directories
- `README.md`: Master documentation and examples.
- `.github/copilot-instructions.md`: Global Copilot context.
- `.github/instructions/`: Path-specific context files (if present).
- `.github/prompts/`: Custom slash command prompt files (if present).

## Example Patterns
- See README.md for full YAML and markdown examples for prompt and instruction files.
- Follow the structure and conventions outlined in README.md for all new additions.

---
_This file is auto-generated to guide AI coding agents. Please update if project structure or conventions change._
