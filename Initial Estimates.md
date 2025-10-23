Project Timeline & Cost Estimate
Total Project Duration: 9–12 weeks (2.25–3 months)
Phase-by-Phase Timeline & Effort
Phase	Duration	Labor Hours	Key Activities
Phase 1: Discovery & Planning	1–2 weeks	40–60 hours	Finalize KPIs, confirm API access, define roles/permissions, document requirements
Phase 2: Data Modeling & Integration	2–3 weeks	80–120 hours	Design schema, build API connectors (Greenhouse, Paycor, Deltek), data validation scripts
Phase 3: Dashboard Development	3–4 weeks	120–160 hours	Build Power BI visuals/pages, DAX measures, configure RBAC, automate refreshes
Phase 4: Testing & Validation	1–2 weeks	40–60 hours	UAT with stakeholders, KPI validation, feedback incorporation, bug fixes
Phase 5: Rollout & Training	1 week	20–30 hours	Production deployment, user training sessions, documentation finalization
Phase 6: Maintenance & Optimization	Ongoing	10–20 hrs/month	Monitor performance, add features, stakeholder reviews
Total Implementation Effort: 300–430 hours (9–12 weeks)

Cost Breakdown
One-Time Implementation Costs
Cost Category	Rate/Unit	Quantity	Estimated Cost
Developer/BI Analyst (mid-senior level)	$100–150/hr	300–430 hrs	$30,000–$64,500
Project Owner/Oversight (Kaitlyn Hoffecker)	$75–100/hr	50–75 hrs	$3,750–$7,500
Azure Data Factory (initial setup, if used)	$0.50–$2/pipeline run	Setup/testing	$500–$1,000
Power BI Premium/Pro licenses (initial)	$20–$49/user/month	5–15 users	First month included below
API Integration Testing (Greenhouse, Paycor, Deltek)	Vendor fees (if any)	One-time	$0–$2,000
Training Materials & Documentation	Included in labor	—	Included
Total One-Time Cost: $34,250–$75,000
Midpoint Estimate: ~$54,625

Ongoing Monthly/Annual Costs
Cost Category	Unit Cost	Quantity	Monthly	Annual
Power BI Pro licenses	$10/user/month	10–15 users	$100–$150	$1,200–$1,800
Power BI Premium (if needed for large data/RBAC)	$4,995/capacity/month	0–1	$0–$4,995	$0–$59,940
Azure Data Factory (recurring ETL runs)	$1–$5/day	Daily refreshes	$30–$150	$360–$1,800
Azure Storage (data lake/staging)	$0.02/GB/month	50–200 GB	$1–$4	$12–$48
Greenhouse API (if metered)	Usually included	—	$0	$0
Paycor/Deltek API access	Usually included	—	$0	$0
Maintenance & Support (10–20 hrs/month)	$100–$150/hr	10–20 hrs/month	$1,000–$3,000	$12,000–$36,000
Total Ongoing Costs:

Minimal scenario (Pro licenses + ADF + minimal support): ~$1,130–$3,300/month = $13,572–$39,648/year
Premium scenario (Premium capacity + full support): ~$6,025–$8,145/month = $72,300–$97,740/year
Midpoint Estimate (Pro + moderate support): ~$26,610/year

Cost Summary
Category	Low	Mid	High
One-Time Implementation	$34,250	$54,625	$75,000
Year 1 Ongoing Costs	$13,572	$26,610	$97,740
Year 2+ Ongoing Costs	$13,572	$26,610	$97,740
Total Year 1 Cost: $47,822–$172,740
Midpoint Year 1: ~$81,235

Key Assumptions & Notes
Labor rates assume U.S.-based mid-senior developer/BI analyst ($100–$150/hr). Adjust for offshore/junior resources or internal staff.
Power BI Premium is optional; needed only if:
User base exceeds ~50 Pro licenses (becomes cost-effective).
Advanced RBAC or large dataset capacity (>10GB) required.
Otherwise, Power BI Pro per-user licensing suffices.
Azure Data Factory cost depends on refresh frequency. Daily refreshes = ~$30–$150/month; hourly = significantly higher.
API costs assume Greenhouse Harvest API, Paycor, and Deltek CRM are accessible without metered charges (common for existing customers).
Maintenance (10–20 hrs/month) covers monitoring, minor updates, new KPI requests, and quarterly reviews. Reduce if internal team handles.
GitHub/GitHub Actions are free for private repos with basic CI/CD usage.
Cost Optimization Opportunities
Use Power BI Gateway instead of Azure Data Factory if on-prem data sources suffice (eliminates ADF costs).
Leverage Power Query (built into Power BI) for transformations instead of separate Python ETL (reduces maintenance hours).
Start with Power BI Pro licenses; upgrade to Premium only if RBAC or capacity demands require it.
Negotiate annual Azure commitment for 10–15% savings on Data Factory/Storage.
Use internal IT/BI team for maintenance to reduce hourly consulting costs.
Timeline Gantt (High-Level)
Start Date: TBD (assume Nov 2025)
Production Launch: Feb 2026 (if started Nov 2025)

