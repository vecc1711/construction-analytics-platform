# SA-002 – Entity Relationship Definition

**Project:** Construction Analytics Solution  
**Sprint:** Sprint 3 – Solution Architecture  
**Session:** SA-002  
**Status:** Complete  
**Started:** July 16, 2026  
**Completed:** July 21, 2026  

---

## Objective

Define the relationships, cardinality, optionality, and lineage among the business entities identified during SA-001.

This session focuses on conceptual business relationships rather than physical database tables, foreign keys, surrogate keys, or implementation-specific column design.

---

## Relationship Standards

Relationships are documented using the following notation:

- `1:1` — one-to-one
- `1:M` — one-to-many
- `M:M` — many-to-many
- `0..1` — zero or one
- `0..M` — zero or many
- **Required** — the child cannot exist without the parent
- **Optional** — the relationship may not exist for every record

Direct many-to-many relationships will not be implemented. Any required many-to-many relationship must be resolved through a bridge or associative entity.

---

## Scope

SA-002 defines relationships for:

- Customer and opportunity
- Opportunity and quote
- Quote and bid-budget revisions
- Accepted quotes and jobs
- Parent jobs and job components
- Base contract, change order, and warranty components
- Component budgets and budget adjustments
- Job cost codes, phases, and cost types
- Job responsibilities and foreman accountability
- Crews and employees
- Labor, burden, equipment, and material activity
- Purchase orders, supplier tickets, and commitments
- Posted job-cost transactions
- Reversals and corrected transactions
- Customer invoices
- WIP and earned revenue
- Forecasting
- Job closure, reopening, and component lifecycle status

---

# 1. Customer, Opportunity, Quote, and Job Relationships

## 1.1 Customer and Opportunity

Confirmed relationship:

```text
Customer 1:M Opportunity
```

Business rules:

- Each opportunity belongs to exactly one customer.
- A customer may have zero or many opportunities.
- An opportunity cannot be created until customer information has been established.
- A separate lead or prospect entity is not required for the initial solution.
- Customer is required for Opportunity.
- Opportunity is optional for Customer.

---

## 1.2 Property and Site Information

A separate Property entity will not be included in the initial solution.

Site information will be stored directly with the Opportunity and copied to the Job when the work is awarded.

```text
Customer
→ Opportunity with Site Information
→ Job with Site Information
```

Each opportunity represents work at one site.

Opportunity site information may include:

- Site name
- Street address
- City
- State
- Postal code
- Property type
- Site contact name
- Site contact email
- Site contact mobile number
- Access instructions
- Workday restrictions
- Work-hour restrictions
- Site notes

When the work is awarded:

- Final site information is copied to the Job.
- Job site information becomes the authoritative historical site record for that job.
- A job cannot span multiple sites.
- Site information does not require a reusable property master record.
- Repeat-site matching and centralized property history are outside the initial scope.
- A future Property entity may be introduced if repeat-site analytics or centralized site management becomes necessary.

---

## 1.3 Opportunity and Quote

Confirmed relationship:

```text
Opportunity 1:M Quote
```

Business rules:

- One opportunity may have one or many quotes.
- Each quote belongs to exactly one opportunity.
- A quote cannot exist without an opportunity.
- A quote cannot be shared across multiple opportunities.
- Opportunity is required for Quote.
- Quote is optional for Opportunity until pricing begins.

---

## 1.4 Quote and Bid-Budget Revision

A quote may have multiple bid-budget revisions.

Confirmed relationship:

```text
Quote 1:M Bid Budget Revision
```

Business rules:

- Each bid-budget revision belongs to exactly one quote.
- A quote may contain one or many bid-budget revisions.
- Prior revisions remain available for traceability.
- One revision is identified as the current revision.
- One revision is identified as the final revision.
- The final customer-facing quote must agree with the final bid-budget revision.
- A revised quote requires a corresponding final bid-budget revision.

Bid-budget revision attributes may include:

- Revision number
- Revision date
- Revision status
- Revision reason
- Created by
- Current revision indicator
- Final revision indicator

Example:

```text
Quote 5001
├── Bid Budget Revision 1
├── Bid Budget Revision 2
└── Bid Budget Revision 3 — Final
```

---

## 1.5 Accepted Quotes and Jobs

One job may originate from multiple accepted quotes.

Direct many-to-many relationships are not permitted. The relationship will be resolved through a bridge entity.

Confirmed relationships:

```text
Job 1:M Job Quote Source
Quote 1:0..1 Job Quote Source
```

Bridge entity:

```text
Job Quote Source
- Job
- Quote
- Final Bid-Budget Revision
- Accepted Date
- Accepted Amount
- Source Sequence
- Notes
```

Business rules:

- One job may have multiple Job Quote Source records.
- Each Job Quote Source record links one accepted quote to one job.
- One quote may contribute to no more than one job.
- Rejected or unaccepted quotes do not have a Job Quote Source record.
- The bridge preserves lineage from the awarded job to its accepted quote and final bid-budget revision.
- Direct unresolved `Job M:M Quote` relationships are not allowed.

Example:

```text
Job 12345
├── Quote 5001
├── Quote 5004
└── Quote 5010
```

---

# 2. Parent Job and Job Component Structure

## 2.1 Parent Job

The parent Job represents the overall customer contract and operational project.

The parent Job stores:

- Job number
- Customer
- Site information
- Division
- Project manager
- Superintendent
- Sales representative
- Estimator
- Coordinator
- Billing information
- Overall job status
- Award date
- Start date
- Completion date
- Close date
- Reopen and reclose activity

Each job belongs to exactly one division.

```text
Division 1:M Job
```

Business rules:

- The division is fixed for the life of the job.
- The division is responsible for the job from award through completion, warranty, and final closure.
- Change orders and warranty activity remain assigned to the parent job’s division.
- Division exceptions are not permitted.
- Division is not independently assigned to job components.

---

## 2.2 Job Components

Each job contains one or more Job Components.

Confirmed relationship:

```text
Job 1:M Job Component
```

Job Component types are:

```text
Base Contract
Change Order
Warranty
```

Each component has its own component type and lifecycle status.

Component type and component status are separate concepts.

Example:

```text
Job 12345
├── 12345-000 — Base Contract
├── 12345-001 — Change Order 1
├── 12345-002 — Change Order 2
├── 12345-W01 — Warranty Callback 1
└── 12345-W02 — Warranty Callback 2
```

---

## 2.3 Base Component

When a parent job is created, the system automatically creates:

```text
12345-000 — Base Contract
```

Business rules:

- Every job has exactly one Base Contract component.
- The Base Contract component uses suffix `000`.
- The Base Contract component may exist before its budget is complete.
- The Base Contract component inherits the parent job’s division and responsibility assignments.
- The Base Contract component represents the original contracted scope.

---

## 2.4 Change-Order Components

Change-order components use a sequential numeric suffix.

Example:

```text
12345-001
12345-002
12345-003
```

Business rules:

- A change-order component number is assigned when the change order is created.
- The number is assigned before approval.
- Rejected or cancelled change orders retain their assigned numbers.
- Component numbers are never reused.
- One job may have zero or many change-order components.
- Each change-order component belongs to one parent job.
- Each change-order component may have no more than one component budget.
- A change-order component may exist before its budget is prepared.

Change-order statuses may include:

```text
Draft
Submitted
Pending
Approved
Rejected
Cancelled
```

Only approved change orders are included in:

- Revised job revenue
- Revised job budget
- WIP calculations
- Earned revenue calculations

---

## 2.5 Warranty Components

Warranty callbacks use a separate `W` sequence.

Example:

```text
12345-W01
12345-W02
12345-W03
```

Business rules:

- One job may have zero or many warranty components.
- A separate warranty component is created for each callback or warranty event requiring separate tracking.
- Warranty components may occur in different periods or years.
- Warranty components retain their own lifecycle status.
- Warranty components have no revenue.
- Warranty components remain assigned to the parent job’s division.
- Warranty numbers are never reused.

Warranty statuses may include:

```text
Draft
Scheduled
In Progress
Complete
Closed
Cancelled
```

---

## 2.6 Component Number Structure

The displayed component number should be generated from separate values rather than used as the only internal identifier.

Example:

```text
Parent Job Number: 12345
Component Suffix: 001
Component Type: Change Order
Display Number: 12345-001
```

Warranty example:

```text
Parent Job Number: 12345
Component Suffix: W01
Component Type: Warranty
Display Number: 12345-W01
```

The solution must not rely on parsing component type solely from the display number.

---

# 3. Component Budgets and Budget Detail

## 3.1 Component Budget

Each Job Component may have one Component Budget.

Confirmed relationship:

```text
Job Component 1:0..1 Component Budget
```

Business rules:

- A component may exist before its budget is prepared.
- A component may have no more than one budget.
- The Base Contract component budget represents the original job budget.
- Change-order component budgets represent proposed or approved additional scope.
- Warranty component budgets represent planned corrective-work cost.
- Warranty component budgets contain no revenue.

Budget rules by component type:

| Component Type | Cost Allowed | Revenue Allowed |
|---|:---:|:---:|
| Base Contract | Yes | Yes |
| Change Order | Yes | Yes |
| Warranty | Yes | No |

---

## 3.2 Budget Detail

Confirmed relationship:

```text
Component Budget 1:M Budget Detail
```

Every budget must contain at least one cost code with applicable phases and cost types.

Budget hierarchy:

```text
Component Budget
→ Cost Code
→ Phase
→ Cost Type
```

Budget-detail values may include:

- Quantity
- Unit of measure
- Unit cost
- Total cost
- Revenue

Business rules:

- A budget must have at least one cost-code detail structure.
- Applicable phases and cost types must be included.
- Quantity, unit cost, total cost, and revenue values may remain blank while the budget is being prepared.
- A finalized budget must satisfy validation rules defined during database design.
- Component budgets reference cost codes activated on the parent job.

---

## 3.3 Original, Change-Order, and Warranty Budget Rollups

Original budget:

```text
Original Budget
=
Base Contract Component Budget
```

Approved change-order budget:

```text
Approved Change-Order Budget
=
Sum of Approved Change-Order Component Budgets
```

Revised contract budget:

```text
Revised Contract Budget
=
Base Contract Budget
+ Approved Change-Order Budgets
```

Warranty budget:

```text
Warranty Budget
=
Sum of Warranty Component Budgets
```

Warranty budget remains separate from revised contract revenue but is included on the cost side of WIP.

---

## 3.4 Budget Adjustments

Each budget adjustment belongs to one Component Budget.

Confirmed relationship:

```text
Component Budget 1:M Budget Adjustment
```

An adjustment may apply at any level within that component budget:

```text
Entire Component Budget
Cost Code
Cost Code + Phase
Cost Code + Phase + Cost Type
```

Budget Adjustment may include:

- Component budget
- Cost code, optional
- Phase, optional
- Cost type, optional
- Adjustment date
- Quantity adjustment
- Cost adjustment
- Reason
- Entered by
- Notes

Business rules:

- An adjustment cannot affect multiple components.
- The adjustment remains at the level entered.
- The system does not automatically allocate higher-level adjustments to lower-level details.
- Budget adjustments cannot alter revenue.
- Reports must combine component budget detail and applicable adjustments at the correct reporting level.

---

# 4. Job Cost Codes, Phases, and Foreman Responsibility

## 4.1 Job Cost Codes

Cost codes are activated at the parent-job level.

Confirmed relationship:

```text
Job 1:M Job Cost Code
```

Business rules:

- The Base Contract budget establishes the initial active job cost codes.
- A change order may introduce a new cost code.
- A cost code introduced by a change order is added to the parent job’s active cost-code setup.
- Components do not maintain separate cost-code master records.
- Component budgets reference the applicable parent-job cost codes.
- Only cost codes activated on the parent job are available for posting.

Example:

```text
Job 12345
├── 1000 Milling
├── 2500 Paving
└── 3000 Concrete — Added by Change Order 002
```

---

## 4.2 Job Cost-Code Phases

Phases are activated for each job cost code.

Confirmed relationship:

```text
Job Cost Code 1:M Job Cost-Code Phase
```

Standard phases:

```text
900 — Production
905 — Travel
```

Business rules:

- Phases `900` and `905` are activated when a cost code is added to the job.

Warranty phases:

```text
950 — Warranty Production
955 — Warranty Travel
```

Business rules:

- Phases `950` and `955` are added only when warranty activity is required for the applicable cost code.

Example:

```text
Job 12345 / Cost Code 2500
├── 900 Production
├── 905 Travel
├── 950 Warranty Production
└── 955 Warranty Travel
```

---

## 4.3 Foreman Responsibility

Foreman profitability responsibility is assigned at:

```text
Job
+ Cost Code
```

Confirmed relationships:

```text
Job Cost Code 1:M Foreman Responsibility
Employee 1:M Foreman Responsibility
```

Foreman Responsibility may include:

- Job
- Cost code
- Foreman
- Responsibility percentage

Business rules:

- Each active job cost code should have at least one responsible foreman.
- Normally one foreman receives `100%` responsibility.
- A cost code may be shared by multiple foremen.
- When shared equally, responsibility is divided evenly.
- Responsibility percentages for a job cost code must total `100%`.
- The assigned foreman is responsible for the cost code’s performance and profitability.
- Foreman responsibility applies across base and change-order work because actual costs are combined at the parent-job and cost-code level.
- Warranty work may separately identify the employee or foreman who performed the repair.

Examples:

```text
Job 12345 / Cost Code 2500
- Foreman A: 100%
```

```text
Job 12345 / Cost Code 3000
- Foreman B: 50%
- Foreman C: 50%
```

---

# 5. Job Responsibility Assignments

Each job has one current:

- Project manager
- Superintendent
- Sales representative
- Estimator
- Coordinator

Business rules:

- Each role has one current assigned employee.
- Assignments may change for scheduling or reassignment purposes.
- Assignment history is not required.
- Current assignments are stored on the parent Job.
- Components inherit the parent-job assignments.
- Component-level responsibility exceptions are not required.
- Change-order and warranty components retain the same division as the parent job.
- The coordinator is responsible for the entire job.
- Coordinator assignment is generally based on division.

A division may retain a default coordinator for initial job setup, but the assigned coordinator is stored on the Job.

---

# 6. Crews and Employees

## 6.1 Crew Structure

Crews are stable estimating and planning templates.

Examples:

```text
Pave 1
Pave 2
Patch 1
Concrete 1
Seal 1
Seal 2
Stripe 1
Stripe 2
```

Confirmed relationship:

```text
Crew 1:M Employee
Employee M:1 Primary Crew
```

Business rules:

- Crews do not belong to divisions.
- Each crew has a stable name.
- Each employee has one primary crew.
- An employee’s primary crew may change.
- Historical primary-crew tracking is not required.
- An employee may temporarily work with another crew for a day without changing the employee’s primary crew.
- Crew templates are used primarily for estimating, staffing assumptions, and planning.
- Actual daily crew composition is determined from employee time entry.

Crew attributes may include:

- Crew name
- Default foreman
- Active status
- Standard crew size
- Standard labor composition
- Notes

---

## 6.2 Daily Crew Composition

A separate Daily Crew Assignment entity is not required.

Employee time entry provides enough information to determine who worked together.

Daily working groups may be inferred using:

- Work date
- Job
- Cost code
- Phase
- Overlapping start and end times
- Foreman context
- Primary crew where useful

This avoids creating a second daily assignment record that could conflict with payroll time entry.

---

# 7. Employee Time, Labor Cost, and Burden

## 7.1 Employee Time Entry

Confirmed Employee Time Entry grain:

```text
Employee Time Entry
- Employee
- Work date
- Job
- Cost code
- Phase
- Labor code
- Start time
- End time
- Hours
```

Business rules:

- Employee time does not track Job Component.
- Base and change-order work are normally combined because the work is frequently completed at the same time and cannot be practically separated.
- Warranty work is identified through warranty phases `950` and `955`.
- Labor code identifies the task performed and may differ from the employee’s normal role.
- Each time-entry record belongs to one employee and one job.

Confirmed relationships:

```text
Employee 1:M Employee Time Entry
Job 1:M Employee Time Entry
Job Cost Code 1:M Employee Time Entry
Phase 1:M Employee Time Entry
Labor Code 1:M Employee Time Entry
```

---

## 7.2 Estimated Daily Labor Cost

Employee time entry does not store the final applied payroll rate.

Before payroll is finalized:

```text
Estimated Labor Cost
=
Time-Entry Hours
× Applicable Estimated Rate
```

The estimated applicable rate is determined from:

- Employee regular rate
- Effective date
- Prevailing-wage classification, when applicable
- Higher-of regular or prevailing-wage rule

Business rules:

- Estimated labor cost supports same-day operational reporting.
- Estimated labor cost is used until final payroll cost is posted.
- Estimated labor cost may differ from final payroll cost.
- The final posted job-cost transaction becomes authoritative.
- The time entry itself does not store the final applied rate.

---

## 7.3 Labor and Burden

Labor and burden remain separate cost types:

```text
L — Labor
B — Burden
```

One Employee Time Entry may produce:

```text
Employee Time Entry
├── Estimated Labor Cost
├── Estimated Tax Burden
└── Estimated Indirect Burden
```

After payroll processing:

```text
Employee Time Entry
├── Final Labor Job-Cost Transaction
├── Final Tax-Burden Job-Cost Transaction
└── Final Indirect-Burden Job-Cost Transaction
```

Business rules:

- Burden transactions remain traceable to the source time activity where possible.
- Overtime premium is posted to overhead rather than as direct job labor.
- Tax burden and indirect burden remain separately calculable even when reported under cost type `B`.

---

# 8. Equipment Usage

## 8.1 Equipment Usage Grain

Confirmed Equipment Usage structure:

```text
Equipment Usage
- Work date
- Equipment unit
- Job
- Cost code
- Phase
- Usage hours
```

Business rules:

- Equipment Usage does not require Job Component.
- Base and change-order equipment activity is normally combined.
- Warranty equipment is distinguished using phases `950` and `955`.
- Only final approved usage hours need to be retained.
- Preliminary calculated equipment hours do not need to be retained.

Confirmed relationships:

```text
Equipment Unit 1:M Equipment Usage
Job 1:M Equipment Usage
Job Cost Code 1:M Equipment Usage
Phase 1:M Equipment Usage
Equipment Unit 1:M Equipment Rate
```

---

## 8.2 Equipment Cost

Before final equipment posting:

```text
Estimated Equipment Cost
=
Usage Hours
× Applicable Equipment Rate
```

The applicable rate is obtained from the effective-dated equipment-rate table for the equipment unit and usage date.

After processing:

- Final equipment cost is stored in Posted Job Cost.
- Posted equipment cost becomes authoritative.
- The source Equipment Usage record remains available for operational traceability.

---

# 9. Material Activity

## 9.1 Material Transaction

Confirmed Material Transaction structure:

```text
Material Transaction
- Work date
- Job
- Cost code
- Phase
- Material item
- Quantity
- Unit of measure
- Applied unit cost
- Source type
```

Material source types may include:

```text
Inventory Issue
Supplier Ticket
Manual Adjustment
```

Confirmed relationships:

```text
Job 1:M Material Transaction
Material Item 1:M Material Transaction
Job Cost Code 1:M Material Transaction
Phase 1:M Material Transaction
```

Business rules:

- Inventory and purchased materials use the same conceptual transaction structure.
- Source type identifies how the material transaction originated.
- Applied unit cost is retained on the final transaction.
- Later changes to inventory average cost or vendor pricing must not alter historical job cost.
- Material activity does not require Job Component.
- Warranty material activity is distinguished through warranty phases.

---

# 10. Purchase Orders, Supplier Tickets, and Commitments

## 10.1 Purchase Orders

Confirmed relationships:

```text
Job 1:M Purchase Order
Vendor 1:M Purchase Order
Purchase Order 1:M Purchase Order Line
```

Business rules:

- Each purchase order belongs to one job.
- Each purchase order belongs to one vendor.
- A purchase order may contain multiple lines.
- A purchase order may span multiple cost codes, phases, and items.
- Purchase orders may cover:
  - Materials
  - Subcontractors
  - Rented equipment
  - Other committed job costs

---

## 10.2 Supplier Tickets

Confirmed relationships:

```text
Purchase Order 1:M Supplier Ticket
Supplier Ticket 1:M Supplier Ticket Line
Purchase Order Line 1:M Supplier Ticket Line
```

Business rules:

- One purchase-order line may be supported by one supplier ticket or many supplier tickets.
- Each supplier-ticket line relates to one purchase-order line.
- One supplier ticket may contain one or more lines.
- Ticket quantities accumulate against the applicable purchase-order line.
- Office review may complete the PO-line match when it is not known at initial entry.

Example:

```text
PO 45025
└── Line 1 — Binder Asphalt, 200 tons
    ├── Ticket 10081 — 21.50 tons
    ├── Ticket 10083 — 22.10 tons
    ├── Ticket 10088 — 20.75 tons
    └── Additional tickets
```

For materials without load tickets, the purchase-order line may be fulfilled through a receiving record or supplier invoice rather than multiple ticket records.

---

## 10.3 Open Commitments

Open commitment is calculated as:

```text
Open Commitment
=
Committed Purchase-Order Amount
− Invoiced Amount
```

Business rules:

- Open commitments are calculated from purchase-order lines.
- A separate manually maintained commitment entity is not required.
- A separate commitment snapshot is not required for the initial solution.
- Open purchase orders through the reporting cutoff are included in job commitments.
- Commitment reporting may also include ordered, delivered, invoiced, and remaining quantities where available.

---

# 11. Posted Job Cost

## 11.1 Posted Job-Cost Transaction

Each final Posted Job-Cost Transaction contains:

```text
Posted Job Cost
- Posting date
- Work or usage date
- Job
- Cost code
- Phase
- Cost type
- Amount
- Quantity
- Source type
- Source record
- Source-system transaction number
```

Possible source types include:

```text
Payroll
Equipment
Inventory
Supplier Invoice
Subcontractor Invoice
Manual Journal
```

Business rules:

- Posted Job Cost is the authoritative financial job-cost record.
- Historical posted amounts do not depend on recalculating current rates.
- Source identifiers are retained for audit and reconciliation.
- Actual Job Component is not generally required because base and change-order actual costs are combined.
- Warranty transactions are identifiable through phases `950` and `955`.

---

## 11.2 Estimated and Final Cost Replacement

Operational activity and Posted Job Cost remain separate.

```text
Operational Activity
→ Estimated Cost

Final Processing
→ Posted Job Cost
```

Reporting rules:

- Use estimated cost while final posted cost is unavailable.
- Use Posted Job Cost once final cost becomes available.
- The estimate is replaced by final cost for reporting purposes.
- Estimated and final costs must never be added together.
- A job may contain a combination of estimated and final activity while processing is incomplete.
- Source linkage should identify which estimated activity has been replaced by final posting.

This supports same-day operational reporting without double-counting costs.

---

## 11.3 Reversals and Corrections

Posted errors are corrected by reversal and reposting.

```text
Original Transaction
→ Reversal Transaction
→ Corrected Transaction
```

Recommended lineage fields:

```text
Reverses Transaction ID
Corrects Transaction ID
Correction Reason
Correction Date
Corrected By
```

Business rules:

- Original transactions remain in history.
- Reversal transactions offset the original amount.
- Corrected transactions are posted as new records.
- Reports use the net transaction effect.
- Audit reporting can trace the full correction chain.
- Posted transactions are not overwritten.

---

# 12. Change-Order Actual Cost Treatment

Change-order actual labor, material, and equipment are not normally tracked separately from the base job.

Business rationale:

- Change-order work is frequently completed at the same time as the original scope.
- The same employees, equipment, and materials may perform both scopes.
- Field separation would require arbitrary or unreliable allocations.
- The solution must not create false precision.

Default treatment:

```text
Base Component 12345-000
→ Base and change-order actual costs
```

Change-order components retain:

- Change-order description
- Submitted scope
- Budget
- Revenue
- Approval status
- Billing support
- Contract-value impact

The system may accurately report:

- Change-order quoted revenue
- Change-order budgeted cost
- Change-order budgeted margin
- Approval status
- Amount included in revised contract value
- Overall impact on parent-job profitability

The system must not claim to report precise individual change-order actual cost or margin unless actual costs were genuinely captured separately.

Warranty activity remains separately identifiable through:

- Warranty component
- Warranty phases `950` and `955`
- Warranty budget
- Warranty actual cost

---

# 13. Customer Invoices and Billing

## 13.1 Customer Invoice

Confirmed relationships:

```text
Job 1:M Customer Invoice
Customer Invoice 1:M Customer Invoice Line
```

Business rules:

- Each customer invoice belongs to exactly one parent job.
- One job may have many invoices.
- Each invoice may contain many invoice lines.
- An invoice cannot include multiple jobs.
- An invoice may include base contract and approved change-order billing.
- Invoice lines do not need to identify cost codes, phases, cost types, or job components.
- Billing analysis is performed at the total-job level.

---

## 13.2 Billed Revenue

```text
Total Billed to Date
=
Sum of Customer Invoice Amounts for the Job
```

Invoice lines may retain descriptive detail for the customer-facing invoice, but analytical billing remains at the parent-job level.

---

# 14. WIP and Earned Revenue

## 14.1 WIP Grain

WIP is calculated at the parent-job level.

Only approved change orders are included in revised revenue and budget.

Warranty budget and warranty actual cost are included on the cost side of WIP.

Confirmed calculation:

```text
Percent Complete
=
(
    Base Actual Cost
    + Change-Order-Related Actual Cost
    + Warranty Actual Cost
    + Open Commitments
)
÷
(
    Base Budget Cost
    + Approved Change-Order Budget Cost
    + Warranty Budget Cost
    + Applicable Budget Adjustments
)
```

Business rules:

- Percent complete is capped at `100%`.
- Earned revenue is capped at revised contract revenue.
- Submitted, pending, rejected, and cancelled change orders are excluded.
- Warranty adds no revenue.
- Warranty budget is included in the denominator.
- Warranty actual cost is included in costs to date.
- Warranty costs reduce overall job profitability.
- Parent-job reporting combines base, approved change-order, and warranty cost activity.

---

## 14.2 Revised Revenue

```text
Revised Revenue
=
Base Contract Revenue
+ Approved Change-Order Revenue
```

Warranty revenue is always zero.

---

## 14.3 Earned Revenue

```text
Earned Revenue
=
Percent Complete
× Revised Revenue
```

Earned revenue cannot exceed revised revenue.

---

# 15. Forecasting

## 15.1 Current Forecast

Each job retains one current forecast.

Confirmed relationship:

```text
Job 1:1 Current Forecast
Current Forecast 1:M Forecast Detail
```

Current Forecast may include:

- Forecast date
- Prepared by
- Remaining cost to complete
- Forecast final cost
- Forecast final revenue
- Forecast final margin
- Forecast notes

Forecast detail is entered primarily by cost code.

Optional cost-type detail may be entered when practical.

```text
Job Cost Code 1:M Forecast Detail
Cost Type 1:M Forecast Detail — optional
```

Calculations:

```text
Forecast Final Cost
=
Costs to Date
+ Open Commitments
+ Remaining Cost to Complete
```

```text
Forecast Final Margin
=
Forecast Final Revenue
− Forecast Final Cost
```

Business rules:

- Warranty cost and anticipated remaining warranty work are included when known.
- Remaining cost is entered at least by cost code.
- Cost-type detail is optional.
- Only the latest forecast is required.
- A new forecast replaces the previous forecast.
- Formal historical forecast snapshots are not retained.

---

# 16. Job Closure, Reopening, and Component Status

## 16.1 Closed Job Posting Restriction

A job with status `Closed` blocks all operational and financial posting.

Blocked activity includes:

- Labor
- Equipment
- Materials
- Supplier invoices
- Subcontractor invoices
- Manual job-cost entries
- Warranty activity

Transactions must be rejected or held until the job is formally reopened.

---

## 16.2 Reopening

Reopening occurs at the parent-job level.

Confirmed relationship:

```text
Job 1:M Reopen Event
```

Reopen Event may include:

- Reopen date
- Reopen reason
- Reopened by
- Reclose date
- Reclosed by
- Notes

Business rules:

- Individual components are not independently reopened for posting.
- Reopening the parent job permits authorized posting.
- A reopen event is required for late cost or warranty activity after closure.
- The job is formally reclosed after processing is complete.
- Reopen and reclose history is retained.

---

## 16.3 Component Lifecycle Status

Each Job Component has its own lifecycle status separate from the parent-job status.

Examples:

```text
12345-000 — Base Contract — Complete
12345-001 — Change Order — Approved
12345-002 — Change Order — Rejected
12345-W01 — Warranty — Closed
```

Parent job:

```text
12345 — Closed
```

Business rules:

- Component status describes the lifecycle of the individual scope item.
- Parent-job status controls whether posting is permitted.
- Component status does not independently reopen a closed parent job.
- A warranty component may be created while the parent job is closed, but posting requires reopening the parent job.
- Rejected and cancelled components remain in history.
- Component numbers are not reused.

---

# 17. Consolidated Conceptual Relationship Model

```text
Customer
└── 1:M Opportunity
    └── 1:M Quote
        └── 1:M Bid Budget Revision

Job
├── 1:M Job Quote Source
│   └── M:1 Quote
│
├── 1:M Job Component
│   ├── Base Contract
│   ├── Change Order
│   └── Warranty
│
├── 1:M Job Cost Code
│   ├── 1:M Job Cost-Code Phase
│   └── 1:M Foreman Responsibility
│
├── 1:M Employee Time Entry
├── 1:M Equipment Usage
├── 1:M Material Transaction
├── 1:M Purchase Order
├── 1:M Posted Job Cost
├── 1:M Customer Invoice
├── 1:1 Current Forecast
└── 1:M Reopen Event
```

Component budget structure:

```text
Job Component
└── 1:0..1 Component Budget
    ├── 1:M Budget Detail
    └── 1:M Budget Adjustment
```

Purchasing structure:

```text
Purchase Order
├── 1:M Purchase Order Line
└── 1:M Supplier Ticket
    └── 1:M Supplier Ticket Line
        └── M:1 Purchase Order Line
```

Labor structure:

```text
Employee
└── 1:M Employee Time Entry
    ├── Estimated Labor Cost
    ├── Estimated Tax Burden
    ├── Estimated Indirect Burden
    └── Final Posted Job-Cost Transactions
```

---

# 18. Key Decisions Made

1. A separate Property entity will not be included in the initial solution.
2. Site information will be stored on Opportunity and copied to Job.
3. Each opportunity requires one customer and represents one site.
4. One opportunity may have many quotes; each quote belongs to one opportunity.
5. Quotes support multiple bid-budget revisions.
6. Accepted quotes are linked to jobs through Job Quote Source.
7. Direct unresolved many-to-many relationships are prohibited.
8. Each job automatically receives Base Component `000`.
9. Change-order component numbers are assigned when created and are never reused.
10. Warranty components use the `W01`, `W02`, and subsequent sequence.
11. Component type and component status are separate.
12. Division is assigned at the parent-job level and never changes.
13. Change orders and warranty remain with the parent job’s division.
14. Crews do not belong to divisions.
15. Employees have one primary crew, but daily working groups are derived from time entry.
16. Each component may have one component budget.
17. Budget adjustments belong to one component budget and may apply at any hierarchy level.
18. Cost codes are activated at the parent-job level.
19. Change orders may add cost codes to the parent job.
20. Phases `900` and `905` are activated with the job cost code.
21. Warranty phases `950` and `955` are activated when needed.
22. Foreman responsibility is assigned at Job plus Cost Code.
23. Foreman responsibility may be shared using percentages totaling `100%`.
24. Employee time does not track Job Component.
25. Change-order actual costs are normally combined with base-job costs.
26. Warranty costs are distinguished using warranty phases.
27. Same-day reporting uses estimated cost until final posted cost is available.
28. Final Posted Job Cost replaces estimated cost in reporting.
29. Posted errors are reversed and reposted rather than overwritten.
30. One customer invoice may contain many lines but belongs to one job.
31. Billing analysis is performed at the total-job level.
32. WIP is calculated at the parent-job level.
33. Only approved change orders affect revised revenue, revised budget, and WIP.
34. Warranty budget and actual cost are included on the cost side of WIP.
35. Warranty adds no revenue.
36. Each job retains one current forecast.
37. Historical forecast snapshots are not required.
38. Closed jobs block all posting.
39. Reopening occurs at the parent-job level.
40. Components retain their own lifecycle status.

---

# 19. Open Questions

No unresolved relationship questions remain from SA-002.

Physical implementation questions will be addressed during later architecture and database-design sessions, including:

- Surrogate-key strategy
- Physical table names
- Foreign-key implementation
- Status lookup structure
- Effective-dated rate tables
- Estimated-to-final transaction matching
- Finalized-budget validation
- WIP reporting-date implementation
- Source-system integration keys
- Incremental-load and correction handling

---

# 20. Next Session

The next Solution Architecture session should translate the conceptual relationships into the initial logical architecture.

Recommended next session:

```text
SA-003 – Logical Data Model and Subject Areas
```

SA-003 should define:

- Master-data subject areas
- Sales and estimating subject area
- Job and budget subject area
- Field-activity subject area
- Purchasing and commitments subject area
- Posted job-cost subject area
- Billing and WIP subject area
- Forecasting subject area
- Entity ownership
- Transaction grain
- Integration boundaries
- Initial logical data-flow model

---