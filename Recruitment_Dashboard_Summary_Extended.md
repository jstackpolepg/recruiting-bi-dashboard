# Recruitment Dashboard Summary

## Executive Summary

This project aims to replace Excel-based recruitment tracking with a
centralized Power BI dashboard that integrates data from
**Greenhouse.io**, **Azure Entra**, **Paycor**, and **Deltek CRM**.\
The dashboard will provide real-time visibility into current and future
staffing needs, support proactive planning, and enable data-driven
decision-making across departments.\
Key stakeholders include department heads, business development, and
recruitment teams.\
Role-based access will ensure tailored visibility and control for each
user group.

------------------------------------------------------------------------

## Scope of Work

### 1. Objectives

-   Centralize recruitment data from multiple systems\
-   Replace manual Excel tracking with automated, refreshable
    dashboards\
-   Enable forecasting, headcount planning, and KPI tracking\
-   Support role-based access and permissions

------------------------------------------------------------------------

### 2. Key Features

#### Power BI Dashboard with:

-   **KPIs:** contingent offer conversions, time to hire, offer
    declinations, candidate satisfaction, quality of hire\
-   **Recruiter activity metrics**\
-   **Time-to-fill and time-to-hire**\
-   **Projected staffing needs** from Deltek CRM\
-   **Requisition-level tracking**

#### Role-Based Access:

-   **Department Heads:** view-only, email updates\
-   **Recruitment:** full access\
-   **Talent Teams:** page/report-specific access

------------------------------------------------------------------------

### 3. Data Sources & Integrations

-   **Greenhouse.io:** via Harvest API or BI Connector\
-   **Azure Entra:** for identity and access management\
-   **Paycor:** for employee and payroll data\
-   **Deltek CRM:** for project pipeline and staffing forecasts

------------------------------------------------------------------------

### 4. KPIs to Include

-   Contingent offers converted to full-time\
-   Time from contingent conversion to hire\
-   Offer declinations and reasons\
-   Candidate satisfaction survey results\
-   Quality of hire (ramp-up time, retention at 30/60/90/180/365 days,
    hiring manager satisfaction)\
-   Recruiter activity metrics\
-   Time-to-fill and time-to-hire\
-   Forecasted staffing needs

------------------------------------------------------------------------

### 5. Data Model & Schema

  -----------------------------------------------------------------------
  Table                       Key Fields
  --------------------------- -------------------------------------------
  **Candidate**               ID, name, source, status, dates

  **Requisition**             ID, title, department, location, salary,
                              open/close dates

  **Activity**                recruiter actions, timestamps

  **Survey**                  candidate and manager satisfaction scores

  **Project**                 project ID, staffing needs, timeline,
                              status
  -----------------------------------------------------------------------

------------------------------------------------------------------------

### 6. Data Pipeline & Automation

-   Use **VS Code**, **GitHub**, and **GitHub Copilot** for development\
-   Automate data pulls via APIs\
-   Schedule refreshes using **Power BI Gateway** or **Azure Data
    Factory**\
-   Transform data using **Power Query** or **Python scripts**

------------------------------------------------------------------------

### 7. Dashboard Layout Recommendations

-   **Executive Summary Page:** high-level KPIs\
-   **Department View:** headcount, open roles, pipeline\
-   **Recruiter View:** activity metrics, time-to-fill\
-   **Forecasting View:** project staffing needs\
-   **Quality of Hire View:** retention, ramp-up, satisfaction

------------------------------------------------------------------------

### 8. Security & Compliance

-   Role-based access control via **Azure Entra**\
-   Data encryption in transit and at rest\
-   Audit logs for data access and changes\
-   Compliance with **GDPR** and internal data policies

------------------------------------------------------------------------

### 9. Tools & Libraries for Integration

-   **Python:** requests, pandas, pyodbc\
-   **Power BI:** Power Query, DAX\
-   **GitHub Actions:** for CI/CD\
-   **Azure Data Factory:** for ETL

------------------------------------------------------------------------

### 10. Developer Deliverables

-   Data model schema documentation\
-   API integration scripts\
-   Power BI dashboard files (.pbix)\
-   Role-based access configuration\
-   Automated refresh setup\
-   User guide and training materials

------------------------------------------------------------------------

### 11. Project Plan

#### Phase 1: Discovery & Planning

-   Finalize KPI definitions and data sources\
-   Confirm access to APIs and systems\
-   Define user roles and permissions\
-   **Timeline:** 1--2 weeks

#### Phase 2: Data Modeling & Integration

-   Design data schema\
-   Build API connectors for Greenhouse, Paycor, Deltek CRM\
-   Validate data quality and completeness\
-   **Timeline:** 2--3 weeks

#### Phase 3: Dashboard Development

-   Build Power BI visuals and pages\
-   Configure role-based access\
-   Set up automated refreshes\
-   **Timeline:** 3--4 weeks

#### Phase 4: Testing & Validation

-   Conduct UAT with department heads and recruiters\
-   Validate KPI accuracy and usability\
-   Adjust based on feedback\
-   **Timeline:** 1--2 weeks

#### Phase 5: Rollout & Training

-   Deploy dashboard to production\
-   Train users on navigation and interpretation\
-   Provide documentation and support\
-   **Timeline:** 1 week

#### Phase 6: Maintenance & Optimization

-   Monitor performance and data refreshes\
-   Add new KPIs or views as needed\
-   Conduct periodic reviews with stakeholders\
-   **Ongoing**

------------------------------------------------------------------------

### 12. Resources & Roles

  -----------------------------------------------------------------------
  Role                Responsibility
  ------------------- ---------------------------------------------------
  **Project Owner:**  Kaitlyn Hoffecker

  **Developer:**      \[Assigned Developer Name\]

  **Data Analyst:**   \[Optional\]

  **Stakeholders:**   Department Heads, Business Development, Recruitment
  -----------------------------------------------------------------------

------------------------------------------------------------------------

### 13. Risks & Mitigations

  -----------------------------------------------------------------------
  Risk                   Mitigation
  ---------------------- ------------------------------------------------
  API limitations or     Mitigate with version control and fallback logic
  changes                

  Data quality issues    Mitigate with validation scripts and alerts

  User adoption          Mitigate with training and feedback loops
  challenges             
  -----------------------------------------------------------------------

------------------------------------------------------------------------

### 14. Success Criteria

-   Dashboard adoption by all target departments\
-   Reduction in manual Excel tracking\
-   Accurate forecasting and KPI visibility\
-   Positive feedback from stakeholders

------------------------------------------------------------------------

*Let me know if you'd like to add resource estimates, Gantt charts, or a
communication plan.*
