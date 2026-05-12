# Review C++ Code

Use this skill when the user asks to review, audit, or critique C++ code, or wants feedback on C++ code quality.

Review C++ source code across three dimensions: **logic**, **performance**, and **architecture**. Output a structured report with severity-ranked findings and actionable suggestions.

## Principles & Constraints

- Only report findings backed by evidence in the code. Do not speculate about code not visible.
- Prioritize findings by impact. A single critical UB outweighs ten minor style issues.
- Prefer high-signal findings over exhaustive nitpicks.
- Group repetitive minor issues instead of listing each occurrence separately.
- Keep suggestions concrete — prefer showing a corrected snippet over describing it in prose.
- Avoid suggesting large refactors unless clearly justified. Prefer localized fixes.
- Skip findings the user explicitly said to ignore (e.g., "ignore style issues").

## Review Dimensions

### Logic

- **Undefined behavior**: dangling references, use-after-move, uninitialized reads, signed overflow, null dereference, out-of-bounds access
- **Resource leaks**: missing RAII, leaked handles, missing `delete`/`close`/`release`
- **Exception safety**: missing catch paths, constructors that leak on throw, swap never called
- **Concurrency**: data races, deadlocks, missing synchronization, incorrect `mutex` ordering
- **Type safety**: implicit conversions, slicing, `static_cast`/`reinterpret_cast` misuse, C-style casts
- **API contract violations**: preconditions not checked, postconditions not upheld, wrong `const` qualifier

### Performance

- **Copies**: pass-by-value where `const&` or move applies, unnecessary temporaries
- **Allocations**: heap allocation where stack suffices, redundant `new`/`malloc`, small-string churn
- **Cache**: data layout unfriendly to locality, AOS vs SOA opportunities, false sharing
- **Algorithmic complexity**: O(n²) where O(n log n) or O(n) exists, unnecessary sorted scans, redundant passes
- **Move semantics**: missing `std::move`, returning by `const` value, emplace vs push
- **I/O and blocking**: synchronous I/O on hot paths, unnecessary flushes, busy-wait loops

### Architecture

- **Coupling**: circular `#include` dependencies, direct access to another module's internals bypassing its API, header pulling transitive includes not needed by its own interface
- **Ownership**: raw owning pointers (paired with `delete`/`new`), ownership transferred via raw pointer without documentation or smart pointer, `shared_ptr` used where `unique_ptr` suffices
- **Visibility**: `public` members that should be `private`/`protected`, `friend` grants that bypass encapsulation, `extern` globals reachable from anywhere
- **Responsibility**: a single function/class exceeding a verifiable size threshold while handling unrelated concerns (evidence: distinct call sites never use all paths), header exposing implementation details in its public interface
- **Hard-coded policy**: magic numbers/strings that vary per deployment, platform-specific code not behind an abstraction boundary

## Report Format

Write the report to a **markdown file** at the target project root named `review_report.md`. Group findings by dimension. For each finding:

```
[SEVERITY] file:line — one-line summary
  Detail: <what is wrong and why>
  Suggestion: <concrete fix or approach>
```

**Severity levels:**

- 🔴 **Critical** — UB, data race, security vulnerability, guaranteed crash
- 🟠 **Major** — resource leak, logic error, significant performance regression
- 🟡 **Minor** — style issue, missed optimization, minor code smell
- 🔵 **Info** — suggestion, alternative approach, educational note

At the end, provide a **Summary**: total findings per severity, and the top 3 issues to address first.

