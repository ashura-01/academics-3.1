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

1. Concurrent Execution

- **Easy Explanation:** Instead of forcing transactions to wait in a single-file line, the database system processes multiple transactions simultaneously12. Imagine a restaurant kitchen where the chef prepares a salad while a steak is baking in the oven, rather than making the next customer wait until the first customer's entire meal is fully cooked and served.
- **Why Databases Do It:**
    - **Higher Throughput & Resource Utilization:** The CPU and hard disks operate in parallel2. While one transaction waits for a disk read/write operation to complete, the CPU can execute instructions for another transaction, maximizing overall work completed per second2.
    - **Reduced Waiting Time:** Short, quick queries do not get stuck sitting behind long, heavy transactions, which drastically lowers average response time3.

---

2. Schedule

- **Easy Explanation:** A **schedule** is the step-by-step master timeline showing the exact chronological sequence in which instructions from concurrent transactions are executed45.
- **How It Works:** When multiple transactions run together, the operating system may switch between them6. A schedule simply records this interleaved timeline—showing whether $T_1$ read Account A, then $T_2$ read Account A, then $T_1$ wrote Account A, and so on47.

---

3. Serializability

- **Easy Explanation:** Mixing the steps of concurrent transactions can sometimes cause errors or corrupted data89. **Serializability** is the ultimate safety standard1011: a concurrent schedule is **serializable** if its final outcome is guaranteed to be identical to running those transactions strictly one after another (serially)1012.
- **The Goal:** You get the fast performance of multi-tasking, but with the exact same reliable correctness as running them one by one1013.

---

4. Conflict Serializability & The Precedence Graph

**What is Conflict Serializability?**

Instead of analyzing complex program logic, the database checks safety by looking at **conflicting instructions**1415:

- **What counts as a conflict?** Two operations conflict if they belong to **different transactions**, target the **exact same data item**, and **at least one is a** **write** **operation**1516.
- **Swapping non-conflicting steps:** If two adjacent steps in a schedule do **not** conflict (e.g., two `read` operations, or operations on completely different account balances), you can swap their order without changing the final result1517.
- **The Test:** If you can turn an interleaved schedule into a step-by-step serial schedule just by swapping non-conflicting operations, the schedule is **conflict serializable**1819.
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