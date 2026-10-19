---
title: Database Normalization
tags:
  - database
date: 2026-10-19
---
# Database normalization

**Definition**: the process of organizing data in a relational DB to reduce redundancy & improve data integrity

**Advantages**:
- data is stored efficiently 
- update/insert/delete wont cause anomolies
- relationships between data are logical and consistent

**Example:**

| eid | sid | sname | cname | instructor |
| --- | --- | ----- | ----- | ---------- |
| 1   | 1   | jamal | DB    | Mr Ali     |
| 2   | 1   | jamal | AI    | Mr Khan    |
| 3   | 2   | kamal | DB    | Mr Ali     |

### Steps in DB normalization
\