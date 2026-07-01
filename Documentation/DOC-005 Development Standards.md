# Construction Analytics Platform
## Development Standards

## Database Naming

- Tables use lowercase with underscores
- Primary keys end with `_id`
- Foreign keys match the referenced primary key name
- Views begin with `vw_`
- Stored procedures begin with `sp_`
- Functions begin with `fn_`

## Table Naming

Master tables are plural.

Examples:

customers
employees
jobs
vendors

Transaction tables describe the business event.

Examples:

job_costs
job_budget
change_orders
daily_production

## SQL Formatting

- SQL keywords UPPERCASE
- Table names lowercase
- One column per line in SELECT statements
- Meaningful aliases

## Business Rules

- No duplicate data unless justified
- Derived values belong in views whenever possible
- Every table requires a primary key
- Every foreign key must enforce referential integrity

## Reporting Layer

Power BI and Tableau consume SQL views whenever possible.

Business logic belongs in SQL.

Visualization belongs in Power BI/Tableau.