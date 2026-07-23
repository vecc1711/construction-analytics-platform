# SA-004 – Logical Entity and Attribute Definition

## Construction Analytics Solution

**Workshop:** SA-004 – Logical Entity and Attribute Definition  
**Sprint:** Sprint 3 – Solution Architecture  
**Status:** Complete  
**Version:** 1.0  
**Date:** July 23, 2026

---

## 1. Purpose

The purpose of SA-004 is to define the logical entities, essential attributes, identifiers, and analytical grain required for the Construction Analytics Solution.

During the workshop, the design was intentionally reduced from a detailed operational-system model to a lean analytics model. The sample database is intended to demonstrate construction job-cost expertise, relational design, SQL analytics, WIP calculations, forecasting, and dashboard development. It is not intended to replace payroll, purchasing, billing, estimating, field-management, or accounting systems.

---

## 2. Scope Boundary

The sample database will support:

- Customer, opportunity, quote, and job lineage
- Base contract, change order, and warranty components
- Original budgets and budget adjustments
- Labor, equipment, material, production, and delay activity
- Posted job costs and open commitments
- Customer billing
- Current forecasts
- Weekly and month-end WIP snapshots
- Power BI and Tableau reporting

The following are deferred:

- Full payroll processing
- Supplier ticket and invoice workflow
- Detailed operational-to-financial matching
- Approval routing
- Employee personal information not required for analytics
- Equipment-rate history
- Crew-assignment history
- Full job reopen-event history
- Multi-company configuration
- ERP-grade audit, correction, and workflow controls

---

## 3. Core Modeling Principles

1. The primary job-cost hierarchy is:

   `Job → Cost Code → Phase → Cost Type`

2. Job becomes authoritative for customer, site, division, responsibility, status, and contract values after award.

3. A Job may originate from multiple accepted Quotes. All accepted Quotes collectively make up the original Job and do not require additional source classifications.

4. A Job contains Job Components for Base Contract, Change Order, and Warranty scope.

5. Each Job Component may have no more than one Component Budget.

6. Original budget values are retained on Budget Detail. Changes are stored separately as Budget Adjustments.

7. Operational activity provides same-day labor, equipment, material, production, and delay data.

8. Posted Job Cost is the authoritative source for final financial cost.

9. The portfolio solution will not build an ERP-style cost-matching engine. When posted cost exists for the same Job, Work Date, Cost Code, Phase, and Cost Type, posted cost is used. Otherwise, estimated operational cost is used.

10. WIP snapshots preserve weekly and month-end history. Current Forecast stores one current forecast per Job.

---

## 4. Logical Subject Areas and Entities

### 4.1 Customer and Sales

#### Customer

Purpose: Stores customer master data and an optional parent-customer relationship.

Key attributes:

- Customer ID
- Customer Number
- Customer Name
- Parent Customer ID
- Address fields
- Active Flag

#### Opportunity

Purpose: Stores pre-award opportunity and site information.

Key attributes:

- Opportunity ID
- Opportunity Number
- Customer ID
- Site Name
- Site Address
- Sales Representative
- Estimator
- Division
- Status
- Created Date
- Expected Award Date
- Estimated Value
- Description

#### Quote

Purpose: Stores customer-facing quote revisions.

Key attributes:

- Quote ID
- Quote Number
- Revision Number
- Opportunity ID
- Status
- Submitted Date
- Accepted Date
- Quoted Revenue
- Description

Quote statuses:

- Draft
- Submitted
- Revised
- Accepted
- Rejected
- Withdrawn
- Expired
- Superseded

#### Bid Budget

Purpose: Stores the internal estimate associated with a Quote revision.

Key attributes:

- Bid Budget ID
- Quote ID
- Revision Date
- Prepared By
- Status
- Total Estimated Cost
- Total Revenue
- Estimated Gross Profit
- Estimated Gross Margin
- Notes

---

### 4.2 Job and Scope

#### Job

Purpose: Stores the awarded construction Job and authoritative post-award attributes.

Identifiers:

- Internal Job ID
- Source-system Job Number

Key attributes:

- Job Number
- Job Name
- Customer ID
- Site Name
- Site Address
- Division
- Status
- Contract Date
- Start Date
- Estimated Completion Date
- Actual Completion Date
- Closed Date
- Reopened Flag
- Most Recent Reopened Date
- Project Manager
- Superintendent
- Sales Representative
- Estimator
- Coordinator
- Original Contract Revenue
- Revised Contract Revenue
- Description

Job statuses:

- Pending
- Active
- On Hold
- Substantially Complete
- Complete
- Closed
- Cancelled

A reopened Job returns to Active. The current Reopened Flag and Most Recent Reopened Date are retained without creating a detailed reopen-event history table.

#### Job Quote Source

Purpose: Preserves lineage between the Job and each accepted Quote and Bid Budget.

Key attributes:

- Job ID
- Quote ID
- Accepted Bid Budget ID
- Accepted Date
- Accepted Revenue
- Source Sequence
- Notes

All linked Quotes collectively form the original Job. No Primary Award, Additional Scope, or Alternate classification is required.

#### Job Component

Purpose: Stores Base Contract, Change Order, and Warranty components.

Key attributes:

- Job Component ID
- Job ID
- Component Number
- Component Type
- Component Status
- Description
- Created Date
- Approval Date
- Approved By
- Completion Date
- Revenue
- Notes

Component-number examples:

- `12345-000` – Base Contract
- `12345-001` – Change Order 1
- `12345-W01` – Warranty 1

Job Components use the same status list as Job. Approval Date and Approved By may remain blank for the Base Contract.

---

### 4.3 Budget and Cost Structure

#### Component Budget

Purpose: Stores the single budget associated with a Job Component.

Key attributes:

- Component Budget ID
- Job Component ID
- Budget Status
- Budget Date
- Prepared By
- Total Budget Cost
- Total Revenue
- Total Gross Profit
- Total Gross Margin
- Notes

Budget totals are both stored and calculated. Stored totals support reporting and preserve the approved summary, while calculated totals are used for validation.

#### Budget Detail

Grain:

`Component Budget + Cost Code + Phase + Cost Type`

Key attributes:

- Budget Detail ID
- Component Budget ID
- Cost Code
- Phase Code
- Cost Type Code
- Original Quantity
- Unit of Measure
- Original Unit Cost
- Original Total Cost
- Original Allocated Revenue
- Notes

Quantity and Unit of Measure are optional to support lump-sum budget lines.

When Quantity and Unit Cost are available:

`Original Total Cost = Original Quantity × Original Unit Cost`

Allocated Revenue is stored at Budget Detail level after being calculated from the Cost Code revenue allocation. Detail allocations must total the Cost Code revenue.

#### Budget Adjustment

Purpose: Preserves changes without overwriting original Budget Detail values.

Key attributes:

- Budget Adjustment ID
- Component Budget ID
- Budget Detail ID
- Adjustment Date
- Adjustment Reason
- Quantity Change
- Unit Cost Change
- Total Cost Change
- Entered By
- Status
- Notes

Adjustment statuses:

- Draft
- Active
- Reversed
- Cancelled

Only Active adjustments affect the current budget. Unit-cost and quantity changes recalculate current total cost. Direct total-cost changes are allowed for lump-sum lines.

#### Cost Code

Attributes:

- Cost Code
- Cost Code Name
- Description

Example:

- Code: 1000
- Name: Milling
- Description: 1000 - Milling

Cost Codes are available across all divisions.

#### Phase

Attributes:

- Phase Code
- Phase Name
- Description

Initial values:

- 900 – Production
- 905 – Travel
- 950 – Warranty Production
- 955 – Warranty Travel

Production and Travel phases are added automatically when a Job Cost Code is used. Warranty phases are used only when warranty work occurs.

#### Cost Type

Attributes:

- Cost Type Code
- Cost Type Name
- Description
- Quantity Required Indicator
- Unit Cost Supported Indicator

Initial values:

- L – Labor
- B – Burden
- G – General Materials
- M – Major Materials
- E – Equipment
- S – Subcontractors
- Z – Miscellaneous

---

### 4.4 Workforce and Field Activity

#### Employee

Purpose: Stores only employee attributes required for operational analytics.

Key attributes:

- Employee ID
- Employee Number
- First Name
- Last Name
- Full Name
- Position
- Department
- Primary Crew Code
- Foreman Indicator
- Active Flag
- Hire Date
- Termination Date

Date of Birth and home-address fields are excluded from the sample analytics database.

#### Crew

Purpose: Stores planning crews.

Key attributes:

- Crew Code
- Crew Name
- Crew Description
- Active Flag

Crew is used for planning only. Actual daily work groups are derived from time-entry records.

#### Employee Labor Rate

Purpose: Stores multiple labor classifications and rates for an Employee.

Key attributes:

- Employee Labor Rate ID
- Employee ID
- Rate Code
- Rate Name
- Regular Time Rate
- Overtime Rate
- Double Time Rate

Examples:

- 10 – Laborer
- 12 – Foreman
- 15 – Striper

#### Employee Time Entry

Purpose: Stores operational labor activity.

Key attributes:

- Time Entry ID
- Employee ID
- Work Date
- Job ID
- Cost Code
- Phase Code
- Labor Code
- Rate Code
- Foreman Employee ID
- Start Time
- End Time
- Regular Hours
- Overtime Hours
- Double Time Hours
- Total Hours

Validation:

`Total Hours = Regular Hours + Overtime Hours + Double Time Hours`

Daily work groups are derived as:

`Job + Work Date + Foreman`

Estimated Labor Cost is calculated from the applicable Employee labor rates. Estimated Burden remains separate under Cost Type B.

#### Equipment

Key attributes:

- Equipment ID
- Equipment Number
- Equipment Name
- Description
- Equipment Type
- Status
- Ownership Type
- Active Flag
- Current Hourly Rate

Equipment ID is the identifier. Equipment Number remains the business-facing number and may contain values such as G-1, G-2, or Pulv-1.

#### Equipment Usage

Key attributes:

- Equipment Usage ID
- Work Date
- Equipment ID
- Job ID
- Cost Code
- Phase Code
- Usage Hours
- Applied Hourly Rate
- Estimated Total Cost

Calculation:

`Estimated Total Cost = Usage Hours × Applied Hourly Rate`

The applied rate is retained on the usage record for historical accuracy.

#### Material Category

Initial categories:

- Asphalt
- Concrete
- Sealer
- Crackfill Rubber
- Paint
- General Materials
- Other

The category list is extensible.

#### Material

Key attributes:

- Material ID
- Optional Material Code
- Material Name
- Description
- Material Category ID
- Default Unit of Measure
- Major Material Indicator
- Active Flag

Material ID is used as the stable identifier while source material-code quality remains unresolved.

Asphalt examples include 3/4 Binder, 1/2 Binder, Top, 1/2-inch Top, and 3/8-inch Top.

#### Material Usage

Key attributes:

- Material Usage ID
- Work Date
- Job ID
- Cost Code
- Phase Code
- Material ID
- Quantity
- Unit of Measure
- Applied Unit Cost
- Total Cost
- Vendor ID
- Ticket Number

Calculation:

`Total Cost = Quantity × Applied Unit Cost`

#### Daily Production

Key attributes:

- Daily Production ID
- Work Date
- Job ID
- Cost Code
- Foreman Employee ID
- Production Measure
- Quantity Produced
- Unit of Measure
- Notes

#### Operational Delay

Key attributes:

- Delay ID
- Work Date
- Job ID
- Cost Code
- Foreman Employee ID
- Delay Type
- Delay Hours
- Description
- Notes

Delay hours are entered directly; start and end times are not required.

---

### 4.5 Purchasing and Financial Activity

#### Vendor

Identifier:

- Internal Vendor ID

Key attributes:

- Vendor ID
- Vendor Number
- Vendor Name
- Vendor Type
- Active Flag

#### Purchase Order Commitment

Purpose: Provides an analytical PO-line view without modeling a full purchasing workflow.

Key attributes:

- PO Number
- PO Line Number
- Job ID
- Vendor ID
- Cost Code
- Phase Code
- Cost Type Code
- Material ID
- Description
- Ordered Quantity
- Unit of Measure
- Unit Cost
- Committed Amount
- Invoiced Amount
- Open Commitment
- PO Status

Calculation:

`Open Commitment = Committed Amount - Invoiced Amount`

#### Posted Job Cost

Purpose: Stores authoritative financial job-cost transactions.

Key attributes:

- Posted Job Cost ID
- Posting Date
- Work Date
- Job ID
- Cost Code
- Phase Code
- Cost Type Code
- Amount
- Quantity
- Unit of Measure
- Source Type
- Source Transaction Number
- Reversal Indicator
- Description

The sample solution will use a simplified estimate-replacement rule rather than a detailed matching engine.

#### Customer Billing

Purpose: Stores simplified invoice or billing transactions at Job level.

Key attributes:

- Invoice Number
- Job ID
- Customer ID
- Invoice Date
- Invoice Status
- Invoice Amount
- Retainage Amount
- Paid Amount
- Balance Due
- Description

Cost Code and Job Component references are intentionally omitted.

---

### 4.6 Forecasting and WIP

#### Current Forecast

Purpose: Stores one current forecast per Job.

Key attributes:

- Forecast ID
- Job ID
- Forecast Date
- Prepared By
- Forecast Final Cost
- Forecast Final Revenue
- Forecast Gross Profit
- Forecast Gross Margin
- Notes

#### Forecast Detail

Key attributes:

- Forecast Detail ID
- Forecast ID
- Cost Code
- Optional Cost Type Code
- Cost to Date
- Open Commitments
- Remaining Cost to Complete
- Forecast Final Cost
- Notes

Calculation:

`Forecast Final Cost = Cost to Date + Open Commitments + Remaining Cost to Complete`

#### WIP Snapshot

Purpose: Preserves weekly and month-end WIP history.

Key attributes:

- WIP Snapshot ID
- Job ID
- Reporting Date
- Snapshot Type
- Revised Budget Cost
- Posted Cost to Date
- Estimated Unposted Cost
- Open Commitments
- Revised Revenue
- Percent Complete
- Earned Revenue
- Billed Revenue
- Over or Under Billing
- Forecast Final Cost
- Forecast Gross Profit
- Forecast Gross Margin

Snapshot types:

- Weekly
- Month End

Percent Complete and Earned Revenue are capped at 100 percent and Revised Revenue, respectively, for reporting purposes.

---

## 5. Simplified Estimated-Versus-Posted Cost Rule

A full record-level matching engine is outside the portfolio scope.

For analytical reporting:

1. Aggregate estimated operational costs by Job, Work Date, Cost Code, Phase, and Cost Type.
2. Aggregate posted costs at the same grain.
3. When posted cost exists at that grain, use posted cost as the financial amount.
4. When posted cost does not yet exist, use estimated operational cost.
5. Continue using operational hours and quantities for production and utilization analysis after financial cost posts.

This rule is a documented portfolio simplification and may be replaced with source-specific reconciliation logic in a production implementation.

---

## 6. Logical Model Summary

The approved lean logical model contains 31 entities across the following subject areas:

- Customer and Sales
- Estimating
- Job and Scope
- Budget and Cost Structure
- Workforce
- Field Activity
- Equipment
- Materials
- Purchasing
- Financial Activity
- Forecasting
- WIP
- Shared Reference Data

The detailed entity and attribute definitions are maintained in DOC-003 Data Dictionary.

---

## 7. Outcome

SA-004 is complete.

The solution now has sufficient logical definition to proceed into physical database design, including:

- Physical table names
- Primary and foreign keys
- SQL data types
- Nullability
- Unique constraints
- Check constraints
- Indexing
- Sample-data strategy
- SQL DDL scripts

---

## 8. Next Step

Proceed to Sprint 4 – Database Design using the approved SA-004 logical model and DOC-003 Data Dictionary.
