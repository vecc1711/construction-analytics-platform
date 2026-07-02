MTG-003
Operations Discovery Workshop
Meeting ID: MTG-003
Project: Construction Analytics Solution
Sprint: Sprint 2 – Business Discovery
Status: Complete
Date: July 2, 2026


Understand how Operations manages daily field production and identify the operational information required to maximize productivity, support Project Managers, and improve overall company profitability.

Executive Summary

Operations is primarily concerned with maximizing productive field hours while minimizing operational disruptions. Unlike Project Managers, who focus on individual project financial performance, Operations focuses on effectively utilizing labor, equipment, materials, and time to complete scheduled work safely and efficiently.

The workshop identified labor productivity as the primary operational driver of profitability and established that production is measured differently depending on the work activity being performed. The discovery also highlighted the importance of comparing expected production against actual production throughout the workday.

Business Discovery
Daily Operational Priorities

Before crews leave the yard each morning, Operations confirms:
    Employee attendance
    Equipment readiness
    Material availability
    Weather conditions
    Project delays
    Plant availability
    Delivery schedules

The schedule serves as the operational plan for the day.

Common Operational Risks

Primary causes of lost productivity include:
    Weather
    Equipment breakdowns
    Traffic
    Material shortages
    Plant delays
    Vendor delays
    Scope changes
    Incorrect equipment
    Truck availability
    Daily Operational Decisions

Operations regularly makes decisions involving:
    Crew assignments
    Equipment substitutions
    Weather delays
    Project scheduling changes
    Resource balancing
    DOT Hours-of-Service compliance
    Definition of Operational Success

A successful day is characterized by:
    Crews completing planned work
    Meeting or exceeding labor estimates
    Meeting production targets
    Material usage remaining within approximately ±10% of estimate
    Completing work on or ahead of schedule
    Operational KPIs

The following operational metrics were identified:
    Tons Installed
    Labor Hours
    Shift Length
    Crew Size
    Units per Hour
    Production Rate
    Material Usage
    Equipment Utilization
    Production Standards

Expected production rates are developed using:
    Estimator experience
    Historical production
    Superintendent knowledge
These production standards provide the baseline for measuring field performance.

Foreman Information

Foremen receive:
    Complete project budget
    Original estimate
    Budgeted labor hours
Providing complete project information enables foremen to understand the overall objectives of the project and make better field decisions.

Operational Adjustments

Operational adjustments are influenced by:
    Remaining scheduled work
    Availability of crews
    Availability of equipment
    DOT Hours-of-Service limitations
    Weather conditions
    Probability of completing the work with additional resources

Major Findings

Finding 001
    Labor productivity is the single largest driver of project profitability.

Finding 002
    Production cannot be measured using a single unit of measure.
    Production metrics vary by work activity.
        Examples include:
            Tons
            Square Feet
            Linear Feet
            Cubic Yards
            Gallons

Finding 003
    Operational performance should compare expected production against actual production.

Finding 004
    Operations manages resources rather than individual projects.

    Resources include:
        Labor
        Equipment
        Materials
        Time
        Trucks
        Weather
        Asphalt Plants

Finding 005
    Operations requires near real-time field information rather than traditional accounting information.

Candidate Functional Requirements
ID	    Requirement
FR-021	The solution shall display current production by project.
FR-022	The solution shall compare expected production with actual production.
FR-023	The solution shall measure labor productivity throughout the workday.
FR-024	The solution shall identify operational delays affecting production.
FR-025	The solution shall support production reporting using multiple units of measure.
FR-026	The solution shall display resource utilization.

Candidate Architecture Decisions
DEC-003 (Candidate)
    Separate operational field activity from posted accounting information.

DEC-004 (Candidate)
    Production standards shall be stored independently from actual production transactions.

DEC-005 (Candidate)
    Production units shall be associated with Cost Codes rather than globally standardized.

Open Questions
    How should expected production standards be maintained?
    How should weather impacts be measured?
    Should production standards vary by crew composition?
    Should historical production be automatically incorporated into production standards?

Parking Lot
Version 2 considerations:
    Mobile Operations Dashboard
    Crew benchmarking
    Predictive production forecasting
    Weather integration
    Equipment telematics
    AI production recommendations

Action Items
    Task
        Review findings
        Incorporate discoveries into conceptual data model
        Continue Business Discovery