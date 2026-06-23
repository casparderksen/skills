---
name: java
description: >
  Java idioms and best practices. Use this skill
  whenever designing, specifying, writing or reviewing any Java code.
  Trigger on any Java code output regardless of context,
  including brainstorming, writing specification, and giving
  examples in design documents or documentation.
---

# Java Conventions

Java conventions that must be applied to all Java code.
Neveer output Java code that does not follow these conventions.

---

## Records

- Use `record` for immutable data carriers: DTOs, value objects, command/query objects.
- No mutable state or setters in records.
- Compact constructors: validation only — no assignment.
- Prefer records over Lombok `@Value` or manual immutable classes.

```java
// Correct
public record CreateOrderCommand(String customerId, List<String> itemIds) {
    public CreateOrderCommand {
        Objects.requireNonNull(customerId, "customerId must not be null"); // defensive null check
        itemIds = List.copyOf(itemIds); // defensive copy of external collection
    }
}
```

---

## Sealed Classes

- Use `sealed` + `permits` for closed type hierarchies (result types, domain events, error types).
- Prefer `sealed interface` over `abstract class` for pure type discrimination.
- Always handle all permitted subtypes in `switch` expressions — rely on exhaustiveness checking.

```java
public sealed interface OrderResult permits OrderResult.Success, OrderResult.Failure {
    record Success(String orderId) implements OrderResult {}
    record Failure(String reason) implements OrderResult {}
}
```

---

## Pattern Matching

- Use `instanceof` pattern matching instead of explicit casts.
- Use `switch` expressions (not statements) with pattern matching for sealed hierarchies.
- Never use raw `switch` statements where expression suffices.

```java
// Correct
String describe(OrderResult result) {
    return switch (result) {
        case OrderResult.Success s -> "Order created: " + s.orderId();
        case OrderResult.Failure f -> "Failed: " + f.reason();
    };
}
```

---

## Text Blocks

- Always use text blocks for multiline strings.
- Align closing `"""` with content indentation to control leading whitespace.
- No multiple `+` concatenations on text blocks — use `String.formatted()`.

---

## Optional

- `Optional` as **return type** only — never `Optional` as field, constructor param, or method param.
- Never call `.get()` without `.isPresent()` — prefer `.orElseThrow()`, `.orElse()`, `.map()`, `.ifPresent()`.
- Never `Optional.of()` on nullable values — use `Optional.ofNullable()`.

---

## Streams

- Prefer streams over imperative loops for collection transformations.
- Extract complex lambdas to named methods - no inline code blocks with multiple statements.
- Use `Collectors.toUnmodifiableList()` / `toUnmodifiableMap()` for defensive results.
- No stateful lambdas or side effects inside stream operations.
- Max one level of stream nesting — extract inner streams to methods.

---

## `var`

- Use `var` when type obvious from RHS (constructor calls, `List.of()`).
- Avoid `var` when type adds clarity (interface types, generic bounds).
- Never `var` for method parameters or return types.

---

## Null Safety

- Never return `null` from public methods — use `Optional` or throw.
- Annotate non-null params with `@NonNull` from any standard JSR-305 annotation, or validate with `Objects.requireNonNull`.
- Prefer `Objects.requireNonNullElse()` over ternary null checks.
- Never pass `null` as argument — make optionality explicit.

---

## Immutability

- Fields `final` by default. Remove `final` only when mutation explicitly required.
- Use `List.of()`, `Map.of()`, `Set.of()` for literals — never `new ArrayList<>()` for constants.
- Use `List.copyOf()` in constructors when accepting external collections.

---

## Naming

- Classes: `PascalCase` noun or noun phrase.
- Methods: `camelCase` verb or verb phrase.
- Constants: `UPPER_SNAKE_CASE`.
- Booleans: prefix `is`, `has`, `can`, `should`.
- No abbreviations except universally accepted (`id`, `url`, `dto`).
- Test class: `<ProductionClass>Test` — `PlaceOrderUseCaseTest`
- Integration test class: `<ProductionClass>IT` — `PlaceOrderIT`.
- Test method: `<method>_<scenario>_<expected>` — `processOrder_shouldThrowException_whenNoOrderLines`

---

## Exception Discipline

- Checked exceptions: integration boundaries only (I/O, external systems).
- Unchecked exceptions: programming errors and violated invariants.
- Never swallow silently — handle with intent or rethrow.
- Never catch `Exception` or `Throwable` in business logic.
- Meaningful messages — include violating value where safe.
- Alway use try-with-resources for every `AutoCloseable` to prevent resource leaks.

---

## General

- Prefer composition over inheritance.
- Program to interfaces, not implementations, for collaborators.
- Single-purpose methods.
- No boolean parameters — extract to enum or two methods.
- No `static` utility classes — prefer instance methods or injected collaborators.
- No magic values — define constants.
- No trailing `return;` at end of void methods — delete it.
- No `if (cond) return true; else return false;` — replace with `return cond;`.
- Remove any method parameter not referenced in the body; trace removals up the call chain.
- Self-explanatory code preferred; expressive naming over inline comments.
- Comments explain **why**, never **what**.

---

## Unit testing

- AssertJ assertions exclusively.
- Chain multiple assertions on same object.

---

## Checkstyle compliance

This project enforces Checkstyle. Generate code that passes on the first try.

**Import ordering** — one alphabetically sorted block, no grouping:
```java
// Correct: single block, alphabetical
import jakarta.persistence.Entity;
import java.util.ArrayList;
import java.util.List;
import javax.annotation.processing.Generated;
import org.hibernate.annotations.UuidGenerator;
```
Never group by `jakarta` / `java` / `javax` / `org` — just sort the whole list.

**Cyclomatic complexity ≤ 10** — each `if`, `else if`, `for`, `while`, `catch`, `case`, `&&`, `||`, `?:` adds 1. When a method exceeds 10 branches, extract private helpers. Deeply nested loops or multi-arm switches are the common culprits.

**Method length: style target ≤ 20 lines, hard limit 150 lines (Checkstyle fails above 150).** For new code, keep methods ≤ 20 lines. Checkstyle enforces a hard ceiling of 150; approaching that ceiling is a signal to refactor.

**`System.out` / `System.err` banned in production** — use a logger. Exception: CLI entry points that intentionally write to stderr. In that case, capture the stream in a local variable before any anonymous class so the reference doesn't appear as a field access inside the class body:
```java
// CLI stderr workaround — 'System.err' not followed by '.' inside anonymous class
java.io.PrintStream err = System.err;
return new System.Logger() {
    public void log(...) { err.println(...); }
};
```

**Variable and parameter names** — `^[a-z][a-zA-Z0-9]+$`, minimum 2 characters. No single-char names (`i`, `e`, `v`) in production source. Use `idx`, `ex`, `val`.

**Braces on every block** — `if`, `else`, `for`, `while` always use `{}`, even single-line bodies.

**No explicit no-arg `super()`** — Checkstyle flags `super();` as unnecessary. Delete it.

**`@return` Javadoc** — no trailing period on `@return` lines.

---

## Static analysis compliance

Code must pass Checkstyle, PMD, SpotBugs, and Spotless on first generation. Fix root cause — never suppress.

Prohibited suppressions (zero exceptions):
- Checkstyle: `@SuppressWarnings("checkstyle:...")`, entries in `custom-suppressions.xml`
- PMD: `@SuppressWarnings("PMD.*")`, `//NOPMD`
- SpotBugs: `@SuppressFBWarnings`
- Spotless: `// spotless:off` / `// spotless:on`

Never modify `.baseline/` configuration files.

---

## SpotBugs compliance

**`NP_NULL_ON_SOME_PATH_FROM_RETURN_VALUE` — `Path.getParent()`:** Always call `path.toAbsolutePath().getParent()`, never `path.getParent()` directly. `getParent()` returns `null` for root-relative paths. Null-check the result before dereferencing.

**`EI_EXPOSE_REP2` (mutable collection in constructor):** Never store an externally-supplied `Map`, `List`, or array directly. Use `Map.copyOf(...)`, `List.copyOf(...)`, or `Arrays.copyOf(...)` in the constructor to store a defensive copy.

**`EI_EXPOSE_REP` / `EI_EXPOSE_REP2` on non-copyable mutable types (e.g. `ResourceSet`):** A Java record auto-exposes all components; if a component cannot be defensively copied, convert the record to a `final class`. Collect the needed data eagerly in the constructor; don't store the mutable object as a field.
