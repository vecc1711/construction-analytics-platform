# DS-003 – Operations Discovery

**Document ID:** DS-003  
**Project:** Construction Analytics Solution  
**Sprint:** Sprint 2 – Business Discovery  
**Status:** Draft
**Version:** 0.1  
**Last Updated:** July 2026

---

# 1. Purpose

This document summarizes the business discoveries identified during the Operations Discovery Workshop. The purpose is to capture how Operations manages daily field activities, allocates resources, monitors production, and supports project profitability through effective execution.

---

# 2. Executive Summary

Operations is responsible for maximizing productive field hours by effectively managing labor, equipment, materials, scheduling, and production.

Unlike Project Managers, who primarily focus on financial performance at the project level, Operations focuses on executing today's work safely, efficiently, and according to plan.

The workshop identified labor productivity and production rate as the primary operational drivers of profitability and confirmed that production must be measured using activity-specific units of measure.

The Daily Operations Dashboard should answer the question:

> **"How are we performing today?"**

---

# 3. Business Discoveries

| ID | Discovery |
|----|-----------|
| BD-201 | Operations manages resources rather than individual projects. |
| BD-202 | Daily scheduling drives operational decision-making. |
| BD-203 | Labor productivity is the largest operational driver of profitability. |
| BD-204 | Production is measured using different units depending on the work activity. |
| BD-205 | Expected production should be compared to actual production throughout the day. |
| BD-206 | Foremen receive complete project budgets to understand the overall project objectives. |
| BD-207 | Field information is available before accounting information is finalized. |
| BD-208 | Labor overruns create compounding impacts on burden and equipment costs. |

---

# 4. Business Questions Identified

The Daily Operations Dashboard should answer questions including:

- Are crews meeting today's production targets?
- Is labor tracking with production?
- Are projects progressing according to plan?
- Which crews require assistance?
- Which operational issues are impacting productivity?
- Are equipment and labor resources being utilized effectively?
- Are today's delays likely to affect tomorrow's schedule?

---

# 5. Candidate KPIs

| KPI | Purpose |
|-----|---------|
| Production Rate | Compare actual production to expected production |
| Labor Productivity | Measure labor efficiency |
| Equipment Utilization | Measure equipment efficiency |
| Material Usage | Compare actual usage to estimate |
| Crew Utilization | Measure workforce utilization |
| Shift Length | Monitor daily operating hours |
| Production Variance | Compare expected vs. actual production |

---

# 6. Candidate Data Requirements

Potential business entities include:

- Daily Production
- Production Standards
- Crews
- Equipment Usage
- Material Usage
- Weather
- Schedule
- Jobs
- Cost Codes
- Phases

---

# 7. Candidate Functional Requirements

Potential functional requirements include:

- Display daily production by project.
- Compare expected production to actual production.
- Measure labor productivity throughout the workday.
- Display resource utilization.
- Identify operational delays affecting production.
- Support multiple production units based on work activity.
- Display daily field activity prior to accounting cost posting.

---

# 8. Candidate Architecture Decisions

| Decision | Status |
|----------|--------|
| Production standards should be maintained separately from production transactions. | Proposed |
| Operational field activity should be available independently of accounting postings. | Approved |
| Production units should be associated with Cost Codes. | Proposed |
| Daily operational analytics should support same-day decision-making. | Approved |

---

# 9. Impact on Solution Design

The Operations Discovery Workshop establishes the Daily Operations Dashboard as a core component of the Construction Analytics Solution.

The workshop also reinforces the importance of separating operational data from accounting data, allowing management to evaluate project performance before payroll, equipment, and other accounting transactions have been finalized.

Operations analytics will focus on leading indicators such as production, labor productivity, and resource utilization rather than historical financial reporting.

---

# 10. Traceability

This discovery contributes to:

- Daily Operations Dashboard
- Project Performance Dashboard
- Production Analytics
- Resource Performance Analytics
- Conceptual Data Model
- KPI Dictionary
- Analytics Layer