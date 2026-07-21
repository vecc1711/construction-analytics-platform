# SA-003 – Logical Data Model and Subject Areas

**Project:** Construction Analytics Solution  
**Sprint:** Sprint 3 – Solution Architecture  
**Session:** SA-003  
**Status:** Complete  
**Started:** July 21, 2026  
**Completed:** July 21, 2026  

---

## Objective

Organize the business entities and relationships approved during SA-001 and SA-002 into logical subject areas and define data ownership, authoritative records, transaction boundaries, source-system flows, operational activity grain, WIP history, and shared configuration rules.

This session does not define physical table names, data types, indexes, or final database implementation details.

---

## Approved Logical Subject Areas

1. Customer and Sales
2. Estimating and Bid Budget
3. Job and Scope
4. Budget and Cost Structure
5. Workforce and Responsibility
6. Field Activity
7. Equipment
8. Materials and Inventory
9. Purchasing and Commitments
10. Posted Job Cost
11. Billing and Revenue
12. WIP and Forecasting
13. Reference and Shared Master Data

---

## 1. Customer and Sales

### Owned Entities

- Customer
- Opportunity
- Quote
- Job Quote Source
- Opportunity Site Information

### Key Decisions

- Customer, Opportunity, and Quote remain in the Customer and Sales subject area.
- Quote is the authoritative customer-facing proposal.
- Site information is stored on Opportunity before award.
- Accepted quote lineage is retained through Job Quote Source.
- Job becomes authoritative for the awarded work after creation.

---

## 2. Estimating and Bid Budget

### Owned Entities

- Bid Budget Revision
- Bid Budget Detail
- Estimate Production Assumption
- Estimate Crew Template
- Estimate Material Assumption
- Estimate Equipment Assumption

### Key Decisions

- Bid Budget Revision remains separate from Quote.
- The final bid-budget revision is the authoritative internal estimate supporting the submitted quote.
- Original quote budgets remain unchanged.
- When multiple accepted quotes make up one job, their bid budgets are not combined.
- A new final job budget is created for the awarded job.

---

## 3. Job and Scope

### Owned Entities

- Job
- Job Component
- Reopen Event

### Shared Reference Entities

- Job Status
- Job Component Type
- Job Component Status

### Key Decisions

- Job is authoritative for the awarded work.
- Job Component remains separate from Component Budget.
- Current Project Manager, Superintendent, Sales Representative, Estimator, and Coordinator are stored directly on Job.
- Job role assignment history is not required.
- Parent-job status controls posting eligibility.
- Component status controls Base Contract, Change Order, and Warranty lifecycle.

---

## 4. Budget and Cost Structure

### Owned Entities

- Component Budget
- Budget Detail
- Budget Adjustment
- Job Cost Code
- Job Cost-Code Phase

### Shared Reference Entities

- Cost Code
- Phase
- Cost Type
- Unit of Measure

### Key Decisions

- A new Base Contract component budget becomes the authoritative original job budget.
- Original quote budgets remain historical estimating records.
- Approved Change Order budgets affect revised budget and revenue.
- Warranty budgets affect the cost side of WIP and add no revenue.
- Budget adjustments do not change revenue.
- Job Cost Code and Job Cost-Code Phase define valid posting combinations.

---

## 5. Workforce and Responsibility

### Owned Entities

- Employee
- Crew
- Foreman Responsibility
- Employee Labor Rate History
- Employee Crew History

### Key Decisions

- Responsible Foreman is assigned by Job plus Cost Code.
- Responsible Foreman supports incentive, profitability, fade/gain, and long-term performance analysis.
- Daily Foreman identifies actual task supervision.
- Employees may work under multiple foremen in one day.
- Employee labor rates and primary crew assignments retain effective-dated history.
- Effective-date ranges cannot overlap.

---

## 6. Field Activity

### Owned or Derived Entities

- Employee Time Entry
- Derived Daily Work Group
- Daily Work Assignment
- Daily Production
- Daily Job Status
- Operational Delay
- Field Note
- Equipment Usage

### Derived Daily Work Group

Daily Work Groups are derived from imported labor records using:

```text
Job
+ Work Date
+ Foreman
```

Cost Code is not part of the work-group identifier because one foreman and work group may work under multiple cost codes during the day.

### Daily Work Assignment

A Daily Work Assignment represents task supervision within the work group and may include:

- Job
- Work Date
- Daily Foreman
- Cost Code
- Phase
- Employee labor segment
- Start and end time when available

### Daily Production

Daily Production attaches to the Daily Foreman and Cost Code.

Recommended grain:

```text
Work Date
+ Job
+ Daily Foreman
+ Cost Code
+ Production Measure
+ Source Record or Sequence
```

Multiple production measures are permitted for the same foreman, job, cost code, and date.

### Operational Delay

Operational Delay stores entered delay hours rather than required start and end times.

A delay may affect:

- Entire work group
- Daily work assignment
- Cost code
- Equipment unit
- Employee

The delay scope must be identified to prevent overstating total delay impact.

### Authority

Operational records remain authoritative for hours, quantities, assignments, and production. Posted Job Cost becomes authoritative for final financial cost.

---

## 7. Equipment

### Owned Entities

- Equipment Unit
- Equipment Rate History
- Equipment Type
- Equipment Status

### Field Activity Ownership

- Equipment Usage

### Key Decisions

- Equipment master data and effective-dated rates remain in Equipment.
- Equipment Usage belongs to Field Activity.
- Effective-dated equipment rates cannot overlap.
- Applied transaction cost is not recalculated when master rates change.

---

## 8. Materials and Inventory

### Owned Entities

- Material Item
- Material Transaction
- Inventory Transaction
- Inventory Location
- Material Standard Cost History

### Key Decisions

- Material Transaction and Supplier Ticket Line are independently imported.
- Material Transaction is authoritative for job-level quantity and applied cost.
- Material standard costs retain non-overlapping effective-dated history.
- Historical applied unit cost remains on the transaction.

---

## 9. Purchasing and Commitments

### Owned Entities

- Vendor
- Purchase Order
- Purchase Order Line
- Supplier Ticket
- Supplier Ticket Line
- Supplier Invoice
- Supplier Invoice Line

### Key Decisions

- Purchase Order Line is authoritative for commitment.
- Supplier Ticket Line is authoritative for delivered quantity.
- Supplier Invoice Line is authoritative for invoiced quantity and cost.
- Supplier Ticket Line and Material Transaction remain independent and are reconciled rather than automatically created from one another.
- Open commitment equals committed amount less invoiced amount.

---

## 10. Posted Job Cost

### Owned Entities

- Posted Job Cost
- Operational Cost Match

### Shared Reference Entities

- Job-Cost Source Type
- Job-Cost Correction Reason
- Matching Status

### Key Decisions

- Posted Job Cost is authoritative for final financial cost.
- Direct source-record matching is preferred.
- Secondary rule-based matching may use Job, date, Cost Code, Phase, Cost Type, source type, quantity, and related master records.
- Matching statuses include Direct Match, Rule-Based Match, Ambiguous Match, Unmatched, and Not Applicable.
- Estimated operational cost is removed only after a sufficiently reliable final-cost match.
- Imported operational corrections update the existing record while retaining audit information.
- Posted financial reversals and repostings remain preserved.

---

## 11. Billing and Revenue

### Owned Entities

- Customer Invoice
- Customer Invoice Line
- Revenue Adjustment

### Key Decisions

- Billing remains separate from WIP and Forecasting.
- Customer Invoice is authoritative for billed revenue.
- Billing analysis is performed at the parent-job level.
- Revised revenue equals Base Contract revenue plus approved Change Order revenue.
- Warranty adds no revenue.

---

## 12. WIP and Forecasting

### Owned Entities and Outputs

- Current Forecast
- Forecast Detail
- Current WIP View
- WIP Snapshot
- Job Profitability View

### Key Decisions

- Current WIP is dynamically calculated.
- Weekly and month-end WIP snapshots are standard.
- Weekly snapshots use a globally configured fixed week-ending day.
- The selected week-ending day applies to the entire company and should not change after implementation.
- Month-end snapshots use the accounting month-end date.
- Prior snapshots are retained and not overwritten.
- WIP snapshots preserve calculated values and the forecast inputs used at the reporting cutoff.
- Period-over-period deltas are calculated from consecutive snapshots.
- One current editable forecast is retained; forecast values are preserved historically through WIP snapshots.

Recommended WIP Snapshot grain:

```text
Job
+ Reporting Date
```

A snapshot may be identified as weekly, month-end, or both.

---

## 13. Reference and Shared Master Data

### Shared Entities

- Division
- Cost Code
- Phase
- Cost Type
- Unit of Measure
- Labor Code
- Status values
- Equipment Type
- Material Source Type
- Department
- Position
- State
- Matching Status
- Snapshot Type
- Global Configuration

### Global Configuration

The Global Configuration entity should include:

- WIP week-ending day
- Fiscal year-end
- Standard workweek
- Weekly WIP snapshot requirement
- Month-end WIP snapshot requirement
- Material-variance tolerance
- Cost-variance thresholds
- Closed-job posting rules
- Default Production and Travel phases
- Default Warranty phases
- Reporting currency
- Default reporting time zone
- WIP and forecast calculation options

Settings that affect historical comparability should be locked after implementation or changed only through a formal conversion process.

---

## Source-System Boundary

Operational and financial records are imported from external source systems.

```text
External Source Systems
        ↓
Staging and Validation
        ↓
Normalized Analytics Database
        ↓
SQL Reporting and Analytics Views
        ↓
Power BI and Tableau
```

Imported records retain:

- Source system
- Source record identifier
- Source created and modified dates
- First and last imported dates
- Import batch
- Record version
- Matching and reconciliation status where applicable

---

## Authoritative Record Summary

| Business Information | Authoritative Record |
|---|---|
| Pre-award customer-facing proposal | Quote |
| Internal pre-award estimate | Final Bid Budget Revision |
| Awarded work | Job |
| Original operating budget | Base Contract Component Budget |
| Current job roles | Job |
| Responsible foreman accountability | Foreman Responsibility |
| Labor hours and assignments | Employee Time Entry |
| Equipment usage hours | Equipment Usage |
| Material job quantity and applied cost | Material Transaction |
| Delivered supplier quantity | Supplier Ticket Line |
| Invoiced supplier quantity and cost | Supplier Invoice Line |
| Final financial cost | Posted Job Cost |
| Billed revenue | Customer Invoice |
| Current remaining-cost estimate | Current Forecast |
| Current calculated WIP | Current WIP View |
| Historical WIP | WIP Snapshot |

---

## Final Decisions

- Subject-area boundaries are approved.
- Quote and Bid Budget remain separate.
- Job Component and Component Budget remain separate.
- Equipment Usage belongs to Field Activity.
- Purchasing and material activity remain separate and reconciled.
- Billing and WIP remain separate.
- Job is authoritative after award.
- A new final job budget is created rather than combining quote budgets.
- Operational activity and posted financial cost have separate authority.
- WIP is dynamically calculated and stored through weekly and monthly snapshots.
- Daily work groups are derived from labor by Job, Work Date, and Foreman.
- Daily Production attaches to Daily Foreman and Cost Code.
- Operational Delay stores entered delay hours and flexible scope links.
- Imported operational corrections update existing records with audit history.
- Effective-dated rates, costs, and crew assignments cannot overlap.
- Global Configuration controls company-wide reporting and processing settings.

---

## Next Workshop

**SA-004 – Logical Entity and Attribute Definition**