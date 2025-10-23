# Statement of Work (SOW)

## HR Data API Infrastructure for Recruitment Analytics

Version: 1.0  
Date: 2025-10-23  
Owner: Kaitlyn Hoffecker (Project Owner)  
Prepared for: Recruiting, Department Heads, Business Development

---

## 1. Purpose and Background

This SOW defines the scope, architecture, requirements, and delivery plan for a reusable HR Data API Infrastructure that aggregates and normalizes data from Greenhouse.io, Paycor, Deltek CRM, and Azure Entra. The API will serve as a reliable, secure, and versioned data layer for the Recruitment Power BI Dashboard and future analytics and applications (e.g., workforce planning, budgeting, custom apps).

The goals are to centralize integration logic, enforce security and governance, improve reliability, and enable rapid reuse across projects.

---

## 2. Objectives

- Provide a secure, scalable REST API layer exposing HR and recruiting entities.  
- Normalize disparate vendor schemas into consistent, documented contracts.  
- Implement authentication/authorization via Azure Entra (Azure AD).  
- Deliver observability (logging, metrics, tracing), caching, and rate limiting.  
- Publish comprehensive OpenAPI documentation and client examples.  
- Decouple data access from visualization tools (e.g., Power BI) for reuse.

---

## 3. In-Scope

- Design and build REST endpoints for:
  - Greenhouse: candidates, requisitions, applications, offers, activities
  - Paycor: employees, employment status, terminations
  - Deltek CRM: projects, pipeline, staffing forecasts
  - Azure Entra: users, groups (for RBAC mapping)
- Data normalization and minimal transformations (consistent field names, data types, enums, timestamps in UTC ISO 8601)
- Authentication and authorization (OAuth 2.0 / OIDC with Azure Entra)  
- Caching (Azure Cache for Redis) and vendor considerate throttling  
- Rate limiting per client/app and global safe-guards  
- Versioning strategy (URL-based, e.g., /v1)  
- Error handling conventions and traceability  
- Observability (Application Insights) and health/readiness probes  
- Infrastructure as Code (Bicep or Terraform) and CI/CD (GitHub Actions)  
- Environments: dev, test, prod with isolated resources  
- Security hardening (Key Vault secrets, least privilege, audit)  
- Documentation (OpenAPI 3.0, runbooks, consumer guide, Postman collection)  
- Test automation (unit, contract, integration smoke tests)

### Out of Scope (initial version)
- Complex historical warehousing, SCD2, or analytics storage (handled by downstream systems)  
- Real-time event streaming (can be a future enhancement)  
- Write-back to source systems  
- Data science features (feature stores, ML models)

---

## 4. Users and Roles

- API Consumers: Power BI (Power Query), internal apps, analytics jobs  
- Admins/Operators: DevOps/IT for deployments, monitoring, and secrets  
- Security/Risk: Approvals and periodic reviews  
- Project Owner and Stakeholders: Requirements and acceptance

RBAC is enforced via Azure Entra app roles and groups. Endpoint access is limited by role (e.g., read:greenhouse, read:paycor, read:deltek, read:entra).

---

## 5. High-Level Architecture

- Azure Functions (Python) for serverless, autoscaling API compute  
- Azure API Management (APIM) as gateway for auth, policies, rate limits, and developer portal  
- Azure Cache for Redis for response/data caching  
- Azure Key Vault for secrets and certificates  
- Application Insights for logs, metrics, distributed tracing  
- Azure Storage (Blob/Table) for optional staging and durable artifacts  
- Azure Entra (AAD) for app registration and OAuth 2.0  
- GitHub Actions CI/CD with OIDC to Azure; IaC (Bicep or Terraform) for repeatable provisioning

```
Clients → APIM → Azure Functions (per-service modules) → Vendor APIs
                       ↘ Redis Cache ↙             ↘ Key Vault / App Insights
```

---

## 6. Proposed Endpoints (v1)

Base URL (per environment):  
- dev: https://api-dev.hrdata.example.com/v1  
- test: https://api-test.hrdata.example.com/v1  
- prod: https://api.hrdata.example.com/v1

Greenhouse
- GET /greenhouse/candidates?updated_since&status&limit&cursor
- GET /greenhouse/requisitions?department&location&status
- GET /greenhouse/applications?candidate_id
- GET /greenhouse/offers?status
- GET /greenhouse/activities?type&since

Paycor
- GET /paycor/employees?status&updated_since
- GET /paycor/terminations?since
- GET /paycor/positions?department&location

Deltek CRM
- GET /deltek/projects?status&since
- GET /deltek/pipeline?stage&since
- GET /deltek/forecasts?project_id&through

Azure Entra (directory metadata for RBAC mapping)
- GET /entra/users?group_id&enabled
- GET /entra/groups?display_name

Utilities
- GET /healthz (liveness)
- GET /readyz (readiness/dependency checks)
- GET /version

Notes
- All list endpoints support pagination via limit + cursor (opaque).  
- Timestamps are UTC ISO 8601.  
- Filtering parameters are optional but strongly recommended to control payload size and rate limits.

---

## 7. Data Contracts (normalized schemas)

Examples (abbreviated):

Candidate
```
{
  "id": "ghc_12345",
  "source": "Greenhouse",
  "firstName": "Jane",
  "lastName": "Doe",
  "email": "jane.doe@example.com",
  "status": "active|hired|rejected|on-hold",
  "appliedDate": "2025-08-01T12:34:56Z",
  "lastUpdated": "2025-08-15T09:21:00Z",
  "requisitionId": "req_9876",
  "stage": "Offer",
  "customFields": {"location": "Dallas", "department": "Ops"}
}
```

Requisition
```
{
  "id": "req_9876",
  "title": "Project Manager",
  "department": "Operations",
  "location": "Dallas, TX",
  "salaryRange": {"min": 90000, "max": 120000, "currency": "USD"},
  "openDate": "2025-07-15",
  "closeDate": null,
  "status": "open|closed|on-hold",
  "hiringManager": {"id": "user_123", "name": "A. Smith"}
}
```

Employee (Paycor)
```
{
  "id": "emp_4567",
  "firstName": "Sam",
  "lastName": "Lee",
  "email": "sam.lee@example.com",
  "status": "active|terminated|leave",
  "hireDate": "2024-11-03",
  "terminationDate": null,
  "department": "Operations",
  "location": "Dallas, TX",
  "positionTitle": "Project Coordinator"
}
```

Project Forecast (Deltek)
```
{
  "projectId": "prj_111",
  "projectName": "Airport Expansion",
  "forecastThrough": "2025-12-31",
  "staffingNeeds": [
    {"role": "Estimator", "count": 2, "start": "2025-11-01", "end": "2026-03-31"}
  ],
  "status": "active|proposed|closed"
}
```

Error (standard)
```
{
  "traceId": "00-2f7c...",
  "code": "rate_limit_exceeded|unauthorized|upstream_error|validation_error",
  "message": "Human readable message",
  "details": {"retryAfter": 30}
}
```

---

## 8. Security & Compliance

- OAuth 2.0 / OIDC with Azure Entra app registration (confidential client)  
- App roles and groups mapped to endpoint-level permissions  
- Secrets and certificates stored in Azure Key Vault; no secrets in code  
- Principle of Least Privilege for all identities and data access  
- PII handling: only necessary fields exposed; optional field masking  
- Encryption in transit (TLS 1.2+) and at rest (Azure-managed keys)  
- Audit logging of access and administrative actions  
- Compliance: GDPR, internal data policies; data minimization and retention controls

---

## 9. Non-Functional Requirements (NFRs)

- Availability SLO: ≥ 99.9% monthly for prod  
- Performance: p95 latency ≤ 500 ms for cached endpoints; ≤ 2 s for uncached vendor calls  
- Scalability: autoscale Functions based on queue/concurrency; APIM policies for throttling  
- Observability: structured logs with correlation IDs; request/dep metrics; dashboards  
- Rate Limits: default 60 requests/min per client; burst tokens + 429 with Retry-After  
- Caching: Redis TTL 15–60 minutes (endpoint-specific); cache-busting query param `bypass_cache=true` (role-restricted)  
- Pagination: cursor-based with opaque token; default page 100, max 1000  
- Timeouts: 30 s APIM; 60 s Function execution; vendor-specific retries with backoff  
- Data Consistency: eventual consistency acceptable; document known vendor lags  
- Localization: UTC timestamps; US English text fields

---

## 10. Error Handling & Conventions

- Structured errors (see schema) with consistent HTTP codes  
- Correlation: `x-correlation-id` accepted and propagated; generated if absent  
- Retries: Exponential backoff for idempotent GETs on transient faults (HTTP 429/5xx)  
- Vendor Errors: Map to standardized codes and include `upstream.status` and `upstream.body` (redacted)

---

## 11. Environments & Configuration

- Environments: dev, test, prod (separate subscriptions/resource groups)  
- Config via App Settings (Functions) and Key Vault references  
- Network: private endpoints and IP restrictions for prod where feasible  
- Naming: `rg-hrapi-<env>`, `fnc-hrapi-<env>`, `apim-hrapi-<env>`, `kv-hrapi-<env>`, `redis-hrapi-<env>`

---

## 12. CI/CD and Infrastructure as Code

- IaC: Bicep or Terraform to provision APIM, Functions, Redis, Key Vault, App Insights, Storage  
- Pipelines: GitHub Actions with OIDC to Azure; environment protection rules  
- Steps: lint → test → build → package → deploy infra (if changed) → deploy app → smoke tests → publish OpenAPI to APIM → tag release  
- Artifacts: container image or zip package; OpenAPI JSON; Postman collection

---

## 13. Data Mapping & Transformations

- Field normalization specs per source system  
- Enumerations and value mappings (e.g., status codes, departments)  
- Timestamp normalization to UTC  
- Optional enrichment via local lookup tables (e.g., department hierarchies)

Deliverable: Data Dictionary documenting all fields, types, and mappings.

---

## 14. Testing Strategy

- Unit tests: parsing, mapping, validation utilities  
- Contract tests: ensure responses match OpenAPI schemas  
- Integration tests: live or sandbox calls against vendor APIs (secure creds)  
- Performance tests: baseline latency and throughput with/without cache  
- Security tests: authZ, role scoping, secrets scans, dependency vulnerabilities  
- Smoke tests: post-deployment /healthz, sample endpoint, log verification

---

## 15. Deliverables

- Source code repository (`hr-data-api`) with:
  - Azure Functions (Python) code organized by service module  
  - Shared libs for auth, caching, error handling, pagination  
  - OpenAPI 3.0 spec and examples  
  - IaC (Bicep/Terraform) for all Azure resources  
  - GitHub Actions workflows for CI/CD  
  - Tests (unit/contract/integration) and fixtures  
  - Postman collection and environment files  
  - Runbooks: operations, incident response, on-call guide  
  - Consumer Guide: how to authenticate, query, and paginate from Power Query (Power BI) and Python

---

## 16. Milestones & Timeline (estimate 3–4 weeks)

- Week 1: Planning & Environment Setup  
  - Finalize endpoint list, contracts, and access to vendor APIs  
  - Provision dev/test infra via IaC; set up CI/CD scaffolding  
- Week 2–3: Build & Integrate  
  - Implement Greenhouse, Paycor, Deltek modules with caching, rate limits  
  - Implement authZ with app roles; logging/metrics; error handling  
  - Draft OpenAPI spec and examples; Postman collection  
- Week 4: Test, Harden, and Release  
  - Performance and security testing; UAT  
  - Publish to APIM, finalize documentation  
  - Promote to prod with change control

---

## 17. Acceptance Criteria

- All in-scope endpoints available under `/v1` with documented contracts  
- OAuth2 authN/authZ enforced; app roles and permissions validated  
- Rate limiting and caching active and observable  
- OpenAPI spec validated; Postman collection functional  
- Monitoring dashboards and alerts configured (App Insights)  
- Data accuracy validated on sampled records (≥ 99% field-level match to sources for mapped fields)  
- CI/CD deploys green across dev/test/prod with smoke tests  
- Runbooks and consumer documentation delivered  
- Security review passed; no critical vulnerabilities open

---

## 18. Risks & Mitigations

- Vendor API limits/changes → Implement retries, backoff, and version pinning; monitor change logs  
- Credential management failures → Use Key Vault; rotate secrets; least privilege service principals  
- Data quality discrepancies → Validation scripts; alerting and reconciliation processes  
- Scope creep → Change control with impact analysis and approval  
- Performance variability → Caching, batching, pagination, and circuit breakers

---

## 19. Assumptions

- Access to Greenhouse Harvest API, Paycor, Deltek CRM, and Azure Entra will be provisioned  
- Existing Azure subscription with rights to deploy APIM, Functions, Redis, Key Vault  
- Power BI and other consumers will use read-only API access  
- PII minimization and redaction policies approved by Security/Risk

---

## 20. Roles & RACI

- Project Owner (K. Hoffecker): Accountable for scope and acceptance  
- Developer/Engineer: Responsible for implementation and tests  
- DevOps/Cloud: Responsible for IaC and CI/CD  
- Security/Risk: Consulted on reviews; approves go-live  
- Stakeholders: Informed of milestones and releases

---

## 21. Change Control

All changes to scope, schedule, or costs require a change request with impact analysis (effort, risk, timeline), approval from the Project Owner, and update to this SOW and release plan.

---

## 22. Cost & Operations (reference)

- Azure Functions + APIM + Redis + App Insights: $200–$800/month (usage dependent)  
- Maintenance: 10–20 hrs/month for monitoring, upgrades, enhancements  
- One-time implementation (from prior estimate): +$10k–$15k incremental effort if split from dashboard

---

## 23. Implementation Instructions for AI (Actionable Prompt)

Follow these instructions to fully implement the project.

1) Repository Structure
```
hr-data-api/
  src/
    common/               # auth, logging, errors, caching, pagination
    greenhouse/           # functions and clients
    paycor/
    deltek/
    entra/
  openapi/
    hr-data-api.v1.yaml
  infra/
    bicep/ or terraform/
  tests/
    unit/
    contract/
    integration/
  .github/workflows/
  docs/
```

2) Tech Stack and Standards
- Language: Python 3.11  
- Framework: Azure Functions v4 (HTTP triggers), Async IO  
- Package Mgmt: pip + requirements.txt or uv/poetry  
- Lint/Format: Ruff + Black; Type hints (mypy optional)  
- Logging: OpenTelemetry-compatible, JSON structured logs  
- Config: pydantic-settings or env vars; Key Vault references  
- Testing: pytest + coverage; schemathesis for OpenAPI checks

3) Environment Variables (by reference to Key Vault where possible)
- GH_API_KEY, PAYCOR_CLIENT_ID, PAYCOR_CLIENT_SECRET, DELTEK_API_KEY  
- ENTRA_TENANT_ID, ENTRA_CLIENT_ID, ENTRA_CLIENT_SECRET  
- REDIS_CONNECTION_STRING, APPINSIGHTS_CONNECTION_STRING  
- APIM_SUBSCRIPTION_REQUIRED=true

4) Build Tasks
- Implement clients for each vendor with retry/backoff  
- Implement normalization mappers to the contracts above  
- Implement caching decorators with Redis TTLs per endpoint  
- Implement rate-limit headers and 429 flows through APIM policies  
- Implement /healthz, /readyz endpoints  
- Generate and publish OpenAPI 3.0 spec; include examples  
- Create Postman collection from OpenAPI

5) CI/CD
- GitHub Actions: lint → test → build → package → deploy IaC → deploy functions → run smoke tests  
- Use OIDC to Azure for secrets-less deployments  
- Publish OpenAPI to APIM on each release

6) Acceptance Tests (Automate)
- AuthN/AuthZ role checks  
- Pagination correctness and cursor continuity  
- Cache HIT rate target ≥ 70% on dashboard access pattern  
- p95 latency targets met  
- Data accuracy checks against sampled upstream records  
- Resilience under induced 429/5xx upstream responses

7) Documentation
- Update OpenAPI descriptions and examples  
- Provide Consumer Guide for Power Query and Python  
- Provide Runbook with common incidents and remediation

---

## 24. Definition of Done

- Code, tests, and IaC merged to main with passing CI  
- Dev/test/prod deployed via pipeline  
- OpenAPI published and browsable via APIM developer portal  
- Monitoring dashboards active with alerts  
- Consumer and operations docs delivered  
- Sign-off by Project Owner and Security

---

End of SOW.
