# SQL Transaction Isolation Levels — Simple Guide

## The Big Idea First

Imagine many people reading and writing to the same spreadsheet at the same time. Without rules, they'd step on each other's work: reading half-finished edits, or overwriting each other's changes.

**Isolation level = the rulebook** that says how much one transaction is allowed to "see" or "be bothered by" other transactions happening at the same time.

- **More strict** → safer data, but people wait longer (less speed).
- **Less strict** → faster, but you risk seeing messy/incomplete data.

Before diving in, learn these 3 problems. Every isolation level is really just "which of these problems does it allow?"

| Problem | Plain meaning |
|---|---|
| **Dirty Read** | You read data that someone else changed but hasn't saved (committed) yet. If they cancel their change, you read something that never really existed. |
| **Non-Repeatable Read** | You read a row twice in the same transaction, and it gave two different answers because someone else changed it in between. |
| **Phantom Read** | You run the same search twice in one transaction, and new rows appear (or vanish) the second time, because someone else inserted/deleted matching rows. |

---

## 1. READ UNCOMMITTED — "Trust no one, but ask nobody to wait either"

**Definition:** You can read data even if another transaction changed it and hasn't committed yet. No locks are used to protect your reads, and you're not blocked by others' locks either.

**Memory trick:** *"Uncommitted = Unfiltered gossip."* You hear the rumor before anyone confirms it's true.

**Real-life example:** You peek at a friend's essay draft while they're still typing and might delete the paragraph. You read something that may never actually get saved.

```sql
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
```

- ✅ Fastest, least blocking
- ❌ Allows **dirty reads**
- Same effect as putting `NOLOCK` on every table in your `SELECT`

---

## 2. READ COMMITTED — "Only believe confirmed news" (SQL Server's default)

**Definition:** You can only read data that has already been committed (saved for real). You will never see someone else's half-finished change. But between two reads in *your own* transaction, the data can still change.

**Memory trick:** *"Committed = Confirmed."* You'll only read the news after it's officially published — but the news can update again by the time you check the paper twice.

**Real-life example:** You check a friend's essay only after they hit "Save." But if you check again five minutes later, they might have saved new edits — so your two reads don't match (non-repeatable read).

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

- ✅ No dirty reads
- ❌ Allows **non-repeatable reads** and phantom reads
- This is the **default** in SQL Server
- Behind the scenes it has 2 modes:
  - **Locking mode** (default): uses shared locks that release quickly
  - **Snapshot mode** (if `READ_COMMITTED_SNAPSHOT = ON`, default in Azure SQL): uses row versioning instead of locks — no blocking, but more memory used to keep versions

---

## 3. REPEATABLE READ — "Once I read it, don't touch it"

**Definition:** Once your transaction reads a row, no one else can **change** that row until you finish. But someone *can still insert brand new rows* that match your search.

**Memory trick:** *"Repeatable = Reserved."* You've reserved every row you touched — nobody can edit them until you're done. But new rows can still sneak in.

**Real-life example:** You're reviewing a list of students in a class. No one can change any student's grade while you're reviewing. But a new student can still enroll and appear if you run your query again (phantom read).

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

- ✅ No dirty reads, no non-repeatable reads
- ❌ Allows **phantom reads**
- Locks are held until the *whole transaction* ends → more waiting than READ COMMITTED

---

## 4. SNAPSHOT — "Give me a photograph of the data, frozen in time"

**Definition:** At the moment your transaction starts, SQL Server takes a mental "snapshot" (photo) of the committed data. You keep seeing that same photo the whole transaction, no matter what others do in the meantime. You don't need locks to read, and you don't block writers either.

**Memory trick:** *"Snapshot = Selfie."* You took a selfie of the data the second you started. Even if the room changes behind you, your photo stays the same.

**Real-life example:** You print out today's bank statement. Even if new transactions happen after you printed it, your paper still shows the old numbers — a frozen moment in time.

```sql
-- Must enable this once per database first:
ALTER DATABASE YourDB SET ALLOW_SNAPSHOT_ISOLATION ON;

SET TRANSACTION ISOLATION LEVEL SNAPSHOT;
BEGIN TRANSACTION;
    SELECT * FROM Accounts;  -- sees data as of transaction start
COMMIT TRANSACTION;
```

- ✅ No dirty reads, no non-repeatable reads, no phantom reads
- ✅ Readers never block writers, writers never block readers
- ❗ Needs `ALLOW_SNAPSHOT_ISOLATION` turned ON for the database first
- ❗ You can't switch *into* SNAPSHOT mid-transaction from another level (it aborts) — but you *can* switch *out* of it

---

## 5. SERIALIZABLE — "Act like we're the only ones in the room"

**Definition:** The strictest level. Nobody can change data you've read, **and** nobody can insert new rows that would match your search, until your transaction is done. It's as if all transactions ran one after another (serially), never at the same time.

**Memory trick:** *"Serializable = Single-file line."* Everyone must wait their turn — no overlapping.

**Real-life example:** You're counting all students with grade "A" in a class. While you're counting, nobody can add a new "A" student, remove one, or change anyone's grade to/from "A" — the classroom is completely frozen for that range until you finish.

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

- ✅ Blocks all 3 problems: dirty reads, non-repeatable reads, phantom reads
- ❌ Slowest, most blocking (locks entire *ranges* of key values, not just rows)
- Same effect as putting `HOLDLOCK` on every table in your `SELECT`

---

## Quick Comparison Table

| Level | Dirty Read | Non-Repeatable Read | Phantom Read | Uses Locks to Read? | Speed |
|---|:---:|:---:|:---:|:---:|:---:|
| READ UNCOMMITTED | ✅ Allowed | ✅ Allowed | ✅ Allowed | No | Fastest |
| READ COMMITTED (default) | ❌ Blocked | ✅ Allowed | ✅ Allowed | Yes (or row versioning) | Fast |
| REPEATABLE READ | ❌ Blocked | ❌ Blocked | ✅ Allowed | Yes | Slower |
| SNAPSHOT | ❌ Blocked | ❌ Blocked | ❌ Blocked | No (versioning) | Fast, but more memory |
| SERIALIZABLE | ❌ Blocked | ❌ Blocked | ❌ Blocked | Yes (range locks) | Slowest |

✅ = the problem **can** happen · ❌ = the problem is **prevented**

**Easy way to remember the order (least → most strict):**
> Uncommitted → Committed → Repeatable Read → Serializable
> *(and Snapshot sits off to the side — strict like Serializable, but fast like Snapshot-selfies, using versions instead of locks)*

---

## How to Set It (Syntax Recap)

```sql
SET TRANSACTION ISOLATION LEVEL
    { READ UNCOMMITTED
    | READ COMMITTED
    | REPEATABLE READ
    | SNAPSHOT
    | SERIALIZABLE
    }
```

## Full Worked Example (from Microsoft Docs)

```sql
USE AdventureWorks2022;
GO

SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
GO

BEGIN TRANSACTION;
GO

SELECT * FROM HumanResources.EmployeePayHistory;
GO

SELECT * FROM HumanResources.Department;
GO

COMMIT TRANSACTION;
GO
```
**What's happening:** Because we set `REPEATABLE READ`, SQL Server holds shared locks on every row read by both `SELECT`s until `COMMIT TRANSACTION` runs. No one else can change those rows in the meantime.

---

## A Few Extra Facts Worth Remembering

- Only **one** isolation level is active per connection at a time, and it stays until you change it.
- You can switch levels **mid-transaction** — except you **cannot switch into `SNAPSHOT`** mid-transaction (it aborts the transaction). You *can* switch out of `SNAPSHOT` into something else, though.
- Isolation level only affects **reads**. Any transaction that **modifies** data always takes an exclusive lock on those rows, no matter the isolation level.
- Setting the isolation level inside a stored procedure or trigger is **temporary** — it resets back to the caller's level once the procedure/trigger finishes.
