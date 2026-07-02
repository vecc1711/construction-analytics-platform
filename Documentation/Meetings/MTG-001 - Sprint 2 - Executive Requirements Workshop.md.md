# MTG-001 - Sprint 2 - Executive Requirements Workshop

## Meeting Information

**Project:** Construction Analytics Solution  
**Date:** July 1, 2026  
**Sprint:** Sprint 2 - Requirements & Database Design  
**Topic:** Executive Dashboard Requirements  
**Attendees:**
- Mark Vecchione
- ChatGPT, Solution Architect

---

## Objectives

- Define the executive-level business questions the solution should answer.
- Identify the first group of KPIs needed for the Executive Dashboard.
- Determine what data is required to support those KPIs.
- Use these requirements to guide the database and analytics model design.

---

## Key Business Question

If an executive opened the dashboard each morning, what would they need to know first?

---

## Executive Dashboard Questions

1. Which jobs are currently profitable?
2. Which jobs are losing money?
3. Which jobs are over budget?
4. Which jobs have the largest cost overruns?
5. What is total contract value across active jobs?
6. What is total actual cost to date?
7. What is gross profit by job?
8. What is gross margin percentage by job?
9. Which divisions are most profitable?
10. Which project managers are managing the most profitable work?
11. Which customers generate the most revenue?
12. How much approved change order value has been added?
13. How much pending change order value is outstanding?
14. Which jobs are at risk?
15. How is job profitability trending over time?

---

## Initial Executive KPIs

| KPI | Purpose |
|-----|---------|
| Current Contract Value | Measures total active contract value after approved change orders |
| Original Contract Amount | Shows starting contract value |
| Approved Change Order Amount | Shows approved contract growth |
| Pending Change Order Amount | Shows potential future contract growth |
| Budget Cost | Total planned cost |
| Actual Cost | Total cost incurred to date |
| Cost Variance | Budget cost minus actual cost |
| Gross Profit | Current contract value minus actual cost |
| Gross Margin % | Gross profit divided by current contract value |
| Jobs at Risk | Jobs over budget, under margin target, or trending poorly |

---

## Initial Data Requirements

The Executive Dashboard will require data from:

- jobs
- customers
- employees
- divisions
- job_budget
- job_costs
- change_orders
- cost_types
- phases
- wbs_codes

---

## Decisions Made

| Decision ID | Description |
|-------------|-------------|
| TBD | Executive reporting will focus on profitability, budget performance, change orders, and jobs at risk. |

---

## Parking Lot

- Define exact Jobs at Risk logic
- Decide whether margin targets should be stored by job, division, or job type
- Determine if backlog should be included in Version 1.0
- Determine if completed jobs should remain in executive dashboard filters

---

## Action Items

| Owner | Task |
|-------|------|
| Mark | Save meeting notes in Documentation/Meetings |
| Mark | Review executive dashboard questions |
| ChatGPT | Help convert dashboard questions into KPI and data model requirements |

---

## Documentation Updated

- Meeting notes only

---

## Git

No commit yet.

---

## Next Meeting

MTG-002 - Project Manager Requirements Workshop