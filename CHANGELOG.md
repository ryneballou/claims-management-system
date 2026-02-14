# Claims Management System — Change Log

> **How to use this file:**
> 1. This file lives in your Git repo at the project root.
> 2. Upload a copy to your Claude Project as project knowledge.
> 3. At the end of each Claude chat, ask: *"Generate changelog entries for this session using the CHANGELOG.md format."*
> 4. Paste Claude's output into this file under the current version heading.
> 5. Commit to Git and re-upload to the Claude Project so future chats stay in sync.
>
> **Entry format:** Each entry should be a short, clear description a human developer can understand in seconds. Include file paths, function names, or module names where relevant.

---

## Unreleased

### Added
<!-- New features, endpoints, components, database tables, or capabilities -->

- Initial system architecture documentation — `docs/architecture/system-architecture.md` (to be created)
- Module structure definition with 14 domain modules — `src/modules/` structure defined
- CI/CD pipeline specification — GitHub Actions + CodePipeline, blue-green deployment
- Complete database schema design with RLS-based multi-tenancy (40+ tables) — `database/migrations/`
- Tenant isolation via PostgreSQL Row-Level Security with `current_tenant_id()` function pattern
- All tenant-scoped tables include `tenant_id` column with composite indexes for query performance
- Shared reference data schema (states, coverage types, ICD-10, CPT, NAICS codes) in `shared` schema — `database/migrations/001_create_shared_schema.sql`
- Multi-peril and multi-claimant data structures via `claim_perils` and `claim_parties` junction tables
- Time-partitioned `activity_log` table with MSK publishing support per ADR-004
- Event publishing columns (`event_published`, `event_published_at`) for outbox pattern
- TypeORM entity examples for NestJS integration per ADR-005 — `src/modules/claims/entities/`
- Database triggers for claim financials, party display names, claim numbering, status tracking
- Immutable reserve tracking with `superseded_by` chain and `current_reserves` view
- Seed data file structure for reference data loading — `database/seeds/`

### Changed
<!-- Modifications to existing features, refactors, or behavior changes -->

### Fixed
<!-- Bug fixes with brief description of what was wrong and what the fix was -->

### Removed
<!-- Deprecated or deleted features, endpoints, or files -->

### Architecture Decisions
<!-- Important design choices and the reasoning behind them — this is the most valuable section for onboarding developers -->

**Chat 1: System Architecture & Tech Stack**

- **ADR-001: Modular Monolith Architecture** — Selected modular monolith over microservices for deployment simplicity while maintaining clean module boundaries for future service extraction. Modules communicate through explicit public APIs and domain events. Rationale: Early-stage velocity, reduced operational complexity, clear extraction path when scale demands. — `docs/architecture/ADR-001-modular-monolith.md`

- **ADR-002: AWS as Primary Cloud Provider** — Selected AWS over Azure/GCP for enterprise insurance market fit, compliance maturity, and service breadth. Key services: Aurora PostgreSQL, ECS Fargate, S3, MSK, EventBridge, SageMaker. — `docs/architecture/ADR-002-aws-cloud.md`

- **ADR-003: Hybrid Multi-Tenancy** — Row-level tenant isolation with PostgreSQL RLS as default; database-per-tenant available for enterprise tier. Balances operational efficiency with enterprise isolation requirements. — `docs/architecture/ADR-003-multi-tenancy.md`

- **ADR-004: Two-Tier Event Architecture** — EventBridge for domain events (claim lifecycle, business triggers), MSK/Kafka for high-volume streams (audit logs, ML pipelines). Consistent CloudEvents-inspired envelope schema. — `docs/architecture/ADR-004-event-architecture.md`

- **ADR-005: TypeScript Full-Stack** — NestJS (TypeScript) for main backend, Next.js for web frontends, React Native for mobile. Python services for AI/ML workloads. Full-stack type safety eliminates contract mismatches. — `docs/architecture/ADR-005-typescript-stack.md`

- **ADR-006: ECS Fargate with Blue-Green Deployments** — Selected ECS Fargate over EKS for operational simplicity in early stages. Blue-green deployment strategy for zero-downtime releases with fast rollback. — `docs/architecture/ADR-006-ecs-fargate.md`

- **ADR-007: Environment Strategy** — Five core environments (local, dev, staging, production, demo) plus on-demand client sandboxes. Terraform IaC, feature flags via LaunchDarkly/AppConfig for environment-specific behavior. — `docs/architecture/ADR-007-environments.md`

**Chat 2: Database Design & Data Model**

- **ADR-DB-001: Row-Level Security for Multi-Tenancy** — Aligned with ADR-003. All tenant-scoped tables include `tenant_id` column with RLS policies enforcing isolation via `current_tenant_id()` session variable. Application middleware sets tenant context on each request. System admin bypass via `is_system_admin()` for cross-tenant operations. — `docs/architecture/ADR-DB-001-rls-tenancy.md`

- **ADR-DB-002: Audit Events Published to MSK** — Aligned with ADR-004. `activity_log` table stores audit records persistently; records also published to MSK topic `claims.audit.events`. `event_published` column tracks publication status. Failed publishes retried via scheduled job (outbox pattern). — `docs/architecture/ADR-DB-002-audit-events.md`

- **ADR-DB-003: Immutable Reserve Records** — Reserves are append-only with `superseded_by` chain for complete history. Supports reserve development analysis (triangulation) and compliance auditing. `current_reserves` view provides simple access to active reserves. — `docs/architecture/ADR-DB-003-reserve-immutability.md`

- **ADR-DB-004: Denormalized Financial Aggregates** — `total_reserve`, `total_paid`, `total_recovered` stored on `claims` table, maintained by triggers. `total_incurred` as generated column. Optimizes read-heavy dashboard and list queries. — `docs/architecture/ADR-DB-004-claim-financials.md`

- **ADR-DB-005: Quarterly Partitioning for Audit Tables** — `activity_log` and `data_change_history` partitioned by quarter on `partition_date`. Enables efficient time-scoped queries, easy archival of old partitions, and supports Kafka replay from specific time ranges. — `docs/architecture/ADR-DB-005-audit-partitioning.md`

### Database Changes
<!-- Schema migrations, new tables, altered columns, index changes -->

- Schema designed for Aurora PostgreSQL 16 per ADR-002
- Created `tenants` table for multi-tenant management with tier, settings, and billing fields
- Created `shared` schema with reference tables: `countries`, `states`, `currencies`, `lines_of_business`, `coverage_types`, `peril_codes`, `claim_status_types`, `icd10_codes`, `cpt_codes`, `body_part_codes`, `nature_of_injury_codes`, `cause_of_injury_codes`, `naics_codes`
- Created core domain tables with RLS: `parties`, `party_addresses`, `policies`, `policy_coverages`, `loss_events`, `claims`, `claim_status_history`, `claim_parties`, `claim_perils`, `reserves`, `payments`, `payment_addresses`
- Created operational tables with RLS: `tasks`, `documents`, `notes`, `correspondence`, `teams`, `users`, `user_teams`
- Created configuration tables with RLS: `jurisdiction_rules`, `lob_configurations`, `claim_status_config`, `workflow_definitions`
- Created AI/automation tables with RLS: `ai_predictions`, `automation_rules`, `automation_log`
- Created specialty module tables with RLS: `subrogation_cases`, `siu_referrals`, `vehicles`, `claim_vehicles`, `vendors`, `vendor_assignments`, `compliance_deadlines`
- Created partitioned audit tables: `activity_log`, `data_change_history` (quarterly partitions 2025-Q1 through 2026-Q1)
- All unique constraints include `tenant_id` for proper multi-tenant isolation
- Indexes optimized for RLS query patterns (`tenant_id` as leading column in composite indexes)
- Created database functions: `current_tenant_id()`, `is_system_admin()`, `update_claim_financials()`, `set_party_display_name()`, `update_user_claim_count()`, `generate_claim_number()`, `track_claim_status_change()`
- Created triggers for automatic claim number generation, status history tracking, financial aggregate updates, display name computation, user workload tracking

### API Changes
<!-- New or modified endpoints, request/response schema changes, breaking changes -->

### Integrations
<!-- External service connections, third-party API integrations, webhook configurations -->

### AI & Automation
<!-- ML model changes, automation rules, prompt engineering updates, training data changes -->

### Security & Compliance
<!-- Auth changes, encryption updates, compliance fixes, audit trail modifications -->

- Designed field-level encryption columns for PII: `ssn_encrypted`, `tax_id_encrypted`, `bank_routing_encrypted`, `bank_account_encrypted` (AES-256 with tenant-specific keys)
- SSN/Tax ID last-four columns for display/search without decryption
- Row-Level Security policies on all tenant-scoped tables
- Immutable audit trail via `activity_log` with partitioning for retention management
- Document retention tracking with `retention_category`, `retention_expires_at`, `is_legal_hold` columns

### DevOps & Infrastructure
<!-- CI/CD changes, deployment config, environment setup, monitoring -->

- Partition management strategy for audit tables (quarterly, automated creation via pg_cron or external scheduler)
- Data archival strategy: Hot (0-2 years, primary DB), Warm (2-7 years, read-only tablespace), Cold (7+ years, S3 Parquet export)
- Read replica topology: Sync HA replica for failover, async replicas for app reads, dedicated replica for reporting/ETL

### Dependencies
<!-- Added, updated, or removed packages and libraries -->

### Notes for Developers
<!-- Context, gotchas, known issues, or temporary workarounds that devs need to know about -->

**From Chat 1:**
- The modular monolith requires discipline: modules must not import from each other's internals. Use the exported module API or domain events only.
- Tenant context must be set on every request before any database operation. Middleware handles this, but be aware when writing tests.
- AI/ML services run as separate Fargate tasks with Python runtime. Communication is async via EventBridge/SQS.
- All infrastructure changes go through Terraform. No manual AWS console changes in staging/production.

**From Chat 2:**
- **Critical:** Tenant context must be set before any database operation. NestJS middleware handles this via `SET LOCAL app.current_tenant_id = '<uuid>'`.
- The `is_system_admin()` function bypasses RLS for admin operations — use sparingly and audit carefully.
- Reserves are append-only; use `current_reserves` view for active reserves, never query `reserves` table directly for current state.
- Activity log entries should be published to MSK via `AuditPublisherService`; the `event_published` column tracks publication status for outbox pattern retry.
- For enterprise-tier tenants requiring database isolation, provision separate Aurora cluster and route via tenant-aware connection pooling.
- ICD-10 and CPT code loading requires annual updates from CMS; CPT codes require AMA license.
- Partition management requires scheduled job to create future partitions before they're needed.
- All database triggers are critical for data consistency — ensure they're included in integration tests.

---

## Version History

<!--
When you're ready to cut a release, move the "Unreleased" entries into a versioned section like this:

## [0.2.0] — 2025-02-20
### Added
- ...
### Changed
- ...
(etc.)

Then create a fresh "Unreleased" section at the top.
-->

<!--
## [0.1.0] — 2025-XX-XX
### Added
- Initial project scaffolding
- ...
-->

---

## Claude Chat Index

> Track which Claude chats contributed to which parts of the system. This helps you find the original conversation if you need to revisit a decision or continue a line of work.

| Chat # | Date | Model | Topic | Key Outputs |
|---|---|---|---|---|
| 0 | 2025-02-14 | Opus 4 | Feature list & chat roadmap | `claims_management_system_features.md`, `cms_chat_roadmap.md` |
| 1 | 2025-02-14 | Opus 4 | System architecture & tech stack | ADR-001 through ADR-007, module structure, CI/CD pipeline spec |
| 2 | 2025-02-14 | Opus 4 | Database design & data model | RLS-based schema DDL (40+ tables), ADR-DB-001 through ADR-DB-005, TypeORM entity examples, MSK audit publishing pattern, partitioning strategy |

---

## Prompt to Use at the End of Each Claude Chat

Copy and paste this prompt at the end of every development chat:

```
Please generate changelog entries for everything we built or decided in this conversation.
Use the exact section headings from the CHANGELOG.md format (Added, Changed, Fixed, Removed,
Architecture Decisions, Database Changes, API Changes, Integrations, AI & Automation,
Security & Compliance, DevOps & Infrastructure, Dependencies, Notes for Developers).

Only include sections that are relevant to this session. For each entry:
- Start with a clear, concise description
- Include file paths or module names where applicable
- For architecture decisions, include the rationale
- Flag any known issues or temporary workarounds

Also provide a row for the Claude Chat Index table.
```
