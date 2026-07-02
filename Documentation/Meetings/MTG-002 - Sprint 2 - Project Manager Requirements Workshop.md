# MTG-002 - Sprint 2 – Project Manager Requirements Workshop
# Meeting ID: MTG-002

**Project:** Construction Analytics Solution
**Date:** July 1, 2026
**Sprint:** Sprint 2 – Requirements & Database Design


## Purpose

The purpose of this workshop is to identify the information Project Managers require to effectively monitor project performance throughout the life of a construction project. The meeting focuses on business questions and operational decision-making rather than reports or database design.

## Project Context

The Construction Analytics Solution is based on the creator's experience working within a commercial construction company for approximately 30 years in roles including field operations, foreman, project management, estimating, purchasing, administration, payroll, and Vice President of Job Cost.

The solution recreates and expands upon a daily job cost dashboard initiative intended to provide Project Managers and Operations personnel with same-day visibility into project performance rather than relying solely on traditional accounting reports produced after payroll and other costs have been processed.

## Business Discovery
**Company Overview**

**Business Type**

Commercial site construction specializing in:

 -   Asphalt paving
 -   Milling
 -   Reclamation
 -   Grading
 -   New construction
 -   Overlay
 -   Patching and repairs
 -   Sealcoating
 -   Crack sealing
 -   Pavement markings
 -   Concrete walks and stairs
 -   Granite and precast curb installation
 -   Drain and catch basin work
 -   Bollards and signage
 -   Sports court repair
 -   Annual winter cleanup

Geographic Area
 -   New England (all six states)

Annual Volume
 -   1,700–2,000 projects annually
 -   $40–50 million annual revenue

Divisions
 -   Paving
 -   Patch & Repair
 -   Seal & Lines
 -   Concrete

Projects belong to one owning division but may utilize crews from multiple divisions.

Project Characteristics
 -   Projects range from:

 -   One-hour service work
 -   Multi-month reconstruction projects

Seasonal Operations

Primary construction season:
  -  April through December

Sealcoating:
   - May through November

## Current Business Process

Project Managers begin each day by reviewing:
 -   Schedule
 -   Daily work assignments
 -   Project status
 -   Labor utilization
 -   Production
 -   Emerging job cost issues
Current accounting systems do not provide timely visibility into project performance during active construction.

## Major Business Findings

**Labor**
 -   Labor represents the largest source of project cost variance.
 -   Labor productivity is considered the most important operational performance indicator.

**Cost Structure**
    Jobs are organized using:

        Job
        ↓
        Phase
        ↓
        Cost Code
        ↓
        Cost Type
        ↓
        Transactions

Primary cost types:

-    Labor
-    Burden
-    General Materials
-    Major Materials
-    Equipment
-    Miscellaneous

Primary phases include:

-    Production
-    Travel
-    Warranty Production
-    Warranty Travel

## Production

Production units vary by work activity.
    Examples include:
    -    Tons
    -    Square Feet
    -    Linear Feet
    -    Cubic Yards
    -    Gallons

Production metrics cannot be standardized across all work activities and must be associated with specific cost codes.

## Field Data Collection

    Daily operational information is collected through mJobTime.

    Foremen record:
    -    Employee labor throughout the day
    -    Equipment usage
    -    Material usage

This operational information becomes available before final accounting costs are posted.

## Initial Project Manager KPIs

Priority KPIs identified:

-    Gross Margin %
-    Budget Variance
-    Labor Dollar Variance
-    Labor Hour Variance
-    Material Quantity Variance
-    Cost by Cost Type
-    Cost by Cost Code
-    Cost by Phase
-    Daily Production
-    Daily Labor Productivity
    
## Key Business Problem

    Project Managers currently cannot accurately determine project profitability while work is in progress.

    The primary objective of the solution is to provide near real-time project performance using field-entered operational data before payroll, equipment, and accounting processes have been completed.

## Proposed Architecture Decisions
    DEC-002 (Approved)
    Design the solution around business decisions rather than traditional accounting reports.

    DEC-003 (Proposed)
    Separate operational field activity from posted accounting transactions to support same-day project analytics.

## Parking Lot

Topics for future discussion:

-    Jobs at Risk methodology
-    Forecast Cost at Completion
-    Earned Value Management
-    Productivity forecasting
-    Weather integration
-    Mobile field dashboards

## Action Items
-    Review meeting notes
-    Continue requirements discovery
-    Define remaining dashboard requirements