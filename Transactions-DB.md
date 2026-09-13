---
course: CSE3103 - Database
tags:
  - Quiz2
---

### The Main Scenario: Transferring $50 from Account A to Account B

Imagine you are using a bank app to transfer **$50** from your **Checking Account (Account A)** to your **Savings Account (Account B)**.

Before the transfer, Account A has **$1,000** and Account B has **$2,000**.

In the database, this single $50 transfer is split into 6 basic step-by-step operations:

1. **`read(A)`**: Look up Account A's balance ($1,000).
2. **`A := A - 50`**: Subtract $50 in temporary memory ($950).
3. **`write(A)`**: Update Account A's balance ($950) in the database.
4. **`read(B)`**: Look up Account B's balance ($2,000).
5. **`B := B + 50`**: Add $50 in temporary memory ($2,050).
6. **`write(B)`**: Update Account B's balance ($2,050) in the database.

---

### 1. What is a Transaction?

A **transaction** is a collection of database operations that forms a single logical unit of work. Even though it takes 6 separate steps behind the scenes, from your perspective as a user, it is one single, indivisible action.

---

### 2. The ACID Properties (with Examples)

#### **A – Atomicity ("All or Nothing")**

- **The Concept:** Either **all** steps of the transaction succeed, or **none** of them take effect.
- **The Scenario:** Imagine the system crashes or loses power right after Step 3 (`write(A)`), before Step 6 (`write(B)`) can run. Account A now shows $950, but Account B still shows $2,000—meaning $50 vanished!
- **How it works:** Atomicity guarantees that if a crash happens midway, the database system's **recovery manager** uses a recorded **log file** to undo (roll back) the $50 deduction, restoring Account A back to $1,000 as if the transfer never started.

#### **C – Consistency ("Preserving Rules and Totals")**

- **The Concept:** Executing a transaction alone must transform the database from one valid state to another, preserving overall business rules.
- **The Scenario:** Before the transfer, the total balance across both accounts is 
  **$1,000 + $2,000 =** **$3,000**. After a successful transfer, **$950 + $2,050 =** **$3,000**.
- **How it works:** Consistency ensures that money is neither created nor destroyed out of nowhere. Writing logic to preserve this application consistency is the programmer's responsibility.

#### **I – Isolation ("No Interference from Concurrent Users")**

- **The Concept:** Concurrently running transactions must operate without interfering with one another, so each transaction feels like it is running alone.
- **The Scenario:** Suppose you transfer *$50* while an automated bank audit program calculates your total wealth. If the audit reads Account A after **step 3 ($950)** and Account B before **step 6 ($2,000)**, it sees a total of **$2,950**—an incorrect, inconsistent value.
- **How it works:** The **concurrency-control system** isolates active transactions so intermediate, uncommitted changes are hidden from other users.

#### **D – Durability ("Permanent Results")**

- **The Concept:** Once a transaction successfully completes (commits) and notifies you, its changes persist permanently and survive system crashes.
- **The Scenario:** You receive a confirmation screen saying _"Transfer Complete!"_ A second later, the bank server loses power.
- **How it works:** Durability ensures that because the transfer entered the committed state, updates (or reconstruction logs) were written to disk/stable storage, so the new balances ($950 and $2050) remain intact when the server reboots.

---

### 3. Transaction States (The Lifecycle)

During its run, a transaction moves through 5 states:

- **Active:** The initial state while operations are executing.
- **Partially Committed:** All code statements have executed, but final updates are still in volatile RAM memory waiting to be safely written to disk.
- **Failed:** An error or crash occurs preventing normal completion.
- **Aborted:** The recovery system undoes all partial changes, returning the database to its original state.
- **Committed:** Updates are permanently written to non-volatile disk storage.

---

### 4. Concurrent Schedules and Serializability

- **Concurrent Execution:** Database systems run multiple transactions at the same time to increase system **throughput** and reduce waiting times.
- **Schedule:** The timeline or chronological sequence showing how operations from concurrent transactions interleave.
- **Serializability:** A concurrent schedule is **serializable** if its final outcome is identical to running those transactions one after another (serially).
- **Conflict Serializability:** Operations conflict if they belong to different transactions, access the same item, and at least one is a `write`. If a schedule can be turned into a serial order by swapping non-conflicting operations, it is conflict serializable. We test this by drawing a **precedence graph**—if the graph has no cycles, the schedule is safe and conflict serializable.

---

### 5. Recoverable & Cascadeless Schedules

- **Recoverable Schedule:** If Transaction 2 (\(T_2\)) reads data written by Transaction 1 (\(T_1\)), then \(T_1\) **must commit before \(T_2\) commits**. Otherwise, if \(T_1\) fails and aborts, \(T_2\) would have committed based on fake, uncommitted data.
- **Cascadeless Schedule:** \(T_2\) is not even allowed to _read_ data written by \(T_1\) until \(T_1\) has officially committed. This avoids **cascading rollbacks**, where one transaction failing forces a chain reaction of multiple other transactions to abort.

---

### 6. SQL Isolation Levels

SQL lets developers trade strict isolation for higher system performance:

1. **Read Uncommitted:** Allows reading uncommitted ("dirty") data.
2. **Read Committed:** Allows reading only committed data (prevents dirty reads, but values can change if read twice).
3. **Repeatable Read:** Guarantees that data read once during a transaction won't be modified by another transaction until completed.
4. **Serializable:** The strictest level; guarantees fully serializable execution.

💡 Would you like to practice identifying conflict serializability using a small sample schedule, or generate a quiz to test your understanding of these concepts?