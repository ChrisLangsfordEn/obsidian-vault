## Key Files

List and describe the most important configuration and entrypoint files (e.g. main entry point, environment config, CI/CD pipeline definition, IaC root module).

## Module & Layer Boundaries

Describe how the codebase is logically layered or modularised. Explain what belongs where and any boundaries that should not be crossed (e.g. "services must not import from components directly").

## Naming Conventions

Document file and folder naming patterns in use (camelCase, kebab-case, snake_case, feature-based folders, etc.).

## Where to Add New Code

For each common task below, state exactly where new code should go: - New API endpoint or route - New UI component or page - New data model or schema - New utility or helper function - New test

---

## Instructions for Kiro

- Scan the full workspace: source files, config files, lockfiles, Dockerfiles, CI definitions, IaC, and READMEs.
- Do not hallucinate or assume technologies that are not evidenced in the codebase.
- Use the actual folder names and file paths from this workspace — do not use generic placeholders.
- If a section has nothing to report (e.g. no cloud infrastructure is present), write "Not applicable for this project" rather than omitting the section.
- Write both files in clean Markdown, suitable for long-term use as living documentation.
- Save both files to `.kiro/steering/tech.md` and `.kiro/steering/structure.md` respectively.