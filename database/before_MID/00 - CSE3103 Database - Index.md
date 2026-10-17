---
tags: [database, CSE3103, MOC]
course: CSE3103 - Database
---

# CSE3103 — Database — Index

> [!info] Map of Content
> Central hub for all CSE3103 Database notes, generated from the lecture slide PDFs. Click any link to jump to that topic.

## Notes

1. [[00 - Introduction to DBMS]] — What a DB/DBMS is, drawbacks of file systems, abstraction levels, DDL vs DML, database architectures (centralized, client-server, parallel, distributed)
2. [[01 - ER Modeling]] — Entities, relationships, attributes, ERD symbols, full worked ERD, keys, cardinality & participation constraints, weak entities, degree of relationships
3. [[02 - Relational Algebra - Unary Operations]] — Selection (σ), Projection (π), Rename (ρ)
4. [[03 - Relational Algebra - Binary Operations]] — Union-compatibility, Union, Intersection, Difference, Cartesian Product
5. [[04 - Joins in Relational Algebra]] — Cartesian Product vs Join, **Natural Join**, **Theta Join**, **Left/Right/Full Outer Join** — with full before/after table examples
6. [[05 - Aggregate Functions and Assignment Operation]] — Aggregate functions (SUM, COUNT, AVG, group-by), the Assignment operation

## Suggested Study Order

```
Intro to DBMS → ER Modeling → RA: Unary Ops → RA: Binary Ops → Joins → Aggregate Functions
```

## Quick Symbol Cheat-Sheet

| Symbol | Name | Meaning |
|---|---|---|
| σ | Sigma | Selection (filter rows) |
| π | Pi | Projection (pick columns) |
| ρ | Rho | Rename |
| ∪ | Union | Combine (distinct rows) |
| ∩ | Intersection | Common rows |
| − | Difference | Rows in R not in S |
| × | Cartesian Product | All row combinations |
| ⋈ | Natural/Theta Join | Matched combination |
| ⟕ | Left Outer Join | All of left + matches |
| ⟖ | Right Outer Join | All of right + matches |
| ⟗ | Full Outer Join | All of both + matches |
| 𝒢 | Aggregation | Group-by / aggregate function |
| ← | Assignment | Store result in a temp relation |

## Attachments
All diagrams referenced by these notes live in [[attachments]] (image folder) — database architecture diagrams, the View of Data diagram, ERD symbols, and the full worked university ERD.
