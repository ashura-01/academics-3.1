# problem 1

Using your **Employee(Name, Age, Salary)** table:

|Name|Age|Salary (USD)|
|---|---|---|
|Marly|24|9000|
|Lucky|40|3000|
|Mark|26|4500|
|John|42|3900|

### Query 1: Employees whose age is less than 30

**Relational Algebra:**

```
σ Age < 30 (Employee)
```

**SQL:**

```sql
SELECT * FROM Employee WHERE Age < 30;
```

**Result:** Marly (24), Mark (26)

---

### Query 2: Age less than 25 **and** salary more than 5000 USD

**Relational Algebra:**

```
σ Age < 25 ∧ Salary > 5000 (Employee)
```

(∧ = AND — both conditions must be true)

**SQL:**

```sql
SELECT * FROM Employee WHERE Age < 25 AND Salary > 5000;
```

**Result:** Marly (24, 9000) → she's the only one under 25 with salary above 5000.

---

### Query 3: Age below 25 **or** higher than 40

**Relational Algebra:**

```
σ Age < 25 ∨ Age > 40 (Employee)
```

(∨ = OR — at least one condition true)

**SQL:**

```sql
SELECT * FROM Employee WHERE Age < 25 OR Age > 40;
```

**Result:** Marly (24 — below 25), John (42 — above 40). _(Lucky is exactly 40, not "higher than 40," so he's excluded.)_

---

### Query 4: Name starts with "mar"

**Relational Algebra:**

```
σ Name = "mar%" (Employee)
```

(% is the wildcard for "anything after this prefix")

**SQL:**

```sql
SELECT * FROM Employee WHERE Name LIKE 'mar%';
```

**Result:** Marly, Mark — both names start with "mar".

---

**How to solve any of these, step by step:**

1. Find the relation → `Employee`
2. Translate the English condition into a comparison (`<, >, =`, etc.)
3. If there are two conditions joined by "and" → use `∧`; joined by "or" → use `∨`
4. Wrap it as `σ condition (Relation)` for algebra, or `WHERE condition` for SQL
5. Scan the table row by row and keep the ones that satisfy the condition




# problem 2

I'll solve each query with:

1. **Relational Algebra**
    
2. **MS SQL**
    
3. **Result Table**
    
4. **Why that algebra/join was chosen**
    

---

# Given Data

### Student

|sid|name|dept|
|---|---|---|
|1|Fahim|CSE|
|2|Jahir|EEE|
|3|Rafi|BBA|
|4|Nila|CSE|
|5|Abir|CSE|

### Course

|cid|course_title|credit|
|---|---|---|
|C1|Database|3|
|C2|Algorithms|3|
|C3|Machine Learning|2|

### Enroll

|sid|cid|grade|
|---|---|---|
|1|C1|3.75|
|1|C2|3.25|
|2|C1|3.00|
|4|C1|3.50|
|4|C2|3.75|
|4|C3|3.00|

---

# (a) Find all students (sid, name, dept, cid, course_title) including students NOT enrolled.

## Understanding

The phrase:

> "including students who are NOT enrolled"

means:

**Keep all Student rows**

⇒ **LEFT OUTER JOIN**

---

## Relational Algebra

[  
\pi_{sid,name,dept,cid,course_title}  
\Big(  
(Student ;\text{⟕}_{Student.sid=Enroll.sid}; Enroll)  
;\text{⟕}_{Enroll.cid=Course.cid};  
Course  
\Big)  
]

---

## MS SQL

```sql
SELECT s.sid,s.name,s.dept,e.cid,c.course_title
FROM Student s
LEFT JOIN Enroll e
    ON s.sid=e.sid
LEFT JOIN Course c
    ON e.cid=c.cid;
```

---

## Result

|sid|name|dept|cid|course_title|
|---|---|---|---|---|
|1|Fahim|CSE|C1|Database|
|1|Fahim|CSE|C2|Algorithms|
|2|Jahir|EEE|C1|Database|
|3|Rafi|BBA|NULL|NULL|
|4|Nila|CSE|C1|Database|
|4|Nila|CSE|C2|Algorithms|
|4|Nila|CSE|C3|Machine Learning|
|5|Abir|CSE|NULL|NULL|

---

# (b) Find total number of CSE students enrolled in each course.

## Understanding

Need:

```text
Only CSE students
Group by Course
Count students
```

---

## Relational Algebra

[  
G_{cid,;count(sid)}  
\Big(  
\sigma_{dept='CSE'}  
(  
Student \bowtie Enroll  
)  
\Big)  
]

To show course names:

[  
G_{course_title,;count(sid)}  
\Big(  
(  
\sigma_{dept='CSE'}(Student)  
\bowtie Enroll  
)  
\bowtie Course  
\Big)  
]

---

## MS SQL

```sql
SELECT c.course_title,
       COUNT(*) AS total_students
FROM Student s
JOIN Enroll e
    ON s.sid=e.sid
JOIN Course c
    ON e.cid=c.cid
WHERE s.dept='CSE'
GROUP BY c.course_title;
```

---

## Result

CSE students:

```text
Fahim → C1,C2
Nila  → C1,C2,C3
Abir  → none
```

|course_title|total_students|
|---|---|
|Database|2|
|Algorithms|2|
|Machine Learning|1|

---

# (c) Find CSE students (sid, name, cid, course_title, grade) who are enrolled in at least one course.

## Understanding

Phrase:

> enrolled in at least one course

means:

**Only matching records**

⇒ **INNER JOIN**

---

## Relational Algebra

[  
\pi_{sid,name,cid,course_title,grade}  
\Big(  
(  
\sigma_{dept='CSE'}(Student)  
\bowtie Enroll  
)  
\bowtie Course  
\Big)  
]

---

## MS SQL

```sql
SELECT s.sid,
       s.name,
       e.cid,
       c.course_title,
       e.grade
FROM Student s
JOIN Enroll e
    ON s.sid=e.sid
JOIN Course c
    ON e.cid=c.cid
WHERE s.dept='CSE';
```

---

## Result

|sid|name|cid|course_title|grade|
|---|---|---|---|---|
|1|Fahim|C1|Database|3.75|
|1|Fahim|C2|Algorithms|3.25|
|4|Nila|C1|Database|3.50|
|4|Nila|C2|Algorithms|3.75|
|4|Nila|C3|Machine Learning|3.00|

---

# (d) Find average grade of students in each department.

## Understanding

Need:

```text
Student JOIN Enroll
Group by Dept
Average(Grade)
```

---

## Relational Algebra

[  
G_{dept,;AVG(grade)}  
(  
Student \bowtie Enroll  
)  
]

---

## MS SQL

```sql
SELECT s.dept,
       AVG(e.grade) AS avg_grade
FROM Student s
JOIN Enroll e
    ON s.sid=e.sid
GROUP BY s.dept;
```

---

## Result

### CSE

Grades:

```text
3.75
3.25
3.50
3.75
3.00
```

Average:

# [  
\frac{3.75+3.25+3.50+3.75+3.00}{5}

3.45  
]

### EEE

Grades:

```text
3.00
```

Average:

[  
3.00  
]

### BBA

No enrolled students, so no row appears.

|dept|avg_grade|
|---|---|
|CSE|3.45|
|EEE|3.00|

---

## How to identify the join from the wording

|Question wording|Join/Operator|
|---|---|
|"including students not enrolled"|⟕ LEFT JOIN|
|"enrolled in at least one course"|⋈ INNER JOIN|
|"all records from both tables"|⟗ FULL OUTER JOIN|
|"all courses even if nobody enrolled"|⟖ RIGHT JOIN (or Course LEFT JOIN Enroll)|
|"count / average / sum"|G (Aggregation)|
|"only CSE students"|σ Selection|
|"show specific columns"|π Projection|

This is the logic examiners expect you to use when deriving relational algebra from English questions.