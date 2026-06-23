---
name: maven
description: >
  Maven best practices. Use this skill when editing Maven build files (`pom.xml`).
---

# Apache Maven standards

## Structure

- Single `pom.xml` at project root for single-module projects.
- Multi-module: parent POM manages versions and plugin configuration only; no build logic or dependencies in the parent.
- Use a BOM (`<dependencyManagement>`) to centralise dependency versions.

## Dependencies

- Declare test-scoped dependencies with `<scope>test</scope>`.
- Define all dependency versions in `<properties>` - never inline in `<dependency>`.
- Do not pin a version that is already governed by an imported BOM; let the BOM control it.

## Plugins

- Pin all plugin versions explicitly in `<build><pluginManagement>` - never rely on Maven default versions
- Plugin configuration belongs in `<pluginManagement>`, not inline per-module.
- Required plugins for every project:

| Plugin | Purpose |
|--------|---------|
| `maven-compiler-plugin` | Enforces Java version and compiler flags |
| `maven-surefire-plugin` | Runs unit tests (`*Test`) |
| `maven-failsafe-plugin` | Runs integration tests (`*IT`); bound to `verify` |
| `spotless-maven-plugin` | Formats code |
| `maven-enforcer-plugin` | Enforces Java version, Maven version, and no banned dependencies |
| `jacoco-maven-plugin`   | Measures and enforces test coverage thresholds  |

## Properties

- Define all reusable values (versions, paths, flags) in `<properties>`.
- Java version: `<maven.compiler.release>25</maven.compiler.release>`.
- Encoding: `<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>`.

## Profiles

- Profiles for environment or build-variant concerns only - e.g. `native`, `docker-push`.
- A profile must never alter dependency versions or change test scope.
- Profiles are opt-in (`-P<name>`) - the default build must succeed without any profile active.
