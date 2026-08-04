---
name: repo-harness-bootstrap
description: "Bootstrap a new repository with an agent-legible harness: short AGENTS.md entrypoint, progressive docs/specs/decisions/tasks/exec-plans/research, spec traceability, mandatory changed-scope validation, check-only full finalization, scaffold planning, DTO/source-of-truth guidance, layer-direction rules, maintainability checks, and explicit Playwright E2E evaluation. Use when creating a fresh repo or applying the harness-template Copier project before the project-specific architecture settles."
---

# Repo Harness Bootstrap

## Overview

Use this skill to create the initial repository harness for a fresh project.
Keep project-specific rules in the target repo's docs and scripts; this skill
is only the bootstrap workflow.

Prefer the local `harness-template` Copier template when it is available. Do
not use this skill as a general re-harness or migration workflow for mature
repos; later changes should follow that repo's own docs and constraints.

## Workflow

1. Inspect the current directory with `pwd`, `git status --short`, and
   `rg --files`.
2. Decide whether this is:
   - a template invocation from `harness-template`,
   - or a fresh target repo that should receive the initial harness.
3. If this harness template is present, use `copier copy` with explicit
   `--data` answers when possible.
4. If the template is not present, create the same initial harness shape
   directly.
5. Before editing, identify the intended stack, package managers, frontend,
   backend, and Swift source/test directories, Apple platform/XcodeGen choices,
   and CI target.
6. Keep `AGENTS.md` short. Put durable guidance in `docs/`; put mechanical
   rules in scripts and CI.
7. Validate with the smallest available checks, then report what was added,
   what was deferred, and why.

## Required Harness Shape

Create these entry points:

- `AGENTS.md`: short table of contents and command index.
- `docs/README.md`: reading guide linking specs, decisions, and tasks.
- `docs/specs/<main-spec>-v0.1.md`: product intent, data contracts,
  architecture boundaries, validation strategy.
- `docs/specs/architecture-layering-v0.1.md`: dependency direction and
  current enforcement.
- `docs/specs/agent-governance-v0.1.md`: how to change conventions, docs,
  checks, generated artifacts, and agent-facing files.
- `docs/SPEC_TEMPLATE.md` and `docs/TRACEABILITY_GUIDE.md`: requirement,
  example, acceptance, plan, code, and test mapping conventions.
- `docs/research/engineering-principles.md` and
  `docs/research/hci-principles.md`: reusable principles for routed context.
- `docs/decisions/`: short ADR-style rationale.
- `docs/tasks/`: repeatable workflows, especially scaffold planning, schema
  generation, alignment checks, and E2E testing when a frontend exists.
- `docs/exec-plans/`: living execution plans for multi-hour features, major
  refactors, or cross-area changes.
- `./scripts/validate-changed-scope.mjs`: mandatory local completion check.
- `./scripts/validate-traceability-structure.mjs`: baseline structure check
  for specs marked `Traceability: Required`.
- `./scripts/validate-agent-governance.mjs`: agent-facing docs alignment check.
- `./scripts/finalize.sh`: check-only CI/pre-merge full gate.
- runtime feedback scripts such as `scripts/dev.sh`, `scripts/check-health.sh`,
  and `scripts/smoke.sh` when the selected stack can support them.

## Scaffold Planning

Require a scaffold planning pass before generating backend, frontend, feature,
or validation code.

The plan must record:

- selected backend/frontend stack and directories.
- product scope and user journeys to support first.
- layer map or feature folder shape.
- dependency direction; lower layers must not import runtime/UI/framework
  concerns from higher layers.
- DTO/source-of-truth strategy and freshness check.
- checks active now.
- checks deferred until the stack exists.
- open questions that block scaffolding.

Example feature split:

```text
DTO source of truth -> repo -> service -> runtime -> components/pages
```

Use this as an example, not a mandate. Replace it with the shape that fits the
project.

## Decisions To Force

Do not silently skip these:

- **DTOs**: choose one source of truth before frontend code consumes backend
  data: generated OpenAPI types, shared schema package, typed RPC, GraphQL
  codegen, or another explicit contract.
- **Layering**: document dependency direction before scaffolding code that
  crosses boundaries.
- **Maintainability**: use 700 lines as the default threshold for hand-written
  product source files unless the repo chooses a different explicit rule. List
  exceptions after the default rule. Rust is exempt: organize Rust modules by
  domain and split them only at real domain, ownership, reuse, or readability
  boundaries, not solely to satisfy a generic line limit.
- **Optimization-sensitive verification**: if meaningful runtime behavior
  depends on compiler optimizations, such as compute-heavy Rust code, document
  that local runtime verification should prefer release or optimized commands
  like `cargo test --release`, `cargo run --release`, `dotnet test -c Release`,
  or a prebuilt optimized artifact.
- **Frontend E2E**: if a frontend exists, explicitly evaluate Playwright. If
  stable browser journeys exist or are part of the first scaffold, install
  Playwright and wire `test:e2e`; otherwise record the deferral reason and
  activation condition.

## Mechanical Checks

Add checks only when they can run against real project files and fail with
actionable messages.

Typical checks:

- markdown lint for `AGENTS.md`, README files, and `docs/**/*.md`.
- traceability structure validation for behavior specs marked
  `Traceability: Required`.
- backend format/build/test gates.
- frontend format/lint/check/build gates.
- generated API contract generation and freshness checks.
- architecture-layer validator for dependency direction and direct `fetch` in
  UI files.
- 700-line hand-written product source file check, excluding documented
  exceptions such as Rust.
- Playwright impact rules through `./scripts/playwright-impact-rules.mjs`,
  validation through `./scripts/validate-playwright-impact-tags.mjs`, and
  selection through `./scripts/select-playwright-impact-tests.mjs`, with
  `test:e2e` in `./scripts/finalize.sh` and CI after frontend startup and at least one
  stable journey exist.
- for iOS/Swift: XcodeGen project generation, recommended-by-default `swift-format`,
  optional SwiftLint only after its overlapping formatting rules and ownership are
  resolved, a repository-owned
  XCTest destination resolver, one explicitly created fixed local simulator,
  compile-only `build-for-testing` evidence, strict project-owned XCTest impact
  areas after source/tests exist, and macOS CI with an explicit simulator
  destination.

`./scripts/validate-changed-scope.mjs` is mandatory before local implementation
work is marked complete. `./scripts/finalize.sh` must remain check-only and
should not start long-running dev servers. Keep startup smoke checks separate.

## Validation

After bootstrapping:

1. Render the template or inspect the edited repo.
2. Run Markdown lint when available.
3. Run syntax checks for shell and Node scripts when present.
4. Run generated `validate-traceability-structure.mjs --strict`.
5. Run generated `validate-layering.mjs` directly when it does not require
   missing app files.
6. Run `./scripts/validate-changed-scope.mjs` when the generated repo has enough
   scaffolded files for it to select meaningful checks.
7. Do not run full stack checks until the selected stack has been scaffolded.
8. Report deferred checks with concrete activation criteria.
