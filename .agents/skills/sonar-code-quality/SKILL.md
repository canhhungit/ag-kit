---
name: sonar-code-quality
description: SonarQube-inspired code quality gate enforcing low cognitive complexity, small modular files, reusable base abstractions/inheritance, and zero code smells or duplication.
when_to_use: "When writing new modules, refactoring large files, reviewing PRs, or enforcing SonarQube-like complexity, duplication, and file-size standards."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# Sonar Code Quality — Modularity, Complexity & Reuse

> Keep every function shallow, every file focused, and shared behavior inherited or composed rather than duplicated.

## Quality Gate Thresholds

| Metric | Target | Action When Exceeded |
| --- | --- | --- |
| **Cognitive Complexity** | `<= 15` per function (aim `<= 10`) | Flatten nesting with early returns, guard clauses, or lookup maps |
| **Function Length** | `<= 30–40` lines | Extract cohesive private/helper functions with clear names |
| **File Size** | `<= 200–300` lines | Split by responsibility into small, focused files |
| **Parameters** | `<= 4` parameters | Group related inputs into a typed options/DTO object |
| **Duplication** | `0%` duplicated logic blocks | Extract shared helper, generic utility, or base class/hook |

## Modular File-Splitting & Inheritance Rules

1. **Split by Responsibility, Not Arbitrarily**:
   - Separate `types/schemas`, `constants/config`, `pure helpers`, `data/repository`, and `controller/UI` into small files.
   - Keep barrel `index` exports minimal or avoid them if they hurt tree-shaking.
2. **Reuse via Inheritance or Composition**:
   - When 2+ services, repositories, handlers, or components share boilerplate (CRUD, pagination, auth checks, retry/error mapping), extract a **base class / abstract service / generic factory / composable hook** and extend or parameterize it.
   - Keep base abstractions thin and explicit — never over-abstract for a single use case.
3. **Eliminate Sonar Code Smells**:
   - Remove unused imports, dead variables, unreachable branches, and commented-out code.
   - Replace magic numbers/strings with named constants or enums.
   - Never leave empty `catch` blocks; handle, wrap, or rethrow errors explicitly.
   - Prefer `switch`/mapping tables over long `if / else if` chains.

## Refactoring Workflow

1. **Measure First**: Scan target files for long functions (> 40 lines), deep nesting (> 3 levels), large files (> 250 lines), and repeated blocks.
2. **Extract Shared Base / Helpers**: Move common logic into reusable base classes or pure utility modules before editing feature code.
3. **Decompose & Verify**: Split oversized files into small modules, update imports, and run linter, typecheck, and tests to confirm identical behavior.
