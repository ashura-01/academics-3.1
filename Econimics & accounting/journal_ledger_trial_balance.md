# 📒 Journal → Ledger → Trial Balance
### 20-Minute Exam Crash Course

---

## 🧠 THE GOLDEN RULE (Memorize This First)

| Account Type | Increases with | Decreases with |
|---|---|---|
| **Asset** | Debit (Dr) | Credit (Cr) |
| **Expense** | Debit (Dr) | Credit (Cr) |
| **Liability** | Credit (Cr) | Debit (Dr) |
| **Owner's Equity / Capital** | Credit (Cr) | Debit (Dr) |
| **Revenue / Income** | Credit (Cr) | Debit (Dr) |

**Memory trick:** "ADE" goes Debit → Assets, Drawings, Expenses  
**"CLIP"** goes Credit → Capital, Liabilities, Income/Revenue, Provisions

---

## STEP 1 — JOURNAL (Recording Transactions)

### What is it?
A journal is the **first place** every transaction is recorded. Every entry has:
- **One or more Debit accounts**
- **One or more Credit accounts**
- **Total Debit MUST = Total Credit always**

### How to Write a Journal Entry

**Format:**
```
Date    Account Name          Ref    Debit    Credit
        Debit Account  Dr.           XXXX
            Credit Account  Cr.              XXXX
```

### Step-by-Step Method

1. **Identify** what came in and what went out
2. **Classify** each item (Asset? Expense? Liability? Revenue?)
3. **Apply the rule:** If it increases → use that account's normal side (Dr or Cr)
4. **Verify:** Total Dr = Total Cr

### Common Transaction Patterns

| Transaction | Debit | Credit |
|---|---|---|
| Owner invests cash | Cash (Asset ↑) | Capital (Equity ↑) |
| Buy asset for cash | Asset (↑) | Cash (Asset ↓) |
| Buy goods on credit | Purchase/Asset (↑) | Accounts Payable (Liability ↑) |
| Sell goods for cash | Cash (↑) | Sales Revenue (↑) |
| Sell goods on credit | Accounts Receivable (↑) | Sales Revenue (↑) |
| Pay expense | Expense (↑) | Cash (↓) |
| Pay creditor | Accounts Payable (↓) | Cash (↓) |
| Receive from debtor | Cash (↑) | Accounts Receivable (↓) |
| Owner withdraws | Drawings (↑) | Cash (↓) |
| Receive advance payment | Cash (↑) | Unearned Revenue (Liability ↑) |
| Prepay insurance | Prepaid Insurance (Asset ↑) | Cash (↓) |

### Worked Example
> **Transaction:** Purchased goods for Tk.2,000 cash and Tk.1,000 on credit from Khalid.

- "Goods purchased" = Purchase account (Expense/Asset → Dr)
- "Cash paid" = Cash goes out (Asset ↓ → Cr)
- "Credit from Khalid" = We owe Khalid (Liability ↑ → Cr)

```
Purchase  Dr.    3,000
    Cash      Cr.         2,000
    Accounts Payable  Cr. 1,000
```
✅ Dr 3,000 = Cr 3,000

---

### Special Cases to Watch

**Trade Discount:** Deduct BEFORE recording. Never show in journal.
> List price Tk.6,000 with 10% trade discount → Record only Tk.5,400

**Cash Discount (when paying):**
```
Accounts Payable  Dr.   1,000
    Cash              Cr.       975
    Discount Received Cr.        25
```

**Cash Discount (when receiving):**
```
Cash              Dr.   1,450
Discount Paid     Dr.      50
    Accounts Receivable  Cr.  1,500
```

**Goods given as free samples:**
```
Advertisement Expense  Dr.   200
    Purchase               Cr.   200
```

**Goods taken by owner:**
```
Drawings  Dr.   100
    Purchase  Cr.   100
```

**Bad Debts:**
```
Bad Debt Expense  Dr.   100
    Accounts Receivable  Cr.  100
```

**Depreciation:**
```
Depreciation Expense  Dr.   XXX
    Accumulated Depreciation  Cr.  XXX
```

---

## STEP 2 — LEDGER (T-Accounts)

### What is it?
A ledger takes all journal entries and groups them **by account**. Each account gets its own "T" shape.

### T-Account Format
```
        Account Name (e.g. Cash)
    Dr (Left)  |  Cr (Right)
   ____________|____________
    20,000     |   1,500  (rent)
     1,000     |   2,800  (salary)
     2,100     |   2,600  (payable)
   ____________|____________
   Balance Dr  |
```

### How to Post from Journal to Ledger

1. For every **Debit** in the journal → write the amount on the **LEFT** side of that account's T
2. For every **Credit** in the journal → write the amount on the **RIGHT** side of that account's T
3. After all entries, **add up both sides**
4. The **difference** is the **closing balance**
   - If Left (Dr) total > Right (Cr) total → **Debit Balance**
   - If Right (Cr) total > Left (Dr) total → **Credit Balance**

### Worked Example — Cash Account
Journal entries affecting Cash:
- April 1: Cash Dr 20,000 (investment)
- April 2: Cash Cr 1,500 (rent paid)
- April 11: Cash Dr 1,000 (advance received)
- April 20: Cash Dr 2,100 (service income)
- April 30: Cash Cr 2,800 (salary paid)
- April 30: Cash Cr 2,600 (payable paid)

```
              Cash (No. 101)
Dr            |    Cr
______________|_____________
20,000        |  1,500
 1,000        |  2,800
 2,100        |  2,600
______________|_____________
23,100        |  6,900
Balance = 23,100 - 6,900 = 16,200 (Debit Balance)
```

---

## STEP 3 — TRIAL BALANCE

### What is it?
A list of **all ledger account balances** in two columns (Dr and Cr).  
If bookkeeping is correct: **Total Dr = Total Cr**

### Trial Balance Rules

| Account Type | Goes in Debit column | Goes in Credit column |
|---|---|---|
| Assets | ✅ | |
| Expenses | ✅ | |
| Drawings | ✅ | |
| Opening Stock | ✅ | |
| Liabilities | | ✅ |
| Capital / Owner's Equity | | ✅ |
| Revenue / Income | | ✅ |
| Accumulated Depreciation | | ✅ |
| Discount Received | | ✅ |

### Two Special Stock Rules
- **Opening/Beginning Stock** → Include in Trial Balance (Debit)
- **Closing/Ending Stock** → Do NOT include in Trial Balance

### Two Special Cash/Bank Rules
- **Beginning Cash/Bank Balance** → Do NOT include
- **Ending Cash/Bank Balance** → Include (Debit)

### Trial Balance Format
```
          [Business Name]
           Trial Balance
        [Date e.g. April 30, 2020]

SL No. | Account Name        | Ref | Debit  | Credit
-------|---------------------|-----|--------|-------
  1    | Cash                |     | 16,200 |
  2    | Accounts Receivable |     |  3,100 |
  3    | Supplies            |     |  4,000 |
  4    | Accounts Payable    |     |        | 1,400
  5    | Unearned Revenue    |     |        | 1,000
  6    | Capital             |     |        |20,000
  7    | Service Revenue     |     |        | 7,200
  8    | Rent Expense        |     |  1,500 |
  9    | Salary Expense      |     |  2,800 |
-------|---------------------|-----|--------|-------
       |                     |     | 27,600 |27,600
```

---

## 🔄 Full Flow Summary

```
TRANSACTION HAPPENS
       ↓
JOURNAL (record with Dr & Cr, must balance)
       ↓
LEDGER (post to T-accounts by account name)
       ↓
TRIAL BALANCE (list closing balances, verify Dr = Cr)
```

---

## ⚡ Quick Reference: Tricky Accounts

| Account | Type | Normal Balance |
|---|---|---|
| Cash | Asset | Dr |
| Accounts Receivable | Asset | Dr |
| Prepaid Insurance | Asset | Dr |
| Supplies | Asset | Dr |
| Equipment/Furniture | Asset | Dr |
| Opening Stock | Asset | Dr |
| Drawings | Owner's (contra) | Dr |
| Discount Allowed/Paid | Expense | Dr |
| Bad Debts | Expense | Dr |
| Purchase | Expense | Dr |
| Accounts Payable | Liability | Cr |
| Notes Payable | Liability | Cr |
| Unearned Revenue | Liability | Cr |
| Bank Loan | Liability | Cr |
| Accrued Expenses | Liability | Cr |
| Capital | Equity | Cr |
| Sales Revenue | Revenue | Cr |
| Service Revenue | Revenue | Cr |
| Discount Received | Revenue | Cr |
| Accumulated Depreciation | Contra Asset | Cr |
| Reserve for Doubtful Debts | Contra Asset | Cr |

---

## 🎯 Exam Checklist

- [ ] Journal: Does every entry have Dr = Cr?
- [ ] Did you apply trade discount BEFORE recording?
- [ ] Goods as samples → Advertisement Expense Dr / Purchase Cr
- [ ] Owner withdraws goods → Drawings Dr / Purchase Cr
- [ ] Ledger: Did you post every journal line to the correct T-account side?
- [ ] Ledger: Did you calculate the closing balance correctly?
- [ ] Trial Balance: Opening stock = Debit, Closing stock = excluded
- [ ] Trial Balance: Ending cash/bank = Debit, Beginning = excluded
- [ ] Trial Balance: Total Debit = Total Credit ✅
