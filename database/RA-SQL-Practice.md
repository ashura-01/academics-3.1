---
tags: [dbms, sql, relational-algebra, practice]
created: 2026-08-11
---

# Relational Algebra & SQL Practice

## Problem 1 — Employee Selection Queries

**Table: `Employee(Name, Age, Salary)`**

| Name  | Age | Salary (USD) |
| ----- | --- | ------------- |
| Marly | 24  | 9000          |
| Lucky | 40  | 3000          |
| Mark  | 26  | 4500          |
| John  | 42  | 3900          |

### Query 1 — Age less than 30

**Relational Algebra:**
$$\sigma_{Age < 30}(Employee)$$

```sql
SELECT * FROM Employee WHERE Age < 30;
```

> [!success] Result
> Marly (24), Mark (26)

---

### Query 2 — Age < 25 **and** Salary > 5000

**Relational Algebra:**
$$\sigma_{Age < 25 \,\land\, Salary > 5000}(Employee)$$
> `∧` = AND — both conditions must be true

```sql
SELECT * FROM Employee WHERE Age < 25 AND Salary > 5000;
```

> [!success] Result
> Marly (24, 9000) — the only one under 25 with salary above 5000.

---

### Query 3 — Age below 25 **or** above 40

**Relational Algebra:**
$$\sigma_{Age < 25 \,\lor\, Age > 40}(Employee)$$
> `∨` = OR — at least one condition true

```sql
SELECT * FROM Employee WHERE Age < 25 OR Age > 40;
```

> [!success] Result
> Marly (24 — below 25), John (42 — above 40).
> *(Lucky is exactly 40, not "higher than 40," so he's excluded.)*

---

### Query 4 — Name starts with "mar"

**Relational Algebra:**
$$\sigma_{Name = \text{"mar\%"}}(Employee)$$
> `%` is the wildcard for "anything after this prefix"

```sql
SELECT * FROM Employee WHERE Name LIKE 'mar%';
```

> [!success] Result
> Marly, Mark — both names start with "mar".

---

### 🧭 How to solve any of these, step by step

1. Find the relation → `Employee`
2. Translate the English condition into a comparison (`<, >, =`, etc.)
3. Two conditions joined by "and" → use `∧`; joined by "or" → use `∨`
4. Wrap it as `σ condition (Relation)` for algebra, or `WHERE condition` for SQL
5. Scan the table row by row and keep the ones that satisfy the condition

---

## Problem 2 — Student / Course / Enroll Joins

For each query below: **Relational Algebra → MS SQL → Result Table → Reasoning**

### Given Data

**Student**

| sid | name  | dept |
| --- | ----- | ---- |
| 1   | Fahim | CSE  |
| 2   | Jahir | EEE  |
| 3   | Rafi  | BBA  |
| 4   | Nila  | CSE  |
| 5   | Abir  | CSE  |

**Course**

| cid | course_title     | credit |
| --- | ---------------- | ------ |
| C1  | Database         | 3      |
| C2  | Algorithms       | 3      |
| C3  | Machine Learning | 2      |

**Enroll**

| sid | cid | grade |
| --- | --- | ----- |
| 1   | C1  | 3.75  |
| 1   | C2  | 3.25  |
| 2   | C1  | 3.00  |
| 4   | C1  | 3.50  |
| 4   | C2  | 3.75  |
| 4   | C3  | 3.00  |

---

### (a) All students, including those NOT enrolled

> [!info] Understanding
> "Including students who are NOT enrolled" → keep **all** Student rows → **LEFT OUTER JOIN**

**Relational Algebra:**
$$\pi_{sid,\,name,\,dept,\,cid,\,course\_title}\Big((Student \; \text{⟕}_{Student.sid=Enroll.sid} \; Enroll)\; \text{⟕}_{Enroll.cid=Course.cid} \; Course\Big)$$

```sql
SELECT s.sid, s.name, s.dept, e.cid, c.course_title
FROM Student s
LEFT JOIN Enroll e
    ON s.sid = e.sid
LEFT JOIN Course c
    ON e.cid = c.cid;
```

**Result:**

| sid | name  | dept | cid  | course_title     |
| --- | ----- | ---- | ---- | ---------------- |
| 1   | Fahim | CSE  | C1   | Database         |
| 1   | Fahim | CSE  | C2   | Algorithms       |
| 2   | Jahir | EEE  | C1   | Database         |
| 3   | Rafi  | BBA  | NULL | NULL              |
| 4   | Nila  | CSE  | C1   | Database         |
| 4   | Nila  | CSE  | C2   | Algorithms       |
| 4   | Nila  | CSE  | C3   | Machine Learning |
| 5   | Abir  | CSE  | NULL | NULL              |

---

### (b) Total CSE students enrolled in each course

> [!info] Understanding
> Only CSE students → group by course → count students

**Relational Algebra:**
$$\mathcal{G}_{cid,\,count(sid)}\Big(\sigma_{dept='CSE'}(Student \bowtie Enroll)\Big)$$

To show course names:
$$\mathcal{G}_{course\_title,\,count(sid)}\Big((\sigma_{dept='CSE'}(Student) \bowtie Enroll) \bowtie Course\Big)$$

```sql
SELECT c.course_title,
       COUNT(*) AS total_students
FROM Student s
JOIN Enroll e
    ON s.sid = e.sid
JOIN Course c
    ON e.cid = c.cid
WHERE s.dept = 'CSE'
GROUP BY c.course_title;
```

**Reasoning:** CSE students → Fahim (C1, C2), Nila (C1, C2, C3), Abir (none)

**Result:**

| course_title     | total_students |
| ---------------- | -------------- |
| Database         | 2              |
| Algorithms       | 2              |
| Machine Learning | 1              |

---

### (c) CSE students enrolled in at least one course

> [!info] Understanding
> "Enrolled in at least one course" → only matching records → **INNER JOIN**

**Relational Algebra:**
$$\pi_{sid,\,name,\,cid,\,course\_title,\,grade}\Big((\sigma_{dept='CSE'}(Student) \bowtie Enroll) \bowtie Course\Big)$$

```sql
SELECT s.sid,
       s.name,
       e.cid,
       c.course_title,
       e.grade
FROM Student s
JOIN Enroll e
    ON s.sid = e.sid
JOIN Course c
    ON e.cid = c.cid
WHERE s.dept = 'CSE';
```

**Result:**

| sid | name  | cid | course_title     | grade |
| --- | ----- | --- | ---------------- | ----- |
| 1   | Fahim | C1  | Database         | 3.75  |
| 1   | Fahim | C2  | Algorithms       | 3.25  |
| 4   | Nila  | C1  | Database         | 3.50  |
| 4   | Nila  | C2  | Algorithms       | 3.75  |
| 4   | Nila  | C3  | Machine Learning | 3.00  |

---

### (d) Average grade of students in each department

> [!info] Understanding
> Student ⋈ Enroll → group by dept → AVG(grade)

**Relational Algebra:**
$$\mathcal{G}_{dept,\,AVG(grade)}(Student \bowtie Enroll)$$

```sql
SELECT s.dept,
       AVG(e.grade) AS avg_grade
FROM Student s
JOIN Enroll e
    ON s.sid = e.sid
GROUP BY s.dept;
```

**Reasoning:**

- **CSE** grades: 3.75, 3.25, 3.50, 3.75, 3.00 → average = $\dfrac{3.75+3.25+3.50+3.75+3.00}{5} = 3.45$
- **EEE** grades: 3.00 → average = 3.00
- **BBA**: no enrolled students → no row appears

**Result:**

| dept | avg_grade |
| ---- | --------- |
| CSE  | 3.45      |
| EEE  | 3.00      |

---

## 🧭 Cheat Sheet — Identifying the Join from Wording

| Question wording                            | Join / Operator                         |
| --------------------------------------------- | ---------------------------------------- |
| "including students not enrolled"             | ⟕ LEFT JOIN                              |
| "enrolled in at least one course"             | ⋈ INNER JOIN                             |
| "all records from both tables"                | ⟗ FULL OUTER JOIN                        |
| "all courses even if nobody enrolled"         | ⟖ RIGHT JOIN (or Course LEFT JOIN Enroll) |
| "count / average / sum"                       | 𝒢 (Aggregation)                          |
| "only CSE students"                           | σ Selection                              |
| "show specific columns"                       | π Projection                             |

> [!tip]
> This is the logic examiners expect when deriving relational algebra from English questions.
