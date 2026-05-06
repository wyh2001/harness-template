# harness-template

Reusable Copier template for the project harness conventions I keep rebuilding:

- short `AGENTS.md` as the agent entry point.
- progressive-disclosure `docs/` with specs, decisions, and repeatable tasks.
- check-only `scripts/finalize.sh`.
- markdown lint, Rust clippy/module docs, .NET gates, generated API contract checks, and an optional Playwright E2E gate.
- unified VS Code tasks, `dev.sh`, `smoke.sh`, and health checks.
- governance docs for scaffold planning, comment policy, layering, DTO source-of-truth choices, 300-line product source limits, schema generation, and E2E testing.

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

Some options intentionally describe checks before they can run. For example, DTO unification and Playwright E2E should be planned before app code grows, but the mechanical checks should only be enabled after the relevant backend/frontend scaffold exists.
