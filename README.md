# Skills for realer engineers

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
$ npx install skills@latest add casparderksen/skills --skill <skill> ...
```

## Overview

### Java development

- [java](development/java/SKILL.md) — Modern Java idioms and conventions: records, sealed classes, pattern matching, null safety, streams, immutability, Checkstyle/PMD/SpotBugs compliance. Triggers on any Java code output.
- [java-code-review](development/java-code-review/SKILL.md) — Full architectural and code quality review of Java projects: hexagonal architecture, DDD, SOLID, coupling, naming, error handling. Produces severity-ranked findings with file locations.
- [maven](development/maven/SKILL.md) — Maven build conventions: dependency version management, plugin configuration, required plugins, profiles. Triggers on `pom.xml` edits.

### Architecture

- [adr-writer](architecture/adr-writer/SKILL.md) - Formulates the outcome of a conversation into a structured Architectural Decision Record (ADR) using a standardised template.

### Agents

- [adr-for-agents](agents/adr-for-agents/SKILL.md) — Writes minimal, outcome-focused ADRs intended to be loaded as agent context, not read by humans. Inspired by Matt Pocock.
- [refactor-claude-md](agents/refactor-claude-md/SKILL.md) — Refactors CLAUDE.md files to be lean and session-focused, moving reference docs and design decisions out via progressive disclosure. Inspired by Matt Pocock.

## AI Disclosure

This project uses artificial intelligence tools for research, coding, or documentation. All  final content
was  reviewed, edited, and validated by the human author before publication.
