# DS-001 – Executive Discovery

**Document ID:** DS-001  
**Project:** Construction Analytics Solution  
**Sprint:** Sprint 2 – Business Discovery  
**Status:** Complete  
**Version:** 0.1  
**Last Updated:** July 2026

---

# 1. Purpose

This document summarizes the business discoveries identified during the Executive Discovery Workshop. The purpose is to capture executive-level business needs, strategic questions, candidate KPIs, and architectural implications that will guide the design of the Construction Analytics Solution.

---

# 2. Executive Summary

Executive leadership requires a concise, high-level view of company performance that supports timely decision-making. Rather than reviewing detailed accounting reports, executives need immediate visibility into company health, project performance, operational trends, profitability, and emerging risks.

The Executive Dashboard should answer the question:

> **"How are we doing today?"**

while allowing users to drill into supporting detail only when necessary.

---

# 3. Business Discoveries

| ID | Discovery |
|----|-----------|
| BD-001 | Executives require a company-wide view before reviewing individual projects. |
| BD-002 | Project health is more valuable than reviewing historical accounting reports. |
| BD-003 | Operational information should be available alongside financial information. |
| BD-004 | Trends are more valuable than isolated daily numbers. |
| BD-005 | Dashboards should identify projects requiring attention rather than simply reporting historical results. |

---

# 4. Business Questions Identified

The Executive Dashboard should answer questions including:

- How are we doing today?
- Are we meeting our profitability goals?
- Which projects require immediate attention?
- Which divisions are performing best?
- How are current results trending compared to previous periods?
- Are there operational issues impacting company performance?

---

# 5. Candidate KPIs

| KPI | Purpose |
|-----|---------|
| Gross Margin % | Measure overall profitability |
| Revenue | Measure company production |
| Project Health | Identify projects requiring attention |
| Division Performance | Compare operating divisions |
| Jobs at Risk | Highlight projects requiring intervention |
| Active Projects | Measure workload |

---

# 6. Candidate Data Requirements

Potential business entities include:

- Jobs
- Divisions
- Customers
- Project Managers
- Financial Performance
- Daily Operations
- Production
- Budgets

---

# 7. Candidate Functional Requirements

Potential functional requirements include:

- Display company-wide performance.
- Support drill-down from company to project level.
- Display current operational and financial performance.
- Identify projects requiring management attention.
- Present trend analysis over multiple time periods.

---

# 8. Candidate Architecture Decisions

| Decision | Status |
|----------|--------|
| Executive dashboards should summarize company performance before allowing drill-down. | Proposed |
| Dashboard design should emphasize business decisions rather than accounting reports. | Proposed |
| Executive dashboards should combine operational and financial information. | Proposed |

---

# 9. Impact on Solution Design

The Executive Discovery Workshop establishes that the highest level of the analytics solution should focus on business performance rather than transactional reporting.

The Executive Dashboard will serve as the entry point into the Construction Analytics Solution and provide visibility into company performance while supporting drill-down into divisions, projects, and operational metrics.

---

# 10. Traceability

This discovery contributes to:

- Executive Dashboard
- Project Health KPI
- Division Analytics
- Company Performance Analytics
- Conceptual Data Model
- Analytics Layer