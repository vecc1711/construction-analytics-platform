# DS-002 – Project Manager Discovery

**Document ID:** DS-002  
**Project:** Construction Analytics Solution  
**Sprint:** Sprint 2 – Business Discovery  
**Status:** Complete  
**Version:** 0.1  
**Last Updated:** July 2026

---

# 1. Purpose

This document summarizes the business discoveries identified during the Project Manager Discovery Workshop. The purpose is to capture how Project Managers monitor project performance, identify emerging issues, and make operational decisions before accounting information is fully available.

---

# 2. Executive Summary

Project Managers require timely operational and financial information to proactively manage project profitability. The workshop confirmed that Project Managers are primarily concerned with understanding whether a project is performing according to plan while work is still in progress.

Rather than relying solely on historical accounting information, Project Managers require leading indicators that identify potential issues before they negatively impact project profitability.

The Project Performance Dashboard should answer the question:

> **"How is my project performing today?"**

---

# 3. Business Discoveries

| ID | Discovery |
|----|-----------|
| BD-101 | Project Managers begin each day by reviewing the schedule before reviewing financial information. |
| BD-102 | Labor represents the largest source of project cost variance. |
| BD-103 | Cost Codes are the primary analytical structure used to evaluate project performance. |
| BD-104 | Daily operational information is more valuable than delayed accounting reports. |
| BD-105 | Project profitability is a shared responsibility across multiple business functions. |
| BD-106 | Projects belong to one primary division but may utilize resources from multiple divisions. |
| BD-107 | Projects range from one-hour service work to multi-month reconstruction projects. |

---

# 4. Business Questions Identified

The Project Performance Dashboard should answer questions including:

- How is the project performing today?
- Is labor tracking with project completion?
- Which cost codes are driving overruns?
- Are material quantities within budget?
- Are production rates meeting expectations?
- Is the project likely to finish within budget?
- Which projects require immediate attention?

---

# 5. Candidate KPIs

| KPI | Purpose |
|-----|---------|
| Gross Margin % | Measure project profitability |
| Budget Variance | Compare actual cost to budget |
| Labor Dollar Variance | Measure labor cost performance |
| Labor Hour Variance | Measure labor productivity |
| Material Quantity Variance | Measure material efficiency |
| Cost by Cost Type | Identify major cost drivers |
| Cost by Cost Code | Identify operational variances |
| Daily Production | Measure current field performance |

---

# 6. Candidate Data Requirements

Potential business entities include:

- Jobs
- Divisions
- Phases
- Cost Codes
- Cost Types
- Job Budgets
- Daily Labor
- Daily Equipment
- Daily Materials
- Production
- Change Orders

---

# 7. Candidate Functional Requirements

Potential functional requirements include:

- Display project performance in near real-time.
- Compare labor consumed against project completion.
- Display project costs by Cost Type.
- Display project costs by Cost Code.
- Display production performance.
- Identify projects at risk before completion.
- Support drill-down from project to transaction level.

---

# 8. Candidate Architecture Decisions

| Decision | Status |
|----------|--------|
| Business questions should drive the analytics model rather than existing ERP reports. | Approved |
| Operational field activity should be maintained separately from posted accounting information. | Approved |
| Project analytics should emphasize leading indicators rather than historical reporting. | Proposed |

---

# 9. Impact on Solution Design

The Project Manager Discovery Workshop establishes the Project Performance Dashboard as the primary operational dashboard within the Construction Analytics Solution.

The workshop also confirms that the solution should focus on proactive project management by combining operational field activity with financial information to identify issues before project completion.

---

# 10. Traceability

This discovery contributes to:

- Project Performance Dashboard
- Job Performance Analytics
- Daily Operations Analytics
- Conceptual Data Model
- KPI Dictionary
- Analytics Layer