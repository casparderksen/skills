Skills for realer engineers

My agent skills for engineering and architecture - no vibing.

Part of my automated Claude Code setup in [Dotfiles to automate Mac setup](https://github.com/casparderksen/dotfiles).
The script [install-claude-plugins.sh](https://github.com/casparderksen/dotfiles/blob/main/scripts/bin/install-claude-plugins.sh)
installs curated Claude Code plugins and skills, includig the skills in this project.
The skills in this project can also be installed standalone.

## Quickstart

```sh
$ npx install skills@latest add casparderksen/skills 
```

Or to install individual skills:

```sh
$ npx install skills@latest add casparderksen/skills <skill> ...
```

## Overview

### Java development

- [java](development/java/SKILL.md) — Java coding standards enforcing modern style and quality rules.
- [java-code-review](development/java-code-review/SKILL.md) — Code review for Java applications.
- [maven](development/maven/SKILL.md) — Apache Maven standards.

### Architecture

- [adr-writer](architecture/adr-writer/SKILL.md) - Formulates the outcome of a conversation into a structured Architectural Decision Record (ADR) using a standardised template.
