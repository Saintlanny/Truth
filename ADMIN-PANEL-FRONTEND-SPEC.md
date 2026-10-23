# Truth Admin Panel — Frontend Design Specification

**Version:** 1.0 draft, 2026-10-23  
**Status:** Proposed design baseline; not implemented or tested.

## Product and assumptions
Truth is a personal intelligence and software platform connecting user context, memory, goals, networks, deterministic calculations, and authorised tools through a governed runtime. The admin panel is an operations and governance console, not a replacement for the Oracle.

Initial scope: system overview, users/access, Runtime tasks, approvals, components/integrations, audit/activity, security, settings. Billing, moderation, and Truecoin operations are future modules.

Stack recommendation: React + TypeScript + Tailwind; React Router; TanStack Query; React Hook Form + Zod; Vitest + React Testing Library; Playwright. Verify repository stack before introducing dependencies. Target WCAG 2.2 AA.

## 1. Goals and non-goals
Goals: locate records quickly; understand system health and failures; manage access; review approvals; trace changes; monitor Runtime; support safe recovery.
Non-goals: unrestricted private conversation access, direct DB mutation, arbitrary code execution, permission bypass, autonomous financial transactions, full Truth World.
Efficiency targets: exact-ID lookup under 5 seconds; failure investigation begins with task detail and related events; no false success messages.

## 2. Roles and permissions
Roles: Super Admin, Operations Admin, Security Admin, Support Admin, Read-only Auditor. Use server-enforced RBAC plus resource scopes and policies.

| Module | Super Admin | Operations | Security | Support | Auditor |
|---|---|---|---|---|---|
| Overview | View | View | View | View | View |
| Users | View/Create/Edit | View | View | View/Edit limited | View |
| Roles | Manage | None | View/Edit scoped | None | View |
| Runtime/tasks | View/Edit/Approve | View/Edit/Approve scoped | View | View limited | View |
| Approvals | Approve | Approve scoped | Approve security scoped | None | View |
| Audit | View/Export | View/Export scoped | View/Export | View scoped | View/Export |
| Security events | View/Edit/Approve | View limited | View/Edit/Approve | None | View scoped |
| Integrations | Manage | Edit scoped | View | View limited | View |
| Global config | Edit | Edit limited | Edit security scoped | None | View |
| Recovery | Approve | Approve scoped | Approve security scoped | None | View |

`Edit limited` must map to explicit permissions. The Super Admin role must not silently bypass all security controls. Hide irrelevant navigation; explain disabled contextual actions; server-check every request.

## 3. Information architecture
Routes:
- `/admin`, `/admin/health`
- `/admin/users`, `/admin/users/:userId`
- `/admin/roles`, `/admin/sessions`
- `/admin/runtime`, `/admin/tasks`, `/admin/tasks/:taskId`
- `/admin/approvals`, `/admin/approvals/:approvalId`
- `/admin/components`, `/admin/integrations`
- `/admin/audit`, `/admin/audit/:eventId`
- `/admin/security`, `/admin/security/incidents/:incidentId`
- `/admin/settings/general`, `/admin/settings/security`, `/admin/settings/retention`

Sidebar 248px expanded / 72px collapsed. Top bar includes breadcrumbs, global search, `Ctrl/Cmd+K` command palette, approvals/notifications, account menu. Search only authorised records. Proposed routes are not verified API endpoints.

## 4. Core layouts
App shell with sidebar/top bar/content region. Dashboard answers: is Truth healthy, what needs attention, what is running/failing, what needs approval? List pages have title/actions, search/filter, saved views, table, pagination. Detail pages show identity/status/actions, summary, tabs, related records, dependencies, and activity. Forms use labels above fields, helper text, inline errors, and a sticky action bar on long pages. Settings grouped into General, Access, Security, Data & Retention, Integrations, System Operations.

## 5. Tables
Default columns:
- Users: ID, display name, status, role, last active, created.
- Tasks: ID, type, status, requester, started, duration, attempts.
- Approvals: ID, action, requester, risk, requested at, expiry.
- Components: name, version, health, uptime, last check, error count.
- Integrations: provider, connection status, permission scope, last sync, last error.
- Audit: time, actor, action, resource, outcome, correlation ID.
- Security events: time, severity, type, resource, source, status.

Server-side sort/filter/search; 250–350ms search debounce; page sizes 25/50/100; page-based pagination initially, cursor pagination for rapidly changing large sets. Density: compact 36px, standard 44px, comfortable 52px. Sticky headers. Saved views store query configuration, not copied data. Bulk actions require eligibility checks and review. Inline edits only for low-risk fields. CSV first; server-side export with permission checks and audit. XLSX only if justified.

## 6. Forms and validation
Use text, textarea, select, checkbox, date/time, numeric, scoped permission picker, endpoint, and masked-secret fields as appropriate. Validate on blur and submit; server validation remains authoritative. Preserve safe input after errors. Autosave only low-risk drafts/preferences. No autosave for roles, grants, security policy, recovery, or secrets. Multi-step risky actions include review and partial-failure reporting.

## 7. Destructive/sensitive actions
Risk levels: low (save preference), moderate (disable integration), high (revoke admin session/change permissions), critical (restore backup/rotate root credential/isolate major component). Use impact review, precise action label, permission recheck, and step-up authentication where policy requires. Typed confirmation only for critical actions. Undo only if reliably supported. Prefer read-only “view as user”; impersonation requires approved role, reason, persistent banner, blocked sensitive actions by default, and full audit.

## 8. Audit/activity
Record timestamp, actor, action, resource, outcome, reason where available, correlation ID, provenance, and policy version where applicable. Display occurrence and recording times separately. Show before/after diffs with sensitive values redacted. Link related tasks/events/approvals. Audit records are append-oriented; no ordinary edit/delete UI.

## 9. Dashboard/analytics
KPI candidates: active users, tasks in progress, failed tasks, pending approvals, component health, unresolved security alerts. Metrics appear only when their data sources and definitions exist; unavailable is not zero. Charts: task volume/failure rate, duration percentiles, component health, security events, approval turnaround. Date ranges: hour, 24h, 7d, 30d, custom. Show timezone and last refresh. Drill-down applies filters. Label stale data.

## 10. Component inventory
Shared components: AdminShell, SidebarNav, TopBar, PageHeader, Button, StatusBadge, SearchInput, FilterBar, SavedViewMenu, DataTable, Pagination, ColumnPicker, BulkActionBar, MetricCard, ChartPanel, DetailHeader, DetailTabs, FormField, ConfirmDialog, DiffViewer, ActivityTimeline, PermissionMatrix, ApprovalCard, HealthPanel, ConnectivityBanner, Toast, EmptyState, ErrorState, Skeleton, SessionWarning, SensitiveValue, JobProgress.

All components define default, hover, focus, selected, disabled, error, loading, and empty states where relevant. Icon-only controls need accessible names. Components do not authorise actions; backend policy does.

## 11. Visual tokens
Light: background `#F7F8FA`, surface `#FFFFFF`, secondary surface `#F0F2F5`, primary text `#171A21`, secondary text `#5D6675`, border `#DCE1E8`, accent `#315EFB`, success `#18794E`, warning `#8A5700`, danger `#B42318`.
Dark: background `#101216`, surface `#181B22`, secondary surface `#222631`, primary text `#F1F3F7`, secondary text `#B1B8C5`, border `#343A46`, accent `#8CA6FF`, success `#6DD6A2`, warning `#F5C56B`, danger `#FF8A80`.
Pending tokens: light `#6D4AFF`, dark `#B5A1FF`; validate all combinations for contrast. Colour is never the only status indicator.

Typography: page title 24/32px, section 18/28, body 14/20, compact 13/18, caption 12/16, KPI 28–32px. Spacing base 4px, scale 4/8/12/16/20/24/32/40/48/64. Controls 40px, compact 32–36px, practical touch target 44×44px. Radii 6/10/14px and pill. Lucide icons at 16/20/24px.

## 12. Responsive
Breakpoints: 0–639 mobile, 640–1023 tablet, 1024–1439 desktop, 1440+ wide. Sidebar becomes drawer on smaller screens. Tables keep key columns and expose remaining fields in row details; filters use drawers/sheets; detail sidebars stack; complex graphs become relationship lists. Critical incident acknowledgement should work on mobile; complex recovery favours desktop.

## 13. Feedback and states
Define skeleton, empty, error, denied, stale, offline, session-expired, long-running job, partial-failure states. Example: “Tasks couldn’t be loaded. Try again.” Never report success before server confirmation. Offline mutations for sensitive admin actions are prohibited. Long jobs use queued/running/succeeded/failed/cancelled status.

## 14. Accessibility
WCAG 2.2 AA. Semantic landmarks/headings/tables; keyboard access; visible focus; dialog focus trap/return; accessible sort state; live-region announcements for important updates; zoom/reflow; keyboard-only and screen-reader testing. Shortcuts: Ctrl/Cmd+K command palette; `/` search when not in a text field; `Escape` dismisses safe dismissible overlays.

## 15. Performance
Targets: LCP ≤2.5s, INP ≤200ms, CLS ≤0.1, initial JS ≤200KB compressed. Server-side pagination/filtering/aggregation. Virtualise only when measured. Lazy-load heavy graphs. Scope caches to authenticated identity and permission context. Cancel stale searches. Optimistic updates only for low-risk reversible operations. Bounded retries for safe reads; idempotency for hazardous duplicate mutations. Exports/recovery use asynchronous jobs.

## 16. Data and state
Server state: users/tasks/permissions/health/approvals/audit/config. URL state: query/filters/sort/page/tab. Local UI state: drawer/dialog/panel. Form state: values/errors/dirty. Session state: authenticated principal/expiry/permission context, validated server-side.

Suggested API patterns (not existing endpoints): `/api/admin/overview`, `/api/admin/users`, `/api/admin/users/{id}`, `/api/admin/tasks`, `/api/admin/tasks/{id}`, `/api/admin/approvals`, `/api/admin/approvals/{id}/decision`, `/api/admin/components`, `/api/admin/audit`, `/api/admin/security/events`, `/api/admin/exports`, `/api/admin/exports/{id}`. Use typed envelopes, pagination metadata, correlation IDs, ISO timestamps, structured errors. `401` reauthenticates, `403` denies, `409` shows conflict/review, `429` respects retry guidance, `5xx` preserves safe drafts. Clear sensitive caches on sign-out/account change.

## 17. Security UI
Authentication/MFA screens should follow the chosen identity provider. Support password managers and recovery. Show identity, role, environment, session state. Mask secrets by default; never prefill existing secret values. Reveal only when authorised and policy allows; log sensitive access without logging secret values. Minimise PII in tables, exports, and errors. Require explicit permission/reason for sensitive exports. UI controls do not replace backend security enforcement.

## 18. Assumptions/open questions
Defaults: internal Truth team first; integrate an existing identity system if present; operations-first MVP. Confirm whether customer-facing admin is also needed, whether identity exists, and whether first slice prioritises operations or users/access. Verify actual repo and backend contracts before implementation.
