# Claims Management System — Change Log

> **How to use this file:**
> 1. This file lives in your Git repo at the project root.
> 2. Upload a copy to your Claude Project as project knowledge.
> 3. At the end of each Claude chat, ask: *"Generate changelog entries for this session using the CHANGELOG.md format."*
> 4. Paste Claude's output into this file under the current version heading.
> 5. Commit to Git and re-upload to the Claude Project so future chats stay in sync.
>
> **Entry format:** Each entry should be a short, clear description a human developer can read and understand in seconds. Include file paths, function names, or module names where relevant.

---

## Unreleased

### Added
<!-- New features, endpoints, components, database tables, or capabilities -->
<!-- Example: - Claim intake API endpoint (`POST /api/v1/claims`) with FNOL validation — see `src/api/claims/intake.ts` -->

### Changed
<!-- Modifications to existing features, refactors, or behavior changes -->
<!-- Example: - Refactored reserve calculation to support multi-claimant splits — `src/services/reserves/calculator.ts` -->

### Fixed
<!-- Bug fixes with brief description of what was wrong and what the fix was -->
<!-- Example: - Fixed duplicate claim detection failing on hyphenated last names — `src/utils/fuzzyMatch.ts` -->

### Removed
<!-- Deprecated or deleted features, endpoints, or files -->
<!-- Example: - Removed legacy XML intake parser — replaced by JSON-based intake in v0.3.0 -->

### Architecture Decisions
<!-- Important design choices and the reasoning behind them — this is the most valuable section for onboarding developers -->
<!-- Example: - Chose event-driven architecture with Kafka for claim lifecycle events. Rationale: decouples core engine from notifications, audit logging, and analytics. See ADR-003. -->

### Database Changes
<!-- Schema migrations, new tables, altered columns, index changes -->
<!-- Example: - Added `jurisdiction_rules` table with columns: state_code, rule_type, deadline_days, notice_template_id — migration `20250214_001_add_jurisdiction_rules.sql` -->

### API Changes
<!-- New or modified endpoints, request/response schema changes, breaking changes -->
<!-- Example: - `GET /api/v1/claims/:id` response now includes `fraud_score` field (float, 0-1) — non-breaking addition -->

### Integrations
<!-- External service connections, third-party API integrations, webhook configurations -->
<!-- Example: - Integrated ISO ClaimSearch API for fraud history lookup — `src/integrations/isoClaimSearch/` -->

### AI & Automation
<!-- ML model changes, automation rules, prompt engineering updates, training data changes -->
<!-- Example: - Implemented severity scoring model v1 using XGBoost — trained on 50k historical claims, AUC 0.87 — `src/ai/severityScoring/` -->

### Security & Compliance
<!-- Auth changes, encryption updates, compliance fixes, audit trail modifications -->
<!-- Example: - Added field-level encryption for SSN and DOB using AES-256 — `src/security/fieldEncryption.ts` -->

### DevOps & Infrastructure
<!-- CI/CD changes, deployment config, environment setup, monitoring -->
<!-- Example: - Added Terraform config for multi-tenant RDS provisioning — `infra/terraform/rds/` -->

### Dependencies
<!-- Added, updated, or removed packages and libraries -->
<!-- Example: - Added `kafkajs@2.2.4` for event streaming -->

### Notes for Developers
<!-- Context, gotchas, known issues, or temporary workarounds that devs need to know about -->
<!-- Example: - The workflow engine currently uses a simple state machine. Migration to a full BPMN engine is planned for v0.6.0 — don't over-invest in the current implementation. -->

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
<!-- | 1 | 2025-XX-XX | Opus 4 | System architecture & tech stack | ADR-001 through ADR-005, architecture diagram | -->
<!-- | 2 | 2025-XX-XX | Opus 4 | Database design & data model | ER diagram, initial migration scripts | -->
<!-- | 3 | 2025-XX-XX | Sonnet 4.5 | Auth & security architecture | RBAC schema, SSO integration design | -->

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
