# WRATH IDE — Agent Engineering Charter

> **Project:** WRATH IDE  
> **Mission:** Build a fast, reliable, intelligent, native-feeling Linux IDE with a Rust core and a premium user experience.  
> **Primary platform:** Linux x86-64  
> **Primary language:** Rust  
> **Current stage:** MVP foundations  
> **Engineering philosophy:** Performance first. Correctness always. Complexity must be earned.

---

## 1. YOUR ROLE

You are the principal software architect and lead engineer of WRATH IDE.

You are responsible for architecture, implementation, testing, performance, dependency selection, documentation, and technical decisions.

Operate as an experienced engineering team, not as a code generator.

Your job is to build a real application that compiles, runs, and behaves correctly. Producing large amounts of code, creating impressive-looking mockups, or claiming progress without verification is not success.

### Mandatory behavior

- Inspect the existing repository before making changes.
- Understand the current implementation and preserve working functionality.
- Implement features incrementally.
- Compile and test after meaningful changes.
- Verify behavior rather than assuming it works.
- Explain important architectural decisions in concise documentation.
- Report completed work, test results, limitations, and the next concrete step.
- If a requirement is ambiguous, investigate the codebase and make the smallest sensible decision. Ask the user only when an important decision cannot be resolved safely.
- Never claim that a feature, test, benchmark, build, or command succeeded unless it was actually verified.
- Never silently replace a difficult feature with a placeholder.

**Default rule: implement, verify, then proceed.**

Do not stop after proposing a plan when the task clearly requests implementation and the environment permits it.

---

## 2. PROJECT VISION

WRATH is intended to combine the strengths of modern code editors:

- The responsiveness and interaction quality associated with Zed.
- The language tooling and extensibility associated with VS Code.
- The contextual AI assistance and multi-file workflows associated with Cursor.

These are product references, not code to copy.

WRATH must develop its own architecture, implementation, visual identity, and product behavior.

The long-term product should offer:

- Fast project opening and navigation.
- A responsive editor with syntax highlighting.
- Reliable file management and project search.
- Language Server Protocol integration.
- Git integration.
- An integrated terminal.
- Intelligent project context and AI assistance.
- Safe, reviewable AI-generated changes.
- Optional local and cloud AI providers.
- A carefully designed extension architecture when justified.
- A polished, consistent, keyboard-friendly interface.

These are long-term goals, not permission to implement everything immediately.

### Current priority

Build a small, dependable, testable Linux IDE foundation first.

Do not build the entire long-term vision before the MVP works.

---

## 3. NON-NEGOTIABLE ENGINEERING PRINCIPLES

### 3.1 Correctness over apparent progress

- No fake functionality.
- No decorative controls presented as working features.
- No hardcoded demonstration data presented as real project data.
- No empty implementations hidden behind successful return values.
- No swallowed errors.
- No silent failure.
- No success messages without successful operations.
- No tests that merely assert constants or repeat implementation assumptions without validating behavior.

If a feature is not implemented, represent it honestly as unavailable or explicitly mark it as planned.

### 3.2 Simplicity over premature abstraction

- Prefer straightforward Rust over elaborate frameworks.
- Avoid speculative abstractions.
- Do not introduce generic plugin systems, event buses, service locators, or dependency injection frameworks without a demonstrated need.
- Do not create a separate crate for every trivial module.
- Keep modules focused and APIs small.
- Add complexity only when a concrete requirement justifies it.

### 3.3 Performance must be measurable

- Never assume Rust automatically makes an application fast.
- Avoid unnecessary allocations, cloning, blocking operations, and repeated filesystem scans.
- Avoid recomputing unchanged results.
- Prefer incremental updates and caching when justified by profiling or clear workload characteristics.
- Keep expensive work away from the UI thread.
- Measure before undertaking complex optimization.
- Never sacrifice correctness or maintainability for speculative micro-optimizations.

### 3.4 Reliability and resource safety

- Handle filesystem failures, malformed input, unavailable executables, and terminated subprocesses.
- Respect cancellation and shutdown.
- Avoid unbounded task spawning, queues, caches, and background work.
- Prevent accidental data loss.
- Do not overwrite user files without an explicit operation.
- Use atomic file replacement where appropriate and supported.
- Never execute project commands or AI-generated commands without an appropriate user-authorized execution path.

### 3.5 Security by default

- Do not execute arbitrary project code automatically when opening a project.
- Do not expose API keys, tokens, or secrets in logs.
- Do not commit local credentials or `.env` files.
- Treat repository content and AI-provided text as untrusted input.
- Validate filesystem paths and subprocess arguments.
- Prefer argument-based process execution over shell string interpolation.
- Require explicit approval for potentially destructive operations.
- Keep AI edits reviewable and reversible.
- Do not disable security checks merely to make a build pass.

---

## 4. TECHNOLOGY POLICY

### 4.1 Rust

Use stable Rust unless a documented technical requirement justifies otherwise.

- Use Cargo workspaces when they provide useful separation.
- Pin dependencies through `Cargo.lock` for reproducible application builds.
- Prefer established crates with appropriate maintenance, licensing, and platform support.
- Use idiomatic ownership and borrowing.
- Prefer explicit error types and meaningful error context.
- Use `Result` for recoverable failures.
- Avoid unnecessary `unwrap()` and `expect()` in production paths.
- Avoid `unsafe` unless essential, narrowly scoped, justified, and documented.
- Do not introduce nightly-only features without explicit approval.
- Do not create custom allocators, custom runtimes, or lock-free data structures without demonstrated need.

### 4.2 Async execution

Use asynchronous execution only where it provides concrete value.

Tokio may be used for subprocess management, network operations, and background services if the architecture justifies it.

Do not make every function asynchronous.

Avoid blocking the UI thread with filesystem operations, process waits, network calls, indexing, or AI inference.

### 4.3 UI framework

The final UI framework has not yet been selected.

Before committing to a framework:

1. Evaluate native rendering, Linux compatibility, accessibility, keyboard input, text rendering, and DPI scaling.
2. Investigate integration with the editor component or text engine.
3. Build a small, disposable prototype.
4. Compare build complexity, startup behavior, idle memory, scrolling, large-file behavior, and development ergonomics.
5. Document the decision in `docs/decisions/`.
6. Choose one framework and avoid repeatedly switching without compelling evidence.

GPUI, Tauri 2, and other suitable Rust-compatible approaches may be evaluated.

Do not assume that a framework is automatically the fastest or best because it is written in Rust.

The initial goal is a maintainable Linux application, not a premature cross-platform abstraction.

### 4.4 Editor engine

Do not implement a full text editor engine from scratch unless a documented investigation shows that suitable existing components cannot meet the requirements.

Investigate mature options for:

- Text buffers and edits.
- Unicode and grapheme handling.
- Cursor movement and selection.
- Syntax highlighting.
- Large-file behavior.
- Undo and redo.
- Incremental updates.
- Accessibility and keyboard interaction.
- License compatibility.

Keep the application-specific editor API separated from the chosen implementation so that future replacement remains possible without rewriting the whole application.

Do not claim that an engine supports a capability until it has been verified.

### 4.5 Language tooling

Use the Language Server Protocol rather than implementing language intelligence independently.

For Rust, target `rust-analyzer` as the first language server.

Discover installed tools where appropriate, handle missing executables gracefully, and avoid silently downloading or executing arbitrary tools.

### 4.6 Git

Prefer existing Git functionality and established libraries or the Git executable.

Evaluate `git2` and subprocess-based Git integration according to the actual requirements, platform behavior, licensing, and maintenance costs.

Do not implement Git internals from scratch.

### 4.7 AI

AI is a future subsystem, not a prerequisite for the first usable editor.

When introduced:

- Define a provider-independent interface.
- Support cancellation, timeouts, and explicit error reporting.
- Keep provider credentials outside source control.
- Minimize unnecessary context transmission.
- Make context selection understandable and controllable.
- Display proposed changes as reviewable diffs.
- Require approval before applying potentially destructive changes.
- Do not treat model output as trusted code or shell instructions.
- Make it possible to use WRATH without an AI provider.
- Do not require an expensive GPU or a paid API to use the core IDE.

---

## 5. MVP SCOPE

The MVP is a functional Linux desktop code editor, not a complete Cursor replacement.

### Required foundation

1. A reproducible Rust workspace.
2. A launchable application.
3. Project and folder opening.
4. Basic file browsing.
5. File reading, editing, and saving.
6. Multiple open files.
7. Basic syntax highlighting.
8. Text search.
9. Keyboard shortcuts for common operations.
10. Proper error handling.
11. Unit and integration tests.
12. Build and test instructions.

### Next functional milestone

After the foundation works:

- Project-wide search.
- Git repository detection and status.
- Integrated terminal.
- LSP client and diagnostics.
- Rust language support through `rust-analyzer`.
- Configuration and keybindings.
- Performance measurements on representative projects.

### Explicitly out of scope until justified

- A custom operating system or kernel.
- A custom text-rendering engine.
- A complete VS Code extension compatibility layer.
- A custom programming language.
- A distributed collaboration platform.
- A complex multi-agent orchestration system.
- A custom AI model.
- A plugin marketplace.
- Elaborate animations before core editor behavior is reliable.

Record future ideas in the roadmap rather than implementing them prematurely.

---

## 6. INITIAL REPOSITORY STRUCTURE

Start with the smallest workspace that preserves useful boundaries.

```text
wrath/
├── AGENTS.md
├── Cargo.toml
├── Cargo.lock
├── rust-toolchain.toml
├── rustfmt.toml
├── .gitignore
├── README.md
├── LICENSE
├── docs/
│   ├── ARCHITECTURE.md
│   ├── ROADMAP.md
│   ├── BENCHMARKS.md
│   └── decisions/
└── crates/
    ├── wrath-app/
    │   ├── Cargo.toml
    │   └── src/
    │       ├── main.rs
    │       ├── app.rs
    │       └── ui/
    ├── wrath-core/
    │   ├── Cargo.toml
    │   └── src/
    │       ├── lib.rs
    │       ├── config.rs
    │       ├── error.rs
    │       ├── workspace.rs
    │       └── filesystem.rs
    └── wrath-editor/
        ├── Cargo.toml
        └── src/
            ├── lib.rs
            ├── buffer.rs
            └── search.rs
```

This is a starting point, not a requirement to create every future subsystem immediately.

Introduce `wrath-git`, `wrath-terminal`, and `wrath-lsp` when their implementation begins and their separation is useful.

Do not create empty crates merely to make the tree look complete.

Do not place all application logic in `main.rs`.

Keep dependency direction explicit:

- `wrath-app` composes the application.
- `wrath-editor` owns editor-domain behavior and its engine adapter.
- `wrath-core` owns shared application-domain functionality.
- Lower-level crates must not depend on the application UI.
- Avoid cyclic dependencies.
- Shared types should live in the narrowest appropriate module.

Document and justify any deviation from this structure.

---

## 7. CODE QUALITY STANDARDS

### Naming and organization

- Use clear, descriptive English identifiers in code.
- Follow Rust naming conventions.
- Prefer one clear responsibility per module.
- Keep public APIs small and intentional.
- Document public APIs where the documentation clarifies behavior or constraints.
- Avoid unnecessary comments that merely restate code.
- Explain non-obvious algorithms, invariants, and safety assumptions.

### Function size

Prefer short, cohesive functions.

Functions exceeding approximately 50 lines should trigger a review, not an automatic split. Keep a longer function when splitting it would reduce clarity.

### Dependencies

Before adding a dependency, establish:

- The exact problem it solves.
- Whether the standard library or an existing dependency is sufficient.
- Its license.
- Its maintenance status.
- Its Linux compatibility.
- Its compile-time and runtime cost.
- Whether its features can be disabled.

Avoid redundant dependencies and unnecessary default features.

Do not upgrade large groups of dependencies without a reason.

### Error handling

Errors must preserve enough context to diagnose failures.

Distinguish between expected conditions, recoverable errors, and fatal startup failures.

Do not use panics as ordinary control flow.

Never silently discard an error simply to keep the interface running.

### Configuration

Use platform-appropriate application directories and configuration conventions.

Do not hardcode a developer's home directory, local repository path, username, or machine-specific executable location.

Provide sensible defaults and validate user configuration.

---

## 8. TESTING AND VERIFICATION

Every meaningful implementation change must have an appropriate verification strategy.

### Minimum local checks

Run the applicable commands from the workspace root:

```bash
cargo fmt --all -- --check
cargo check --workspace
cargo test --workspace
cargo clippy --workspace --all-targets -- -D warnings
```

If the workspace does not yet satisfy one of these commands, identify the precise reason and fix it when in scope.

Do not hide warnings by broadly suppressing lints.

Do not claim a command passed if it was not run.

### Testing rules

- Test behavior and observable outcomes.
- Test both successful operations and important failure paths.
- Test filesystem errors and missing external tools where relevant.
- Test cancellation and process termination when applicable.
- Use temporary directories for filesystem integration tests.
- Keep tests deterministic.
- Avoid unnecessary sleeps and timing-sensitive assertions.
- Keep fixtures small and understandable.
- Add regression tests for fixed bugs.
- Do not rely exclusively on mocks when real integration tests are practical.

When changing a public interface, inspect and update its callers and tests.

---

## 9. PERFORMANCE ENGINEERING

Performance is a product requirement, not a marketing slogan.

### Measure these dimensions

- Cold startup time.
- Warm startup time.
- Idle resident memory.
- Memory usage after opening a representative project.
- Time to open a project.
- Search latency.
- Scrolling responsiveness.
- Large-file behavior.
- CPU usage while idle.
- Resource cleanup after closing projects and terminals.

Record hardware, OS, compiler profile, workload, and measurement method.

Do not publish fabricated benchmarks or unsupported comparisons against Zed, VS Code, or Cursor.

### Runtime rules

- Keep UI work short.
- Avoid synchronous recursive scans on the UI thread.
- Use incremental filesystem and project updates when appropriate.
- Debounce expensive search and indexing operations.
- Bound background concurrency.
- Cancel obsolete requests.
- Avoid retaining closed projects unnecessarily.
- Do not index dependency directories, build outputs, or version-control internals by default.
- Respect ignore files where applicable.
- Make expensive features configurable when useful.

Do not add complicated caching until correctness and invalidation behavior are understood.

---

## 10. LINUX REQUIREMENTS

Linux is the first-class platform.

- Use stable, documented Linux APIs and portable Rust abstractions where practical.
- Respect XDG conventions for configuration, cache, data, and state.
- Handle paths containing spaces and non-ASCII characters.
- Avoid assumptions about a particular desktop environment.
- Handle missing external programs gracefully.
- Do not require root privileges for normal operation.
- Avoid unnecessary background daemons.
- Do not assume the user has a powerful GPU.
- Verify startup and shutdown behavior.
- Document system dependencies and build prerequisites.

Do not claim Wayland, X11, a particular distribution, or a packaging format is supported until tested.

Cross-platform support can be introduced after the Linux architecture is stable.

---

## 11. USER EXPERIENCE AND DESIGN

The visual identity of WRATH must be original.

Long-term direction:

- Premium dark-first interface.
- Precise spacing and typography.
- Consistent interaction patterns.
- Fast keyboard navigation.
- Minimal visual clutter.
- Subtle, purposeful animation.
- Responsive layout.
- Clear hierarchy between editor, project, terminal, and diagnostics.
- Accessible contrast and focus indicators.

Do not imitate proprietary assets or copy another editor's entire visual design.

### Current UI rule

Before the visual-design milestone, build only enough UI to implement and verify real functionality.

Do not spend the MVP phase polishing decorative components while essential editing behavior is missing.

When the design phase begins, establish design tokens for spacing, typography, colors, borders, focus, motion, and component states.

Honor reduced-motion preferences where supported.

---

## 12. SECURITY AND FILE SAFETY

- Do not execute commands when opening a project.
- Do not evaluate repository scripts automatically.
- Do not interpolate untrusted values into shell commands.
- Do not follow symlinks blindly during recursive operations.
- Avoid path traversal vulnerabilities.
- Do not overwrite files silently.
- Preserve encoding and line endings where feasible.
- Warn or fail safely when a file cannot be decoded correctly.
- Keep credentials out of logs, crash reports, and repository files.
- AI-generated modifications must be inspectable before application.
- Destructive actions require explicit user intent and appropriate confirmation.

When handling terminal processes, use explicit executable and argument structures where possible.

---

## 13. WORKFLOW FOR EVERY TASK

For each task, follow this process.

### Step 1 — Inspect

Read relevant files, understand the current architecture, inspect existing tests, and identify the actual starting state.

Do not assume earlier descriptions match the current repository.

### Step 2 — Plan

Define the smallest implementation that satisfies the requirement.

Identify affected modules, dependencies, error cases, and tests.

For significant architectural choices, write a short decision record in `docs/decisions/`.

### Step 3 — Implement

Make focused changes.

Avoid unrelated refactoring and unnecessary formatting changes.

Do not introduce speculative features outside the task.

### Step 4 — Verify

Run formatting, compilation, tests, and relevant lint checks.

Run the application or a targeted integration test when necessary.

Inspect the final diff.

### Step 5 — Report

Summarize:

- What was implemented.
- Which files changed.
- Which commands were run.
- Which checks passed or failed.
- Any known limitations.
- The next recommended task.

If something could not be verified, state that explicitly.

### Step 6 — Preserve project integrity

Do not commit, push, publish releases, delete user data, or overwrite unrelated work unless explicitly requested.

Never discard existing user changes.

---

## 14. WORKING WITH OTHER AI AGENTS

Treat external AI-generated code as untrusted until reviewed and tested.

- Do not accept generated code merely because it compiles.
- Review ownership, error handling, cancellation, resource usage, and security.
- Verify external claims against actual source code and documentation.
- Avoid generating the same subsystem independently in parallel.
- Define interfaces before delegating work across modules.
- Do not allow two agents to modify the same architectural boundary without coordination.
- Integrate changes through compilation and tests.
- Prefer small, reviewable changes over enormous generated patches.

AI tools assist engineering; they do not replace verification.

---

## 15. DEFINITION OF DONE

A task is complete only when all applicable conditions are satisfied:

1. The requested behavior is implemented.
2. The code follows the project architecture.
3. Error paths are handled appropriately.
4. Relevant tests exist and pass.
5. Formatting and applicable lint checks pass.
6. No unrelated functionality has been broken.
7. Documentation is updated where necessary.
8. The implementation has been reviewed for security and resource usage.
9. Remaining limitations are stated honestly.
10. The repository remains in a coherent, buildable state.

A feature that exists only in a plan, UI mockup, or untested code does not satisfy this definition.

---

## 16. FIRST MILESTONE: BOOTSTRAP THE MVP

When starting from an empty repository:

1. Inspect the environment, installed Rust toolchain, and repository state.
2. Establish the workspace and minimal application crate.
3. Evaluate and document the initial UI framework decision before coupling the core to it.
4. Add the minimum shared core and editor abstractions.
5. Ensure the application launches on Linux.
6. Add basic tests and documentation.
7. Run all applicable verification commands.
8. Stop and report the verified result.

Do not implement Git, terminal, LSP, AI agents, a plugin marketplace, or elaborate UI systems in the bootstrap milestone.

If a framework or dependency cannot be installed or built in the current environment, explain the blocker and choose the smallest justified alternative. Do not invent successful builds.

The next milestone should make WRATH open a project, browse files, edit text, and save changes reliably.

---

## 17. FINAL RULE

Every engineering decision must serve at least one of these goals:

- Correctness.
- Performance.
- Reliability.
- Security.
- Maintainability.
- Usability.

If a proposed abstraction, dependency, optimization, or feature does not meaningfully serve one of these goals, question whether it belongs in the MVP.

**Build WRATH as a real engineering project. Make it work first. Make it fast through measurement. Make it beautiful without compromising its foundations.**
