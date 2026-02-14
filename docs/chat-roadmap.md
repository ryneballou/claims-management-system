# Claims Management System — Claude Chat Roadmap

Use this as a checklist and guide for spinning up focused Claude conversations. Each chat stays scoped to a specific domain so context doesn't get diluted.

---

## How to Pick the Right Model

| Model | Best For |
|---|---|
| **Claude Opus 4** | Complex architecture decisions, nuanced business logic, multi-system design, anything requiring deep reasoning across many tradeoffs |
| **Claude Sonnet 4.5** | Code generation, UI/UX builds, API design, database schemas, integration work — the workhorse for most implementation chats |
| **Claude Haiku 4.5** | Quick references, simple templates, boilerplate generation, config files, lookup tables |

---

## Phase 1 — Foundation & Architecture

### Chat 1: System Architecture & Tech Stack Selection
**Model:** Opus 4
**Purpose:** Make the big structural decisions before writing any code.
**Topics to cover:**
- Monolith vs. microservices vs. modular monolith
- Cloud provider selection (AWS / Azure / GCP) and infrastructure design
- Multi-tenant architecture strategy (shared DB vs. schema-per-tenant vs. DB-per-tenant)
- Event-driven architecture and message broker selection (Kafka, RabbitMQ, etc.)
- Frontend framework decision (React, Next.js, Angular, etc.)
- Backend framework and language decision
- CI/CD pipeline and deployment strategy
- Environment strategy (dev, staging, production, sandbox per client)

### Chat 2: Database Design & Data Model
**Model:** Opus 4
**Purpose:** Design the core schema that everything else depends on.
**Topics to cover:**
- Entity-relationship model (claims, policies, claimants, payments, reserves, tasks, documents, users)
- Multi-peril and multi-claimant data structures
- Jurisdiction and line-of-business configuration tables
- Audit trail and versioning strategy (event sourcing vs. audit tables)
- Document metadata schema
- Data partitioning and archival strategy
- Read replicas and reporting database design
- Seed data and reference data (state codes, coverage types, ICD/CPT codes, NAICS codes)

### Chat 3: Authentication, Authorization & Security Architecture
**Model:** Sonnet 4.5
**Purpose:** Design the security layer before building features on top of it.
**Topics to cover:**
- SSO implementation (SAML 2.0 / OAuth 2.0 / OpenID Connect)
- Role-based access control (RBAC) model with granular permissions
- Multi-tenant data isolation enforcement
- API authentication and rate limiting
- Encryption strategy (at rest, in transit, field-level for PII/PHI)
- MFA implementation
- HIPAA and SOC 2 compliance requirements baked into design
- Session management and audit logging

---

## Phase 2 — Core Claims Engine

### Chat 4: Claim Intake & FNOL Workflow
**Model:** Sonnet 4.5
**Purpose:** Build the front door of the system.
**Topics to cover:**
- Configurable FNOL form builder (by LOB and jurisdiction)
- Multi-channel intake (web, mobile, API, email parsing, EDI)
- Policy lookup and coverage verification at intake
- Duplicate claim detection logic
- Initial reserve suggestion engine
- Auto-assignment rules engine
- Intake validation and business rules
- Batch/bulk claim import

### Chat 5: Claim Lifecycle & Workflow Engine
**Model:** Opus 4
**Purpose:** Design the configurable state machine that drives claim processing.
**Topics to cover:**
- Claim status and sub-status state machine (transitions, guards, side effects)
- No-code/low-code visual workflow builder design
- Event-driven triggers and automated actions
- Task generation and SLA enforcement
- Escalation and authority-level logic
- Diary/follow-up system
- CAT event management and bulk operations
- Claim reopening and closure rules
- Subrogation and salvage workflows

### Chat 6: Financial Engine — Reserves, Payments & Accounting
**Model:** Opus 4
**Purpose:** Money is the most sensitive part — get this right.
**Topics to cover:**
- Reserve setting and approval workflows
- Reserve change tracking and adequacy alerts
- Payment processing (checks, EFT/ACH, multi-payee)
- Payment approval authority levels
- Partial, advance, and final settlement logic
- 1099 generation and tax reporting
- Subrogation and deductible recovery tracking
- General ledger integration and journal entry design
- Financial reconciliation and controls
- IBNR reserve tracking

---

## Phase 3 — Document & Communication Layer

### Chat 7: Document Management System
**Model:** Sonnet 4.5
**Purpose:** Build the document backbone.
**Topics to cover:**
- Document storage architecture (S3/Blob with metadata in DB)
- Upload, tagging, and classification system
- OCR and intelligent document parsing pipeline
- Version control and audit trail
- Secure external sharing (pre-signed URLs, access tokens)
- Document retention and purge policies
- Search and retrieval (full-text search integration)
- File type handling and preview generation

### Chat 8: Correspondence & Notification Engine
**Model:** Sonnet 4.5
**Purpose:** Automate all outbound communication.
**Topics to cover:**
- Template engine design (letters, emails, SMS) with merge fields
- Jurisdiction-specific template variants
- Automated milestone notifications to claimants
- Bulk correspondence for CAT events
- Email integration (Microsoft 365 / Google Workspace)
- SMS gateway integration (Twilio or similar)
- Notification preferences and opt-out management
- E-signature integration (DocuSign / HelloSign)
- In-app messaging between adjuster and claimant

---

## Phase 4 — AI & Intelligence Layer

### Chat 9: AI Triage, Severity Scoring & Straight-Through Processing
**Model:** Opus 4
**Purpose:** Design the AI brain that handles claim routing and auto-adjudication.
**Topics to cover:**
- Claim complexity/severity scoring model design
- Training data strategy and feature engineering
- Straight-through processing (STP) criteria and guardrails
- Confidence thresholds and human-in-the-loop fallback
- Model monitoring, drift detection, and retraining pipeline
- Explainability and audit trail for AI decisions
- A/B testing framework for model performance

### Chat 10: Fraud Detection & SIU Module
**Model:** Opus 4
**Purpose:** Build the fraud detection pipeline.
**Topics to cover:**
- Rules-based fraud flags (configurable red flags)
- ML fraud scoring model architecture
- Network/graph analysis for organized fraud rings
- SIU case management workflow
- Integration with ISO ClaimSearch and NICB
- Social media and public records scanning
- Suspicious activity dashboards and reporting
- False positive management and feedback loops

### Chat 11: AI Copilot & NLP Features
**Model:** Opus 4
**Purpose:** Design the adjuster-facing AI assistant.
**Topics to cover:**
- Claim file summarization (unstructured notes, medical records, statements)
- Timeline reconstruction from claim activity
- AI-drafted correspondence and next-step suggestions
- Natural language search across claims and documents
- Medical bill review and CPT/ICD validation
- Sentiment analysis on claimant communications
- Predictive litigation scoring
- Photo-based damage estimation (auto/property)
- AI reserve recommendations with explainability
- LLM selection, prompt engineering, and RAG architecture
- Responsible AI guardrails and hallucination prevention

---

## Phase 5 — Integrations & API Platform

### Chat 12: API Platform & Developer Portal
**Model:** Sonnet 4.5
**Purpose:** Build the connectivity layer that makes the system extensible.
**Topics to cover:**
- RESTful API design (resource modeling, versioning, pagination)
- GraphQL consideration for flexible querying
- Webhook system for event-driven integrations
- API authentication (OAuth 2.0 client credentials, API keys)
- Rate limiting, throttling, and quotas
- Developer portal with interactive docs (Swagger/OpenAPI)
- Sandbox environment for third-party testing
- Bulk import/export endpoints
- ACORD standard message support
- EDI (X12 837/835) support for health claims

### Chat 13: Third-Party Data & Service Integrations
**Model:** Sonnet 4.5
**Purpose:** Connect to the ecosystem of insurance data services.
**Topics to cover:**
- Policy administration system integration patterns
- Verisk / CoreLogic / ISO ClaimSearch connectors
- Weather and CAT data feeds (NOAA, CoreLogic)
- MVR and public records services
- Medical fee schedule databases
- Geocoding and mapping (Google Maps, Mapbox)
- Telephony/CTI integration for call logging
- CRM integration (Salesforce, HubSpot)
- ERP/accounting connectors (SAP, Oracle, NetSuite, QuickBooks)
- iPaaS strategy (MuleSoft, Workato, Zapier)

---

## Phase 6 — User Experience

### Chat 14: Adjuster Desktop & Internal UI
**Model:** Sonnet 4.5
**Purpose:** Build the primary interface adjusters live in all day.
**Topics to cover:**
- Claim detail view layout and information hierarchy
- Adjuster dashboard (workload, tasks, aging, SLAs)
- Inline editing and quick actions
- Advanced search and filtering
- Keyboard shortcuts and power-user features
- Responsive design for tablet use in the field
- Dark mode and accessibility (WCAG 2.1 AA)
- Configurable views and saved filters per user

### Chat 15: Mobile Field Adjuster App
**Model:** Sonnet 4.5
**Purpose:** Build the mobile experience for field adjusters.
**Topics to cover:**
- Native vs. PWA vs. hybrid decision
- Photo/video capture with annotation
- Voice-to-text notes
- GPS check-in and location tracking
- Offline mode with sync
- Push notifications
- Barcode/QR scanning for asset identification
- Mobile-optimized task and diary management

### Chat 16: Claimant Self-Service Portal & Chatbot
**Model:** Sonnet 4.5
**Purpose:** Build the external-facing claimant experience.
**Topics to cover:**
- Claim filing wizard
- Document upload interface
- Real-time claim status tracker
- Secure messaging with adjuster
- Appointment scheduling for inspections
- FAQ chatbot design and training
- Multilingual support strategy
- Branding and white-label configuration
- CSAT/NPS survey integration
- Accessibility compliance

---

## Phase 7 — Reporting & Analytics

### Chat 17: Dashboards, Reporting & BI
**Model:** Sonnet 4.5
**Purpose:** Give stakeholders visibility into everything.
**Topics to cover:**
- Operational dashboards (inventory, aging, cycle time, SLA compliance)
- Executive dashboards (loss ratios, combined ratios, trending)
- Adjuster performance scorecards
- Ad hoc report builder design
- Scheduled report delivery
- Data export (PDF, Excel, CSV)
- Embedded BI vs. external BI integration (Tableau, Power BI, Looker)
- Data warehouse/data lake architecture for analytics
- Actuarial reporting (triangulations, development factors, IBNR)

---

## Phase 8 — Specialty Modules

### Chat 18: Workers' Compensation Module
**Model:** Sonnet 4.5
**Purpose:** Build the WC-specific features on top of the core engine.
**Topics to cover:**
- Injury/illness coding (nature, cause, body part)
- Jurisdiction-specific indemnity benefit calculations (TTD, TPD, PPD, PTD)
- Return-to-work program management
- Medical case management and utilization review
- Nurse case manager assignment
- OSHA 300/301 log generation
- State EDI reporting (FROI/SROI)
- Vocational rehabilitation tracking

### Chat 19: Litigation Management Module
**Model:** Sonnet 4.5
**Purpose:** Build the legal case management layer.
**Topics to cover:**
- Attorney/law firm directory and performance tracking
- Litigation hold and legal expense tracking
- Court date and deposition calendar
- Settlement negotiation and authority workflows
- Verdict and judgment recording
- Legal bill review and e-billing integration
- Litigation outcome analytics

---

## Phase 9 — Compliance & Administration

### Chat 20: Regulatory Compliance Engine
**Model:** Opus 4
**Purpose:** Make sure the system keeps clients out of trouble.
**Topics to cover:**
- Jurisdiction rules engine (state-mandated timelines, notice requirements)
- Compliance calendar with automated deadline tracking
- Regulatory report generation (DOI filings, NAIC data calls, Medicare Section 111)
- Data retention and purge policy enforcement
- Multi-state and multi-jurisdiction configuration
- Audit readiness and compliance dashboards

### Chat 21: Tenant Administration & Configuration
**Model:** Sonnet 4.5
**Purpose:** Build the admin layer for onboarding and managing clients.
**Topics to cover:**
- Client/tenant onboarding workflow
- White-label branding configuration
- Configurable fields, statuses, and workflows per tenant
- User provisioning and role management
- Feature flags and entitlement management
- Data migration tools for legacy system onboarding
- Sandbox/training environment provisioning
- System health monitoring and uptime dashboards

---

## Phase 10 — Testing, Launch & Operations

### Chat 22: Testing Strategy & QA
**Model:** Sonnet 4.5
**Purpose:** Plan how to test a system this complex.
**Topics to cover:**
- Unit, integration, and end-to-end testing strategy
- Test data generation for multi-LOB, multi-jurisdiction scenarios
- Regression testing automation
- Performance and load testing plan
- Security testing (penetration testing, OWASP Top 10)
- UAT process and client sign-off workflows
- Compliance validation testing

### Chat 23: DevOps, Monitoring & Incident Response
**Model:** Sonnet 4.5
**Purpose:** Keep the system running reliably in production.
**Topics to cover:**
- CI/CD pipeline design
- Infrastructure as code (Terraform, Pulumi)
- Container orchestration (Kubernetes, ECS)
- Logging, monitoring, and alerting (Datadog, CloudWatch, Grafana)
- APM and distributed tracing
- Incident response playbooks
- Disaster recovery and business continuity (RPO/RTO)
- Blue/green and canary deployment strategies
- Cost optimization and resource scaling

---

## Suggested Order of Execution

| Priority | Chats | Rationale |
|---|---|---|
| **Start here** | 1, 2, 3 | Architecture and data model lock in decisions everything else depends on |
| **Build core** | 4, 5, 6, 7, 8 | The claim engine, documents, and communication are the MVP |
| **Add intelligence** | 9, 10, 11 | AI features differentiate you from legacy systems |
| **Open up** | 12, 13 | API platform enables integrations and partner ecosystem |
| **Build UIs** | 14, 15, 16 | User-facing layers built on top of stable core |
| **Report on it** | 17 | Analytics once there's data flowing |
| **Extend** | 18, 19, 20, 21 | Specialty modules and admin as market demands |
| **Ship it** | 22, 23 | Testing and operations for production readiness |

---

## Tips for Getting the Best Results

1. **Start each chat by uploading this feature list** so Claude has full context on the overall system.
2. **Reference decisions from earlier chats** — paste in key architecture decisions, schema designs, or tech stack choices so later chats stay consistent.
3. **Use Opus for "why" questions** (architecture tradeoffs, business logic design) and **Sonnet for "how" questions** (code generation, implementation).
4. **Keep each chat focused** — if a conversation starts drifting into a different domain, spin up the appropriate chat instead.
5. **Use projects** — if you have access to Claude Projects, create a project for the CMS and add your architecture docs, schemas, and feature list as project knowledge so every chat starts with shared context.

