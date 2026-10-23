# Truth Master Architecture v1.0 (Proposed)

**Status:** Design baseline; not yet implemented or validated in code.  
**Purpose:** Establish shared boundaries, data contracts, responsibilities, and dependencies so coding agents can implement Truth in small, testable increments.

## 1. Product definition
Truth is a personal intelligence and software platform connecting user context, memory, goals, networks, deterministic calculations, and authorised tools through a governed runtime. The long-term vision is local-first and self-contained where host-device capabilities permit; this is not a requirement to deliver full autonomy in the MVP.

## 2. Architectural principles
1. Contract-first: components communicate through versioned, validated contracts.
2. Governance before execution: every consequential operation passes policy and capability checks.
3. Separate deterministic computation from probabilistic model reasoning.
4. State changes are explicit, validated, and traceable.
5. Data, code, permissions, capabilities, and instructions are distinct concepts.
6. External/model-produced content is untrusted until validated.
7. Host independence: the application runs above the existing OS and respects its permission/resource boundaries.
8. Local-first direction without promising unsupported offline or Android background behaviour.
9. Provenance, timestamps, correlation IDs, and schema versions are recorded where needed.
10. No feature is called implemented until its code and tests have been inspected.

## 3. Layered system map
- **Interfaces:** Truth user app, Admin console, future external connectors.
- **API / Host Adapter:** authentication context, typed request/response contracts, host-specific capabilities.
- **Policy Gate:** authorisation, scope, risk, approval, and resource policy checks.
- **Runtime:** component lifecycle, task/event dispatch, cancellation, timeouts, bounded retries, health, shutdown.
- **Kernel:** authoritative state transition and commit boundaries.
- **Oracle:** interpretation, retrieval, reasoning, scenario analysis, recommendations.
- **PI (Personal Interface/Intelligence):** coordinates user goals, preferences, tasks, approvals, and progress.
- **Compute Engine:** deterministic calculations, declared rules, dependency resolution, scenario isolation.
- **Persistence:** event history, current state, memory records, retrieval indexes, recovery.
- **Network Registry:** entities and typed relationships.
- **Dependency Matrix:** operational prerequisites, execution ordering, cycle detection, impact analysis.
- **TIR:** shared typed intermediate representation for supported entities, events, tasks, commands, capabilities, permissions, results, evidence references, and transformations.
- **Cross-cutting services:** security, audit, observability, resource management, backup/recovery.

## 4. Core request lifecycle
`Receive → Validate → Interpret → Retrieve Context → Plan → Authorise → Execute → Verify → Commit → Audit/Observe`

Not every request uses every step. Read-only queries should not be forced through mutation workflows. Model output is not executable authority. An operation must be routed through registered handlers and permission enforcement.

## 5. Canonical data concepts
- **Observation:** incoming information with source and capture time.
- **Event:** a recorded occurrence; distinguish occurrence time from recording time.
- **StateSnapshot:** accepted current state at a version/revision.
- **MemoryRecord:** retrievable contextual record with provenance and sensitivity.
- **Entity:** stable ID plus typed attributes and validity.
- **Relationship:** typed, provenance-bearing link between entity IDs.
- **Task:** requested unit of work with type/version, actor, permissions, lifecycle status, timestamps, correlation ID, and result/error references.
- **Command:** request to perform an operation; distinct from an event that reports what happened.
- **Capability:** a declared operation a component can perform; not itself a permission grant.
- **PermissionGrant:** scoped, time-bounded or condition-bound authorisation.
- **Result:** validated output and status from a task or calculation.
- **EvidenceReference:** pointer to supporting information and its provenance.
- **TransformationRecord:** input/output schemas, versions, information preserved/lost, side effects, and validation result.

All persisted objects should be versioned where compatibility matters. Do not collapse unknown, missing, zero, false, and empty into one value.

## 6. Runtime and task lifecycle
Suggested states: `RECEIVED → VALIDATED → QUEUED → RUNNING → SUCCEEDED`. Alternative states: `REJECTED`, `FAILED`, `CANCELLED`, `TIMED_OUT`, `WAITING_FOR_APPROVAL`.

Requirements:
- Registered handlers only.
- Validate inputs before dispatch.
- Enforce permission at execution boundary.
- Use bounded retries and idempotency where duplicates could cause harm.
- Do not claim exactly-once execution without proof.
- Verify outcomes before committing success.
- Persist enough state to recover from restart and expose partial failures.
- Support safe shutdown and resource limits.

## 7. Security and recovery
- Least privilege and scoped permissions.
- Separate authentication, capability registration, and permission grants.
- Treat external payloads, model output, imported files, and tool results as untrusted.
- Never store secrets in ordinary audit logs.
- Audit actor, action, target, policy decision/version, timestamps, result, and correlation ID.
- Containment should be scoped and reversible where possible.
- Recovery should preserve evidence, restore a verified point, validate configuration/state/permissions, test, and gradually restore service.
- Do not expose arbitrary shell execution or unrestricted agents in the MVP.

## 8. Dependency and traceability summary
| Specification | Depends on | Initial acceptance evidence |
|---|---|---|
| Host/local autonomy | Runtime boundary and deployment assumptions | Host constraints documented |
| Runtime/execution | Task/event contracts | Lifecycle tests pass |
| TIR/interoperability | Versioned schemas and validation | Invalid records rejected |
| Compute Engine | TIR + dependency resolver | Reproducible calculation and cycle tests |
| State/memory | Persistence + event schema | Restart persistence tests |
| Network Registry | Entity/relationship schemas | Relationship validation and traversal tests |
| Oracle/PI | Retrieval, state, policy, Runtime | Mock reasoning, goal/task workflow tests |
| Security/recovery | Identity, permissions, audit, persistence | Denied action and recovery tests |
| User frontend | API contracts and design tokens | Home/Oracle/Goals/Activity tests |
| Admin frontend | Admin API, RBAC, Runtime, audit | Task inspection and permission-denied tests |

## 9. MVP boundary
First deliver an executable vertical slice with typed contracts, validation, a mock policy gate, a simple Runtime/task lifecycle, local persistence where appropriate, and a small admin UI that displays simulated or actual data with the source clearly labelled. Add real integrations only when contracts and permissions are defined.

## 10. Explicitly out of scope for MVP
- Custom operating system or unrestricted self-contained device runtime.
- Universal binary/source translator.
- Universal connector support or arbitrary app reverse-engineering.
- Unrestricted agent execution or self-granted permissions.
- Autonomous financial transactions.
- Full Truth World, Truecoin, global real-time world graph, or universal ontology.
- Claims of human-level cognition or perfect predictions.

## 11. Proposed architecture decisions
- **ADR-001:** Truth initially runs as an application above the existing host OS.
- **ADR-002:** Runtime coordinates execution; Kernel governs state transitions; Oracle reasons and recommends; PI coordinates user goals and work.
- **ADR-003:** All consequential operations pass through registered handlers and the policy boundary.
- **ADR-004:** TIR starts as a language-neutral, JSON-compatible typed representation.
- **ADR-005:** Deterministic calculations use explicit procedures, versioned inputs/rules, units, and recorded results; AI prose is not the source of mathematical truth.
- **ADR-006:** Event history and current state are separate concepts; indexes must be rebuildable where feasible.
- **ADR-007:** Real-world relationships and operational dependencies use different edge types and cycle rules.
- **ADR-008:** The admin UI cannot directly mutate internal state or bypass the Runtime/Policy Gate.

## 12. Change control
Each architecture change must state: decision, motivation, alternatives, security impact, compatibility impact, affected contracts, tests, and migration/rollback approach.
