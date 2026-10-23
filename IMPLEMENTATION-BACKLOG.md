# Truth Implementation Backlog — Phase 10

**Status:** Proposed backlog; task completion requires code and test evidence.  
**Principle:** Small changes, explicit contracts, acceptance tests, reviewed diffs.

## T0 — Repository audit and bootstrap plan (P0)
**Goal:** Establish the real working baseline without overwriting local work.
- Inspect source tree, branch, status, dependency manifests, entry points, tests, README, and recent commits.
- Reconcile local and remote state.
- Add architecture and admin specification documents on a feature branch.
- Record verified stack and commands.

**Acceptance**
- Clean checkout instructions work.
- Existing work is preserved.
- Actual runtime and test commands are documented.
- No code or endpoint is described as present without evidence.

## T1 — Shared contracts and policy boundary (P0)
- Define versioned Task, Event, Result, Error, PermissionContext, and CorrelationId types.
- Validate inputs at the API and execution boundaries.
- Implement a mock policy evaluator with allow/deny reasons.
- Add tests for malformed payloads and denied operations.

**Acceptance:** invalid requests fail safely; a denied operation cannot reach a handler; error output contains no secrets.

## T2 — Frontend bootstrap (P0, after repository stack audit)
- Add or retain React + TypeScript + Tailwind only if compatible with the actual project.
- Configure routing, semantic design tokens, lint/typecheck/test commands.
- Implement responsive AdminShell, navigation, PageHeader, StatusBadge, EmptyState, ErrorState, Skeleton, ConfirmDialog.
- Include keyboard focus and basic WCAG checks.

**Acceptance:** shell renders at mobile/tablet/desktop widths; navigation works; typecheck/lint/tests pass.

## T3 — Admin operational vertical slice (P0)
- Dashboard with clearly labelled mock or live data.
- Task list with search/filter/status/pagination contract.
- Task detail with lifecycle timeline, error summary, correlation ID, related events.
- Activity list with explicit stale/loading/error states.
- Mock API adapter kept behind a typed interface.

**Acceptance:** a reviewer can inspect a simulated task from queued to failed/succeeded and trace related events. Mock data is never presented as live system health.

## T4 — User-facing vertical slice (P1)
- Home, Oracle workspace, Goals, Activity.
- Typed goal/task/message contracts.
- Replaceable mock Oracle adapter.
- Accessible loading, error, empty, and offline states.

**Acceptance:** sample goal can be created, associated mock tasks shown, Oracle response source is labelled, activity is traceable.

## T5 — Persistence and Kernel boundary (P1)
- Select local transactional storage appropriate to verified backend stack.
- Persist event history and current state separately.
- Add versioned schemas, provenance, timestamps, and correlation IDs.
- Define explicit state transition rules and recovery on restart.

**Acceptance:** state survives restart; duplicate event handling is tested; failed persistence does not report success.

## T6 — Authorised operations and audit (P1)
- Implement server-side role/scope checks.
- Add approvals for configured sensitive actions.
- Add structured audit events and correlation IDs.
- Implement idempotency for operations where duplicates are hazardous.
- Show actual outcome states in UI.

**Acceptance:** denied actions remain denied even if UI controls are manually invoked; actions and decisions are auditable.

## T7 — Compute, registry, and recovery (P2)
- Deterministic arithmetic/calculation registry.
- Dependency graph with cycle detection.
- Typed entity/relationship registry.
- Backup/restore and security incident workflows.

**Acceptance:** calculations are reproducible, dependency cycles are rejected, and restore is verified before service resumes.

## Definition of Done for every coding-agent task
1. Scope and touched files are listed.
2. Contracts and acceptance tests are explicit.
3. Relevant tests/typecheck/lint commands are run.
4. Diff is reviewed for security, correctness, and scope.
5. Known limitations are recorded.
6. No commit/merge occurs before review.

## Agent prompt template
**Objective:** [one bounded outcome]

**Inspect first:** [existing files/contracts]

**Implement:** [specific modules/files]

**Contracts:** [input/output/state/error]

**Acceptance tests:** [observable outcomes]

**Must not:** [out-of-scope work and security boundaries]

**Report back:** changed files, test commands/results, design decisions, remaining risks, and any unverified assumptions.
