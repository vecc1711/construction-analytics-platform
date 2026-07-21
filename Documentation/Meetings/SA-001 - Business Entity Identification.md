# SA-001 – Business Entity Identification

**Project:** Construction Analytics Solution  
**Sprint:** Sprint 3 – Solution Architecture  
**Session:** SA-001  
**Status:** Complete  
**Date:** July 16, 2026  

---

## Objective

Identify the major business entities required to support estimating, job budgeting, field operations, labor, equipment, materials, purchasing, job cost, work in progress, forecasting, warranty analysis, and closed-job review.

This session focused on business concepts and lifecycle requirements rather than physical database tables, columns, keys, or implementation details.

---

## Design Principles

Each identified entity should:

1. Represent a distinct business concept.
2. Have a clear business purpose.
3. Support a documented business process, requirement, or analytical question.
4. Have a defined owner, lifecycle, or source.
5. Avoid duplicating another entity’s responsibility.
6. Support traceability from operational activity to financial reporting.
7. Preserve original records where audit visibility is required.
8. Use the standard job-cost hierarchy where applicable.
9. Distinguish operational activity from finalized accounting activity.
10. Support future analytical capabilities without treating unavailable historical data as actual data.

---

## Topics Reviewed

- Customer and property relationships
- Opportunity, quote, and bid-budget lifecycle
- Job creation and original job budgets
- Change-order budgets
- Warranty budgets
- Budget adjustments
- Job responsibility assignments
- Employees, crews, and labor activity
- Prevailing-wage requirements
- Labor burden and overtime treatment
- Equipment usage and costing
- Inventory and purchased materials
- Purchase orders and commitments
- Delivery tickets and vendor invoices
- Job-cost transactions
- Billing, WIP, and forecasting
- Warranty and job closeout
- Standard cost codes, phases, and cost types

---

## Customer and Property

### Customer

A customer is the organization contractually responsible for the work.

Business rules:

- One customer may have multiple properties.
- One customer may have multiple jobs.
- A property-management company may be the customer while work is performed at a separately identified property.
- Default billing information belongs to the customer.
- Job-specific billing information may override the customer defaults.

Default customer billing information may include:

- Billing contact
- Billing address
- Billing email
- Payment terms

### Site Information

A separate reusable Property entity is not required for the initial solution.

Each Opportunity represents work at one site and stores the proposed site information directly. When the work is awarded, the final site information is copied to the Job and retained as the authoritative historical site record for that job.

A future Property entity may be introduced if centralized property management, repeat-site matching, or property-level analytics becomes necessary.

### Customer Contact

A customer may have multiple contacts for billing, contract administration, project communication, or executive communication.

### Site Contact

A site contact represents an individual responsible for access or communication at a specific property.

### Job Billing Override

A job may store billing information that overrides the customer defaults.

Possible overrides include:

- Billing contact
- Billing address
- Billing email
- Payment terms
- Billing instructions

---

## Sales and Estimating

### Opportunity

An opportunity represents potential work being pursued.

Business rules:

- One opportunity may have multiple quotes.
- Quotes may represent options, alternatives, sections, or separate portions of proposed work.
- One awarded job may originate from multiple opportunities.

### Quote

A quote is the customer-facing proposal.

Business rules:

- A quote normally has one supporting bid budget.
- One opportunity may have multiple quotes.
- If only part of a quote is accepted, the quote and budget are revised before the work is sold.
- The final accepted quote represents the complete awarded scope.
- Multiple accepted quotes may contribute to one job.

### Bid Budget

A bid budget is the internal cost, quantity, revenue, and margin plan supporting a quote before award.

Business rules:

- One bid budget normally supports one quote.
- Multiple accepted bid budgets may be combined into one original job budget.
- A bid budget uses the standard hierarchy:

```text
Cost Code
→ Phase
→ Cost Type
```

### Bid Budget Detail

Each bid-budget detail includes:

- Cost code
- Phase
- Cost type
- Quantity
- Unit of measure
- Unit cost
- Total cost
- Revenue

Calculation:

```text
Total Cost = Quantity × Unit Cost
```

Quantity and unit cost are stored directly. Total cost is calculated.

---

## Job and Budget Structure

### Job

A job represents awarded work.

Each job has:

- One customer
- One property/site
- One owning division
- One project manager
- One superintendent
- One sales representative
- One estimator
- One original job budget

A job may also have:

- Multiple originating opportunities
- Multiple originating quotes
- Multiple project coordinators
- Multiple foremen
- Multiple cost codes
- Multiple change orders
- One or more warranty budgets
- Multiple budget adjustments
- Multiple lifecycle events
- Multiple customer invoices
- Multiple job-cost transactions

Business rules:

- One job equals one site.
- One job belongs to one customer.
- A job may be formed from multiple accepted quotes and bid budgets.
- A job retains one owning division even when crews from multiple divisions perform work.
- Only cost codes included in the approved job budget are available for posting.

### Job Lifecycle

The core job statuses are:

- Awarded
- In Progress
- Complete
- Closed
- Cancelled

Important dates include:

- Award/Sold Date
- Start Date
- Complete Date
- Close Date
- Reopen Date
- Reclose Date

Business rules:

- Awarded means the work has been sold and established as a job.
- In Progress means field work has started.
- Complete means physical work is finished, but additional costs may still be processed or posted.
- Closed means no additional costs may be posted.
- Cancelled means the awarded work will not proceed.
- A closed job must be formally reopened before late costs, corrections, or warranty activity can be posted.
- A job may be reopened more than once.
- Reopen and reclose activity should be retained through lifecycle events.

### Job Budget

The job budget is the original approved cost and revenue plan for the awarded job.

Business rules:

- Each job has one original job budget.
- The original job budget is created from the accepted bid budgets.
- The original job budget must remain permanently preserved.
- Scope changes do not overwrite the original job budget.
- Original contract revenue originates from the original job budget.
- Revenue cannot be modified through an ordinary budget adjustment.

### Standard Budget Hierarchy

The standard hierarchy is:

```text
Job
→ Cost Code
→ Phase
→ Cost Type
```

There are no additional levels such as:

- Proposal line
- Quote section
- Work area
- Location
- Bid item
- Schedule of values item

### Job Budget Detail

Each job-budget detail includes:

- Quantity
- Unit of measure
- Unit cost
- Total cost
- Revenue

Calculation:

```text
Total Cost = Quantity × Unit Cost
```

Quantity and unit cost are stored directly. Total cost is calculated.

### Revenue Allocation

Revenue is entered initially at the cost-code level.

Revenue is allocated across the cost types within the cost code based on each cost type’s percentage of total cost.

```text
Cost-Type Revenue
=
Cost-Code Revenue
×
Cost-Type Cost
÷
Total Cost-Code Cost
```

This supports revenue and margin analysis for:

- Labor
- Burden
- General Materials
- Major Materials
- Equipment
- Subcontractors
- Miscellaneous

The cost-code revenue is the business input. Cost-type revenue is an analytical allocation.

---

## Change Orders

A change order represents additional or revised scope after job award.

Business rules:

- Each change order belongs to one job.
- Each change order has its own budget.
- Change orders do not overwrite the original job budget.
- Only accepted change orders are included in revised budget and revenue calculations.
- A change order may increase cost and revenue.
- A change-order budget uses the same hierarchy as the original job budget.

```text
Change Order
→ Cost Code
→ Phase
→ Cost Type
```

Potential change-order statuses include:

- Draft
- Submitted
- Pending
- Accepted
- Rejected
- Cancelled

The final status catalog will be confirmed during logical design.

### Change-Order Budget Detail

Each change-order budget detail may include:

- Quantity
- Unit of measure
- Unit cost
- Total cost
- Revenue

Only accepted change-order budgets are included in revised job analysis.

---

## Warranty Budgets

A warranty budget is a separate zero-revenue budget attached to the original job.

Business rules:

- Warranty work remains on the original job.
- Warranty work uses the original job cost codes.
- Warranty production uses phase `950`.
- Warranty travel uses phase `955`.
- Warranty budgets contain cost but no revenue.
- Warranty work does not create a separate job.
- A warranty budget is separate from the original job budget.
- A warranty budget is separate from change-order budgets.
- A warranty budget may be entered depending on the expected warranty scope.

The warranty budget uses the standard hierarchy:

```text
Job
→ Cost Code
→ Warranty Phase
→ Cost Type
```

---

## Budget Adjustments

A budget adjustment modifies budget quantity or cost without overwriting the original budget.

An adjustment may:

- Increase quantity
- Decrease quantity
- Increase cost
- Decrease cost
- Apply to the entire job
- Apply to a cost code
- Apply to a phase
- Apply to a cost type

Each adjustment should retain:

- Adjustment date
- Adjustment value
- Adjustment type
- Reason
- Entered by
- Notes

Business rules:

- No formal approval is required.
- Adjustments may be positive or negative.
- Adjustments may affect quantity or cost.
- Budget adjustments cannot modify contract revenue.
- Original budget values must remain preserved.
- Adjustments must remain visible for audit and analysis.

### Budget Calculations

```text
Revised Budget
=
Original Job Budget
+ Accepted Change-Order Budgets
```

```text
Adjusted Budget
=
Revised Budget
+ Budget Adjustments
```

```text
Revised Contract Revenue
=
Original Contract Revenue
+ Accepted Change-Order Revenue
```

Warranty budgets and ordinary budget adjustments do not increase contract revenue.

---

## Organization and Responsibility

### Division

The confirmed divisions are:

- Paving
- Patch
- Seal & Lines
- Concrete

Business rules:

- Each job has one owning division.
- Cost codes are not owned by divisions.
- Any qualified crew may perform any applicable cost code.
- A job may contain work performed by multiple divisions while retaining one owning division.

### Employee

An employee represents an individual worker, manager, coordinator, estimator, or operational employee.

An employee may have:

- A normal employee role
- A normal crew
- A temporary daily role
- A job-level responsibility
- A cost-code responsibility
- Multiple time entries during a workday

### Employee Role

An employee role represents the employee’s normal organizational or operational role.

Examples include:

- Laborer
- Foreman
- Operator
- Project Manager
- Superintendent
- Project Coordinator
- Sales Representative
- Estimator

An employee’s actual daily role may differ from the normal role.

For example, a second crew member may become acting foreman when a crew is split for the day.

### Job Responsibility Assignment

Job-level responsibilities include:

- Project manager
- Superintendent
- Sales representative
- Estimator

Each job normally has one employee assigned to each responsibility.

### Project Coordinator Assignment

Project coordinator responsibility may be based on:

- Division
- Crew
- Cost code
- Work type
- Specific job

Business rules:

- The coordinator assigned to the job’s owning division is normally the primary scheduling contact.
- Specialized coordinators may support selected crews or cost codes across multiple jobs.
- A job may involve multiple coordinators.
- One coordinator field on the job would not fully represent coordinator responsibility.

### Foreman Assignment

Foreman responsibility is associated with job cost codes rather than only the overall job.

Business rules:

- One job may have multiple foremen.
- One foreman may be responsible for multiple cost codes.
- Each applicable job cost code may have a planned foreman.
- The actual daily foreman may differ from the planned foreman.
- Foreman incentives are based on direct labor performance for assigned cost codes.
- Burden is excluded from foreman incentive analysis.

```text
Job
→ Cost Code
→ Foreman Assignment
```

---

## Crews and Labor

### Crew

A crew is a persistent named operational group.

Examples include:

- Pave 1
- Concrete 1
- Patch 2
- Sealcoat 1

Each crew normally has:

- One division
- One default foreman
- A stable core group of employees
- A typical work type

Business rules:

- Crews are named and relatively consistent from day to day.
- Crew composition may be adjusted based on scheduled work.
- Employees may move between crews.
- Crews may be augmented with additional employees.
- A crew may be split into multiple work groups during a day.
- A temporary crew leader may act as foreman for a split crew.

### Crew Membership

Crew membership represents an employee’s current normal crew.

Business rules:

- Current normal crew membership is required.
- Historical default crew membership is not required.
- Actual daily participation is captured through daily activity.

### Daily Crew Assignment

A daily crew assignment represents a crew assigned to a date, job, cost code, and phase.

Business rules:

- A crew may work multiple cost codes during one day.
- Crew members may split across different cost codes.
- Multiple crews may work on the same job during the same day.
- Multiple crews may work on the same cost code during the same day.
- Crew-level data alone is not sufficiently detailed to represent actual labor activity.

### Employee Time Entry

The employee time entry is the authoritative labor activity record.

Required fields include:

- Employee
- Work date
- Job
- Cost code
- Phase
- Hours
- Start time
- End time
- Labor code

Business rules:

- One employee may have multiple time entries on the same job and date.
- One employee may work multiple cost codes during the same day.
- One employee may have both travel and production entries on the same day.
- Employees may perform different labor classifications during the same day.
- Labor activity is analyzed using the date the work occurred.

Example:

```text
7:00–8:00    Travel       Driver
8:00–11:00   Paving       Operator
11:00–12:00  Paving       Laborer
12:30–3:30   Grading      Operator
3:30–4:30    Travel       Driver
```

### Labor Code

A labor code represents the specific work classification performed for a time entry.

Examples include:

- Foreman
- Operator
- Raker
- Driver
- Laborer
- Concrete Finisher
- Curb Setter
- Line Painter

Business rules:

- The labor code belongs to the employee time entry.
- The labor code may differ from the employee’s normal role.
- Labor codes support prevailing-wage rate selection.
- Labor codes support analysis of the tasks actually performed.

### Employee Pay Rate

An employee pay rate stores the employee’s normal wage rate.

Rates should support effective dates because employee rates may change over time.

Historical labor costing must retain or reproduce the rate applicable when the work occurred.

### Prevailing-Wage Schedule

A prevailing-wage schedule represents wage requirements applicable to a prevailing-wage project.

A schedule may be associated with:

- Job
- Jurisdiction
- Effective period
- Labor classifications

### Prevailing-Wage Rate

A prevailing-wage rate stores the required rate for a labor classification.

```text
Applicable Pay Rate
=
Higher of:

Employee Regular Rate
or
Applicable Prevailing-Wage Rate
```

Prevailing-wage rates must support effective dates.

### Overtime Treatment

Overtime is calculated at the end of the workweek.

Business rules:

- Each direct job receives labor at the regular rate.
- The overtime premium is removed from the direct job.
- The overtime premium is posted to an overhead job.
- Overtime overhead is recovered through indirect burden.
- This prevents job performance from being affected by the day of the week the work occurred.

```text
Employee Time Entry
→ Direct Job Labor at Regular Rate
```

```text
Weekly Overtime Calculation
→ Overtime Premium
→ Overhead Job
```

---

## Labor Burden

Burden is a separate cost type:

```text
B — Burden
```

Burden is not combined with direct labor.

Burden consists of:

- Tax Burden
- Indirect Burden

### Tax Burden

Tax burden includes employer payroll costs such as:

- FICA
- FUTA
- SUTA
- Other payroll taxes

### Indirect Burden

Indirect burden recovers indirect overhead using a fixed percentage of direct labor dollars.

```text
Indirect Burden
=
Direct Labor Dollars × Indirect Burden Rate
```

The indirect burden rate is approximately 95% per direct labor dollar but may change over time.

Burden rates should support effective dates.

### Posting Treatment

```text
Labor Cost Type
- Direct labor hours
- Direct labor dollars
```

```text
Burden Cost Type
- Tax burden
- Indirect burden
```

Labor and burden remain separate in:

- Budgets
- Job cost
- Revenue allocation
- Variance analysis
- Forecasting
- Margin analysis

---

## Equipment

### Equipment Type

An equipment type groups similar equipment units.

Examples include:

- Skid Steer
- Tri-Axle Dump
- Paver
- Roller
- Pickup Truck

Equipment of the same type normally has the same internal hourly rate, although each unit retains its own rate assignment.

### Equipment Unit

An equipment unit represents an individually tracked physical asset.

Business rules:

- Every equipment unit is tracked separately.
- Every equipment unit has a preassigned internal hourly rate.
- Equipment units may be grouped by equipment type.
- Individual-unit tracking is required for future cost and benefit analysis.

### Equipment Rate History

Equipment rate history stores effective-dated internal hourly rates for each equipment unit.

Business rules:

- Each equipment unit has its own rate.
- Rates may change over time.
- Travel and production use the same rate.
- Historical job cost must use the rate applicable when the equipment was used.
- Later rate changes must not alter historical equipment cost.

### Equipment Usage Entry

An equipment usage entry records the use of an individual equipment unit.

Required fields include:

- Equipment unit
- Work date
- Job
- Cost code
- Phase
- Hours
- Crew
- Hourly rate
- Total equipment cost

The operator is not required.

```text
Equipment Cost
=
Equipment Hours × Applicable Hourly Rate
```

Business rules:

- Total equipment cost is calculated.
- A unit receives a separate entry for each cost code and phase where it was used.
- A unit is not charged automatically to every cost code worked by the crew.
- One equipment unit may have multiple entries on the same job and date.
- Equipment usage is attributed to the date used.

### Division Equipment Utilization Standard

A division equipment utilization standard defines the factor used to calculate chargeable equipment hours.

```text
Equipment Hours
=
Applicable Crew Shift Hours × Division Utilization Factor
```

Example:

```text
Crew shift hours: 5.0
Division utilization factor: 90%

Equipment hours:
5.0 × 90% = 4.5 hours
```

Business rules:

- The utilization factor varies by division.
- The factor may change over time.
- Foremen identify which equipment was used.
- Foremen identify the applicable job, cost code, and phase.
- Foremen do not need to track exact operating minutes.
- Calculated equipment hours support standardized equipment recovery.

### Rented Equipment

Externally rented equipment is acquired through the purchase-order process but posts to:

```text
E — Equipment
```

The purchasing method does not determine the final cost type.

### Equipment Ownership and Operating Costs

Ownership and operating costs are maintained separately from internal job charges.

Potential ownership costs include:

- Depreciation
- Lease or loan cost
- Insurance
- Registration
- Taxes

Potential operating costs include:

- Fuel
- Repairs
- Maintenance
- Parts
- Other operating expenses

### Equipment Recovery

Equipment recovery compares internal job charges to ownership and operating cost.

```text
Equipment Recovery
=
Internal Job Equipment Charges
− Equipment Ownership and Operating Costs
```

The future target is analysis by individual equipment unit.

This supports evaluation of:

- Internal hourly rates
- Utilization
- Replacement decisions
- Rent-versus-own decisions
- Individual asset profitability

---

## Materials and Inventory

### Material

A material represents an item consumed or purchased for a job.

A material may retain:

- Material name
- Unit of measure
- Material classification
- Standard cost type
- Inventory indicator
- Active status

### Material Classification

Materials are classified as Major Materials or General Materials.

Major Materials are:

- Concrete
- Asphalt
- Sealer
- Crackfill Rubber
- Paint

Major Materials post to:

```text
M — Major Materials
```

All other materials are General Materials and post to:

```text
G — General Materials
```

The material master should determine the applicable material cost type.

### Inventory Item

An inventory item represents a material maintained in company inventory.

Business rules:

- Inventory is maintained at one central location.
- The foreman reports the quantity used.
- Inventory usage reduces inventory.
- Inventory usage is charged immediately to the job.
- Inventory materials use average inventory cost.

### Inventory Average Cost History

Inventory average cost changes over time.

Business rules:

- Each inventory issue must retain the average unit cost applied at the time of use.
- Historical job cost must not change when the current average cost changes.

### Inventory Usage

Inventory usage represents material removed from inventory and applied to a job.

Required information may include:

- Work date
- Job
- Cost code
- Phase
- Inventory item
- Quantity
- Unit of measure
- Applied average unit cost
- Total cost
- Entered by

```text
Inventory Material Cost
=
Quantity Used × Average Cost in Effect
```

### Purchased Material Usage

Purchased material usage records the foreman’s report of non-inventory material used during the day.

Examples include:

- Asphalt
- Concrete
- Curb
- Other project-specific materials

Business rules:

- The foreman reports the quantity used.
- The reported quantity is an operational estimate.
- Supplier load tickets are entered individually.
- Supplier-ticket totals may differ from foreman-reported quantities.
- Vendor invoice quantities may also differ.
- Reported, ticketed, and invoiced quantities must remain separately identifiable.

Example:

```text
Foreman-reported asphalt quantity: 450.00 tons
Supplier ticket total:             448.12 tons
```

---

## Vendors and Purchasing

### Vendor

A vendor represents an external supplier, subcontractor, rental provider, or service provider.

Vendor types may include:

- Material supplier
- Subcontractor
- Equipment rental company
- Service provider

### Purchase Order

A purchase order represents an external commitment for a job.

Business rules:

- Each purchase order belongs to one job.
- Each purchase order belongs to one vendor.
- A purchase order cannot contain multiple jobs.
- A purchase order may contain multiple cost codes.
- A purchase order may contain multiple phases.
- A purchase order may contain multiple materials or services.
- A purchase order may contain subcontracted services.
- A purchase order may contain rented equipment.
- The purchase order contains the negotiated vendor price.

### Purchase-Order Line

Each purchase-order line maps to:

```text
Job
→ Cost Code
→ Phase
→ Cost Type
```

A purchase-order line may represent:

- Material
- Subcontracted service
- Equipment rental
- Miscellaneous external cost

### Delivery Ticket

A delivery ticket represents an individual supplier load or delivery record.

Required information may include:

- Ticket number
- Delivery date
- Purchase order
- Purchase-order line
- Job
- Vendor
- Cost code
- Phase
- Material
- Quantity
- Unit of measure
- Unit price
- Extended amount

Business rules:

- Supplier tickets are retained individually.
- Multiple tickets may be entered against one daily purchase order.
- Ticket quantity is used as the final operational quantity for purchased-material analysis.
- Ticket totals may differ from foreman-reported quantities.

### Vendor Invoice

A vendor invoice represents the supplier’s billing document.

Business rules:

- Vendor invoices are received after materials or services are delivered.
- Invoice processing converts committed cost into posted actual job cost.
- Vendor invoices should be matched to the applicable purchase order.

### Vendor Invoice Line

A vendor invoice line supports detailed matching among:

- Purchase-order line
- Delivery ticket
- Material or service
- Job
- Cost code
- Phase
- Cost type
- Job-cost transaction

### Open Commitment

An open commitment represents the remaining value of open purchase orders through the reporting cutoff date.

Open purchase orders may include:

- Materials
- Subcontractors
- Rented equipment
- Other expected external job costs

```text
Remaining Commitment
=
Purchase-Order Amount
− Invoiced or Posted Amount
```

The exact treatment of partial invoicing will be finalized during transaction-flow design.

---

## Job Cost and Accounting

### Job-Cost Transaction

A job-cost transaction represents an individual posted cost line.

Transaction types may include:

- Employee labor
- Burden
- Inventory materials
- Purchased materials
- Equipment
- Subcontractors
- Miscellaneous costs
- Corrections
- Reversals

Business rules:

- Job cost must retain individual transaction line items.
- Job-cost totals must be traceable to underlying transactions.
- The analytical transaction date is the date used.
- Accounting posting date must also be retained.
- Transactions use the standard hierarchy:

```text
Job
→ Cost Code
→ Phase
→ Cost Type
```

### Operational Activity Versus Posted Cost

Labor, equipment, and inventory activity are entered and reviewed before being posted to official job cost.

Historical process:

```text
Daily Field Entry
→ Review and Validation
→ Weekly Payroll or Closeout Processing
→ Posted Job Cost
```

The solution must distinguish among:

- Entered activity
- Reviewed activity
- Posted cost
- Reversed cost
- Reposted cost

Daily preliminary activity may support operational reporting, but it must remain distinguishable from finalized accounting cost.

### Preliminary Cost

The future solution may calculate preliminary cost from reviewed, unposted activity.

```text
Preliminary Labor Cost
=
Entered Labor Hours × Applicable Labor Rate
```

```text
Preliminary Equipment Cost
=
Calculated Equipment Hours × Applicable Equipment Rate
```

```text
Preliminary Inventory Cost
=
Quantity Used × Average Inventory Cost
```

Preliminary cost must not be double counted after the related activity is posted.

### Posting Batch

A posting batch represents the weekly payroll or closeout process that transfers reviewed activity into job cost.

A posting batch may include:

- Labor
- Burden
- Equipment
- Inventory materials

Posting-batch information supports reconciliation between operational activity and official job-cost transactions.

### Reversal and Correction

Incorrect posted transactions must be corrected through reversal and reposting.

The original transaction must not be overwritten.

Required relationships include:

- Original transaction
- Reversal transaction
- Corrected transaction
- Correction reason
- Correction date
- User making the correction

```text
Original Posted Transaction
→ Reversal Transaction
→ Corrected Reposted Transaction
```

---

## Billing

A billing transaction represents customer billing for a job.

Business rules:

- Each customer invoice belongs to exactly one job.
- A job may have multiple customer invoices.
- An invoice cannot contain multiple jobs.
- Billed revenue is analyzed as total job billings through the reporting cutoff date.
- Billing does not need to be allocated by cost code, phase, or cost type.

```text
Billed Revenue to Date
=
Sum of Job Invoices Through Cutoff Date
```

---

## Work in Progress

WIP uses a cost-to-cost percentage-complete methodology.

### Current Adjusted Budgeted Cost

```text
Current Adjusted Budgeted Cost
=
Original Budgeted Cost
+ Accepted Change-Order Budgeted Cost
+ Cost Budget Adjustments
```

### Percent Complete

```text
Percent Complete
=
(Actual Cost to Date + Open Commitments)
÷ Current Adjusted Budgeted Cost
```

Percent complete cannot exceed 100%.

### Earned Revenue

```text
Earned Revenue
=
Percent Complete × Revised Contract Revenue
```

Earned revenue cannot exceed revised contract revenue.

If a job reaches 100% earned revenue while meaningful costs remain, a cost budget adjustment may be entered to reflect expected remaining spend.

### Overbilling and Underbilling

Overbilling and underbilling are calculated at the job level.

```text
Net Billing Position
=
Billed Revenue − Earned Revenue
```

- Positive value indicates overbilling.
- Negative value indicates underbilling.

Results may be summarized by:

- Division
- Project manager
- Customer
- Reporting period
- Company

The calculation originates at the job level.

---

## Forecasting

### Remaining to Spend

Remaining to spend represents future expected cost that is not yet:

- Posted as actual cost
- Included in an open purchase order
- Otherwise committed

Business rules:

- Remaining to spend is estimated by project managers and superintendents.
- The preferred future detail level is by cost type.
- The practical initial entry level is by cost code.
- The model should allow future phase and cost-type detail.
- Only the latest estimate is required.
- Formal forecast history is not required for the initial solution.

Potential fields include:

- Job
- Cost code
- Optional phase
- Optional cost type
- Remaining-to-spend amount
- Forecast date
- Entered by
- Notes

### Forecast Final Cost

```text
Forecast Final Cost
=
Actual Cost
+ Open Commitments
+ Remaining to Spend
```

### Forecast Final Margin

```text
Forecast Final Margin
=
Revised Contract Revenue
− Forecast Final Cost
```

### Forecast Margin Percentage

```text
Forecast Margin %
=
Forecast Final Margin
÷ Revised Contract Revenue
```

---

## Performance Analysis

### Fade and Gain

Fade and gain are based on cost performance.

```text
Fade or Gain
=
Adjusted Budgeted Cost
− Actual Cost
```

Interpretation:

- Positive result: Gain
- Negative result: Fade
- Zero: On budget

Fade and gain should be available at:

- Job
- Cost code
- Phase
- Cost type
- Division
- Project manager
- Superintendent
- Foreman

### Foreman Labor Performance

Foreman performance includes direct labor hours and direct labor cost.

```text
Labor Hours Variance
=
Budgeted Labor Hours
− Actual Labor Hours
```

```text
Labor Cost Variance
=
Budgeted Direct Labor Cost
− Actual Direct Labor Cost
```

Positive values indicate favorable performance.

Burden is excluded from foreman labor incentive analysis.

### Material Performance

Material performance includes quantity and cost.

```text
Material Quantity Variance
=
Budgeted Quantity
− Actual Quantity
```

```text
Material Cost Variance
=
Budgeted Material Cost
− Actual Material Cost
```

Final actual quantity sources are:

- Inventory issue quantities for inventory materials
- Supplier delivery-ticket quantities for purchased materials

Foreman-reported quantities may be used for same-day operational review but are not the final source for closed-job variance analysis.

Unit-of-measure conversion may be required when budget, field, supplier, and invoice units differ.

---

## Warranty and Closeout

### Warranty Treatment

Warranty costs are always charged to the original job and original cost codes.

Warranty phases are:

- `950` — Warranty Production
- `955` — Warranty Travel

Business rules:

- Warranty work does not generate additional revenue.
- Warranty work may receive a separate warranty budget.
- Warranty work remains part of the original job’s final profitability.
- Warranty cost must remain separately identifiable from original production cost.
- Travel phases never contain production quantity.

### Warranty Responsibility

Warranty performance is attributed to the original:

- Job
- Division
- Project manager
- Superintendent
- Foreman
- Crew
- Cost code

The solution should also retain the employees, foreman, or crew that actually performed the warranty repair.

This supports two questions:

1. Which original project generated the warranty cost?
2. Who performed the corrective work?

### Complete Versus Closed

A job is Complete when the physical scope of work is finished.

At this point:

- Field production has ended.
- Labor may still need to be processed.
- Equipment may still need to be posted.
- Material invoices may still be outstanding.
- Purchase orders may remain open.
- Final billing may still be underway.
- Additional costs may still be posted.

A job is Closed when financial processing is finalized and no additional costs may be posted.

```text
Physical Work Finished
→ Complete
→ Final Cost Processing
→ Closed
```

A closed job must be reopened before additional transactions can be posted.

### Closure Review Indicators

The following may be displayed as warnings before closure:

- Open purchase orders
- Unposted field activity
- Outstanding commitments
- Unprocessed vendor invoices
- Remaining-to-spend amount
- Incomplete change orders
- Unfinalized customer billing

These are review indicators rather than mandatory technical blocks.

The primary closure rule is:

> Closed means no additional costs may be posted unless the job is formally reopened.

### Closed Job Review

A closed job is normally reviewed once.

Business rules:

- A frozen financial snapshot is not required.
- Financial values are calculated from final underlying budget and cost records.
- Review notes may be retained.

Potential review information includes:

- Job
- Review date
- Reviewer
- Findings
- Corrective action
- Estimating feedback
- Operations feedback
- Notes

Review measures may include:

- Original budget
- Accepted change-order budget
- Warranty budget
- Budget adjustments
- Adjusted budget
- Actual cost
- Final contract revenue
- Gross margin
- Fade or gain
- Labor-hours variance
- Labor-cost variance
- Burden variance
- Material-quantity variance
- Material-cost variance
- Equipment variance
- Warranty cost

---

## Standard Cost Codes, Phases, and Cost Types

### Cost Codes

Cost codes are standardized company-wide.

Examples include:

- `1000` — Milling
- `1200` — Reclaim
- `1300` — Grading
- `3000` — Concrete

Business rules:

- Cost codes are not restricted to one division.
- Any qualified crew may perform a cost code.
- Only cost codes included in the approved job budget are activated for the job.
- Transactions may post only to cost codes active for the job.
- Custom job-specific cost codes are not required.

### Phases

| Code | Phase |
|---|---|
| 900 | Production |
| 905 | Travel |
| 950 | Warranty Production |
| 955 | Warranty Travel |

Business rules:

- Phases `900` and `905` are normally available for each active job cost code.
- Phases `950` and `955` are added only when warranty work is required.
- Warranty phases are normally added when a job is reopened.
- Travel phases do not contain production quantity.
- Production phases may support production quantity when available.

### Cost Types

| Code | Cost Type |
|---|---|
| L | Labor |
| B | Burden |
| G | General Materials |
| M | Major Materials |
| E | Equipment |
| S | Subcontractors |
| Z | Miscellaneous |

#### L — Labor

Includes direct employee labor hours and dollars.

#### B — Burden

Includes tax burden and indirect burden.

#### G — General Materials

Includes all materials not defined as Major Materials.

#### M — Major Materials

Includes:

- Concrete
- Asphalt
- Sealer
- Crackfill Rubber
- Paint

#### E — Equipment

Includes:

- Owned equipment internal charges
- Externally rented equipment

#### S — Subcontractors

Includes external labor or services such as:

- Police details
- Traffic control
- Testing
- Other subcontracted services

#### Z — Miscellaneous

Typically includes non-taxable incidental costs such as:

- Dumping fees
- Hotel rooms
- Permits
- Other miscellaneous costs not assigned to another cost type

### Unit of Measure

Units of measure support:

- Budgets
- Materials
- Production
- Equipment
- Job-cost transactions

Examples include:

- Hours
- Tons
- Square feet
- Linear feet
- Cubic yards
- Gallons
- Each
- Loads

Unit conversion may be required when supplier, inventory, budget, and field-reporting units differ.

---

## Major Entities Identified

### Customer and Property

- Customer
- Customer Contact
- Property / Site
- Site Contact
- Job Billing Override
- Property Type

### Sales and Estimating

- Opportunity
- Quote
- Bid Budget
- Bid Budget Detail

### Jobs and Budgets

- Job
- Job Lifecycle Event
- Job Budget
- Job Budget Detail
- Change Order
- Change-Order Budget
- Change-Order Budget Detail
- Warranty Budget
- Warranty Budget Detail
- Budget Adjustment
- Remaining-to-Spend Estimate

### Organization and Responsibility

- Division
- Employee
- Employee Role
- Job Responsibility Assignment
- Project Coordinator Assignment
- Foreman Assignment

### Crews and Labor

- Crew
- Crew Membership
- Daily Crew Assignment
- Employee Time Entry
- Labor Code
- Employee Pay Rate
- Prevailing-Wage Schedule
- Prevailing-Wage Rate
- Job Prevailing-Wage Assignment

### Equipment

- Equipment Type
- Equipment Unit
- Equipment Rate History
- Equipment Usage Entry
- Division Equipment Utilization Standard
- Equipment Ownership Cost
- Equipment Operating Cost
- Equipment Recovery

### Materials and Inventory

- Material
- Material Classification
- Inventory Item
- Inventory Average Cost History
- Inventory Usage
- Purchased Material Usage

### Vendors and Purchasing

- Vendor
- Purchase Order
- Purchase-Order Line
- Delivery Ticket
- Vendor Invoice
- Vendor Invoice Line
- Open Commitment
- Commitment Category

### Job Cost and Accounting

- Job-Cost Transaction
- Posting Batch
- Transaction Status
- Transaction Source
- Reversal and Correction Relationship
- Billing Transaction

### Financial Performance

- Burden Rate
- Burden Transaction
- WIP Calculation
- Earned Revenue Calculation
- Overbilling / Underbilling Position
- Job Forecast
- Remaining-to-Spend Estimate
- Fade / Gain
- Closed Job Review

### Reference Data

- Cost Code
- Phase
- Cost Type
- Unit of Measure
- Property Type
- Job Status
- Change-Order Status
- Transaction Status
- Commitment Category

---

## Key Decisions

1. Customer and property are separate entities.
2. Each job belongs to one customer and one site.
3. One customer may have multiple properties and jobs.
4. One property may have multiple jobs over time.
5. Opportunities may contain multiple quotes.
6. One job may originate from multiple opportunities and accepted quotes.
7. Quote and bid budget are separate business concepts.
8. Multiple accepted bid budgets may become one original job budget.
9. Bid budgets and job budgets are separate lifecycle concepts.
10. Original job budgets must remain preserved.
11. Change orders retain separate budgets.
12. Only accepted change orders affect revised budget and revenue.
13. Warranty work uses a separate zero-revenue warranty budget.
14. Warranty costs remain on the original job and original cost codes.
15. Budget adjustments may change quantity or cost but not revenue.
16. Revenue originates only from original and accepted change-order budgets.
17. Revenue is entered at the cost-code level and allocated to cost types based on cost percentages.
18. The standard hierarchy is Job → Cost Code → Phase → Cost Type.
19. Job budgets control which cost codes are available for posting.
20. Cost codes are standardized company-wide.
21. Cost codes are not owned by divisions.
22. Employee time entry is the authoritative labor activity record.
23. Labor code represents the task actually performed.
24. Employee normal role and actual daily role are separate concepts.
25. Prevailing-wage rates may override regular rates when higher.
26. Burden is a separate cost type.
27. Tax burden and indirect burden remain separately identifiable.
28. Overtime premium is posted to overhead rather than direct jobs.
29. Crews are persistent named groups with adjustable daily composition.
30. Historical default crew membership is outside the initial scope.
31. Foreman responsibility is associated with job cost codes.
32. Planned and actual daily foreman may differ.
33. Equipment is tracked by individual unit.
34. Equipment rates are effective-dated.
35. Equipment hours are calculated using crew shift hours and division factors.
36. Equipment usage is recorded separately by cost code and phase.
37. Rented equipment posts to Equipment cost type.
38. Inventory materials use average inventory cost.
39. Inventory has one central location.
40. Foreman-reported material quantities remain separate from supplier-ticket quantities.
41. Supplier delivery tickets are retained individually.
42. Purchase orders belong to one job and one vendor.
43. Purchase orders may contain multiple cost codes, phases, and items.
44. Open purchase orders define committed cost.
45. Job cost retains individual transaction line items.
46. Date used drives operational and financial analysis.
47. Operational activity and posted accounting cost remain separate states.
48. Posted corrections use reversal and reposting.
49. Original posted transactions are not overwritten.
50. Each customer invoice belongs to one job.
51. Billed revenue is analyzed at the job level.
52. Remaining to spend is estimated by project managers and superintendents.
53. Remaining to spend will initially be entered by cost code, with future cost-type detail supported.
54. Only the latest forecast is required.
55. WIP uses cost-to-cost percentage complete.
56. Earned revenue cannot exceed revised contract revenue.
57. Fade and gain equal adjusted budgeted cost minus actual cost.
58. Foreman labor performance includes hours and dollars but excludes burden.
59. Material performance includes quantity and cost variance.
60. Complete and Closed are separate job lifecycle stages.
61. Complete jobs may still receive costs.
62. Closed jobs may not receive additional costs unless formally reopened.
63. Reopening must be controlled and auditable.
64. Warranty does not generate additional revenue.
65. The solution uses seven standard cost types: L, B, G, M, E, S, and Z.

---

## Open Questions

No open business-entity questions remain for SA-001.

The following items will be addressed during SA-002 and later architecture sessions:

- Relationship cardinality
- Relationship optionality
- Identifying and non-identifying relationships
- Many-to-many relationship resolution
- Entity consolidation
- Transaction lineage
- Budget lineage
- Physical table design
- Key selection
- Attribute definitions
- Source-system ownership
- Historical-data requirements
- Fact and dimension classification
- Reporting-view design

---

## Documentation Updates

The following documentation should be updated after SA-001:

- DOC-006 Architecture Decision Log
- DOC-007 Project Traceability Matrix
- PROJECT_STATUS.md

The following documents should remain unchanged until additional solution-architecture work is complete:

- README.md
- PROJECT_ROADMAP.md
- CHANGELOG.md
- DOC-001 Functional Specification
- DOC-002 Technical Design
- DOC-003 Data Dictionary
- DOC-004 KPI Dictionary
- DOC-008 KPI Catalog

---

## Next Session

**SA-002 – Entity Relationship Definition**

The next session will define:

- Entity cardinality
- Required and optional relationships
- Parent-child relationships
- Many-to-many relationships
- Opportunity, quote, budget, and job lineage
- Job and site relationships
- Budget and change-order relationships
- Responsibility assignments
- Employee, crew, and daily-activity relationships
- Equipment and material relationships
- Purchasing and commitment relationships
- Job-cost transaction lineage
- Billing, WIP, and forecast relationships