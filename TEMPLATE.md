# harness-template

Reusable Copier template for the project harness conventions I keep rebuilding:

- short `AGENTS.md` as the agent entry point.
- progressive-disclosure `docs/` with specs, decisions, and repeatable tasks.
- mandatory local changed-scope validation through `scripts/validate-changed-scope.mjs`.
- check-only full finalization through `scripts/finalize.sh` for CI, pre-merge, and explicit full-gate runs.
- living execution plans under `docs/exec-plans/` for multi-hour features, major refactors, and cross-area changes.
- reusable `docs/research/` engineering and HCI principles.
- spec and traceability templates with lightweight structure validation.
- markdown lint, Rust clippy/module docs, .NET gates, generated API contract checks, governance validation, optional Playwright E2E impact selection, and an optional Swift/iOS XcodeGen harness.
- Swift/iOS support distilled from a production repository: recommended-by-default `swift-format`, optional SwiftLint with a deliberately non-formatting starter configuration, XcodeGen as project source of truth, one explicitly created fixed local simulator, a centralized XCTest runner, compile-only evidence separated from executed tests, strict project-owned XCTest impact areas, macOS CI, and warning-checked builds. SwiftLint formatting-style rules can overlap or conflict with `swift-format`; if a project expands SwiftLint into formatting, it must choose one formatting owner and may disable `swift-format`.
- unified VS Code tasks, `dev.sh`, `smoke.sh`, and health checks.
- governance docs for scaffold planning, comment policy, layering, DTO source-of-truth choices, a default 700-line product source threshold followed by documented exceptions such as domain-oriented Rust modules, schema generation, and E2E testing.
- a repository-level `Spec -> Clarifier -> Plan -> Writer -> Critic` rule for non-trivial changes without tool-specific agent configuration.
- template-local `.agents/skills/repo-harness-bootstrap` skill for bootstrapping fresh repos; Copier excludes it from generated projects.

The template intentionally does not generate a root `package.json` or `pnpm-workspace.yaml`. Harness scripts discover tools such as `markdownlint-cli2` and `openapi-typescript` from PATH first, then fall back to `pnpm dlx` or `npx -y`.

## Use

```bash
copier copy /path/to/harness-template /path/to/new-project
```

For non-interactive testing:

```bash
copier copy --defaults \
  --data project_name="Example App" \
  --data project_slug="example-app" \
  /path/to/harness-template /tmp/example-app
```

The generated scripts assume the selected backend/frontend stack exists or will be scaffolded next. If a stack is not part of the project, choose `No backend yet` or `No frontend yet` during copy.

For a standalone iOS app, select the iOS Swift harness and choose no backend/frontend. The template does not generate app source or `project.yml`; it generates the repository contract around that scaffold. Before adding the first Swift source and XCTest classes, replace the empty `scripts/xctest-impact-rules.mjs` starter with broad project-specific responsibility mappings.

Some options intentionally describe checks before they can run. For example, DTO unification and Playwright E2E impact tags should be planned before app code grows, but the mechanical checks should only be enabled after the relevant backend/frontend scaffold exists.
