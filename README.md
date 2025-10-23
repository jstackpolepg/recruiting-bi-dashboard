# Start Here: Recruiting BI Dashboard – Step 1

This guide helps you start the dashboard project the right way. Do not begin building Power BI data connections or visuals until the HR Data API Infrastructure is confirmed ready.

## 0) API Infrastructure Readiness Gate (Must Pass Before Starting)

Confirm the following with the API team or via the checks below:

- API base URL(s) issued for your environment(s):
  - dev: https://api-dev.hrdata.example.com/v1
  - test: https://api-test.hrdata.example.com/v1
  - prod: https://api.hrdata.example.com/v1
- Authentication configured (Azure Entra / OAuth 2.0) and you have:
  - Tenant ID, Client ID, and a method to obtain tokens (device code/app reg)
  - APIM subscription key (if required by policy)
- OpenAPI (Swagger) published and browsable in the developer portal
- Health endpoints return 200:
  - GET /healthz, GET /readyz
- At least one data endpoint returns data (e.g., candidates, requisitions) with your auth
- CORS or gateway policies allow Power BI and your network
- Rate limits documented; guidance on paging and filtering provided
- Postman (or similar) collection provided and works in dev

Quick verification (PowerShell examples; replace placeholders):

```powershell
# 1) Acquire an access token (example: device code flow via MSAL CLI or custom script)
# Placeholder: Obtain $accessToken and $subscriptionKey from your team/process

$baseUrl = "https://api-dev.hrdata.example.com/v1"
$headers = @{ 
  "Authorization" = "Bearer $accessToken" 
  "Ocp-Apim-Subscription-Key" = "$subscriptionKey" # if required
}

# 2) Health check
Invoke-RestMethod -Method GET -Uri "$baseUrl/healthz" -Headers $headers
Invoke-RestMethod -Method GET -Uri "$baseUrl/readyz" -Headers $headers

# 3) Sample data call
Invoke-RestMethod -Method GET -Uri "$baseUrl/greenhouse/candidates?limit=5" -Headers $headers
```

If any check fails, pause the dashboard work and coordinate with the API project (see `HR_Data_API_SOW.md`).

---

## 1) Set Up Your Local Environment

- Power BI Desktop (latest)
- VS Code with recommended extensions:
  - Python, Pylance (if using scripts)
  - Power Query / M language support
  - DAX language support
  - GitHub Pull Requests & Issues (optional)
- Access to the GitHub repo and the API developer portal

---

## 2) Establish Project Parameters (for Power BI / Power Query)

Create parameters in Power BI (Manage Parameters) so connections are portable:
- ApiBaseUrl (Text): e.g., https://api-dev.hrdata.example.com/v1
- UseSubscriptionKey (Logical): true/false
- SubscriptionKey (Text): blank or your APIM key
- AuthAuthority (Text): e.g., https://login.microsoftonline.com/<TenantId>
- AuthClientId (Text): your Azure AD app registration Client ID
- AuthScope (Text): e.g., api://<ApiAppId>/.default

These will drive your M queries and enable quick environment switching.

---

## 3) First Connections (after API is ready)

Start with read-only endpoints needed for the initial model:
- GET /greenhouse/candidates
- GET /greenhouse/requisitions
- GET /deltek/forecasts (for staffing needs)

Tips:
- Use pagination parameters (limit + cursor) and fold into incremental refresh later
- Filter by date ranges (updated_since, since) to reduce payloads
- Normalize timestamps to UTC; document any local time needs in visuals

---

## 4) Minimal Folder Structure (in this repo)

```
.
├─ powerquery/        # M scripts (optionally export from PBI for versioning)
├─ dax/               # Measures saved as .dax or .txt for review
├─ docs/              # Notes, screenshots, decisions
└─ reports/           # .pbit or .pbix (if you choose to version binaries)
```

Note: By default, `.gitignore` does not exclude .pbix—decide if you want to track them.

---

## 5) Initial Deliverable Scope (Sprint 1)

- Connect to the three endpoints above using parameters
- Create a basic star schema (Candidate, Requisition, Project Forecast) in the model
- Define core measures: time-to-fill, time-to-hire (draft)
- Build a single “Executive Overview” page with sample KPIs
- Validate numbers with stakeholders

---

## 6) Guardrails and Gotchas

- Respect API rate limits—use parameters and filters to limit data size
- Use environment-specific base URLs; never hardcode prod in dev files
- Avoid storing secrets in Power BI; prefer APIM keys and OAuth flows
- Document assumptions about KPI definitions before creating measures

---

## 7) If the API Isn’t Ready Yet

Stop here. Track progress on the API repo and confirm the readiness gate items. Refer to `HR_Data_API_SOW.md` for what “ready” means (endpoints, auth, docs, monitoring). Only proceed once the dev environment passes the checks above.

---

## 8) When Ready—Next Steps

- Parameterize additional endpoints (offers, activities, employees)
- Add RLS definitions aligned with Azure Entra roles/groups
- Automate refresh (Gateway or ADF) after performance testing
- Expand pages: Department, Recruiter, Forecasting, Quality of Hire

---

Questions or changes? Open an issue in GitHub describing the API readiness status and any blockers.
