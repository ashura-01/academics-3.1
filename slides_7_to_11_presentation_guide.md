# Slides 7–11: complete explanation for presenting to your teacher

**Your part:**
- Slide 7: State controller
- Slide 8: Circuit synthesis (the PLA)
- Slide 9: The PLA program table
- Slide 10: Verification traces
- Slide 11: Worked example

**How to use this file:** read "Background you need first", then each slide in order. Each slide has *what it shows*, *what it means*, and *what to say*. The simple questions your teacher may ask are at the very end.

---

## Background you need first (2 minutes)

The divider has **two parts**:

| Part | Job | What it contains |
|---|---|---|
| **Datapath** | Holds the numbers and does the arithmetic | Registers A, Q, B, N, P and one adder/subtractor |
| **Control unit** | Decides what the datapath does on each clock tick | A 3-bit state register plus a **PLA** |

**The registers (know these):**

| Register | Holds |
|---|---|
| **B** (6 bits) | The divisor. It never changes. |
| **A** (7 bits) | The partial remainder (running leftover). It changes every pass. At the end it holds the remainder. |
| **Q** (6 bits) | The bottom 6 bits of the dividend at the start. At the end it holds the quotient. |
| **N** (1 bit) | 1 if A is negative after the last pass. It decides add or subtract. |
| **P** (3 bits) | Counts the loop passes (starts at 6). |
| **As, Bs, Qs** | Sign bits of the dividend, divisor and quotient. |

**The problem in one line:** divide a 12-bit number by a 6-bit number. The 12-bit dividend is split: the top 6 bits go in A and the bottom 6 bits go in Q.

**The algorithm in four lines (non-restoring):**
1. Shift A and Q left together.
2. If N=0, subtract B. If N=1, add B.
3. If the new A is not negative, the quotient bit is 1 and N=0. If it is negative, the quotient bit is 0 and N=1.
4. Repeat 6 times. At the end, if N=1, add B once more (the **correction**).

Quotient sign = As XOR Bs. Remainder sign = the dividend's sign.

---

## Slide 7: State controller

### What it shows
Five states "replace a longer micro-operation sequence", and a division takes about **9 clock cycles instead of about 21**.

| State | Job |
|---|---|
| **T0** | Idle, waiting for the start signal |
| **T1** | Test for overflow; enter the loop or abort |
| **T2** | One full loop pass: shift + add/subtract + quotient bit + countdown |
| **T5** | Optional final correction |
| **T6** | Finish and return to idle |

### What it means
The controller is a **finite state machine**. At any moment it is in one state, and in each state the datapath does a specific job. The slide says "five states" because we merged a longer design.

**Why T3 and T4 are missing: the answer to say if asked**
- The first design had 7 states, named **T0 to T6**: T0 idle, T1 overflow check, **T2 shift, T3 add/subtract, T4 set quotient bit and count down**, T5 correction, T6 done.
- Shifting is just wiring, and the adder gives the sign immediately, so T2, T3 and T4 can all happen in **one clock tick**. They merge into one state, T2.
- So **T3 and T4 are missing because they were merged into T2**, not forgotten.
- The names T5 and T6 were kept so the two designs are easy to compare. We did not renumber them to T3 and T4.
- The state *names* (T0, T1…) are only labels for people. The PLA uses the 3-bit *codes*: T0=000, T1=001, T2=010, T5=011, T6=100. Note T5 is 011, not 101.
- The three leftover codes (101, 110, 111) are unused, and we treat them as don't-cares.

**Short answer to say:**
- T3 and T4 were merged into T2 (shift, add/subtract and count fit in one clock tick).
- We kept T5 and T6 names for comparison.
- The PLA uses the 3-bit codes, and unused codes are don't-cares.

---|---|
| Merged (ours) | T1 (1) + T2 six times (6) + T5 (1) + T6 (1) = **9** |
| Unmerged | T1 (1) + (T2+T3+T4) six times (18) + T5 (1) + T6 (1) = **21** |

(There is also the idle cycle in T0 where the start signal is seen. It is the same in both designs.)

**The state transitions:**
- T0 → T1 when start (qd) = 1.
- T1 → T0 if overflow (V=1). T1 → T2 if no overflow.
- T2 stays in T2 until the last pass, then → T5.
- T5 → T6 (always).
- T6 → T0 (always).

### What to say
> "The controller has five states. T0 is idle and waits for start. T1 checks for overflow and either aborts or enters the loop. T2 does one complete pass in a single clock: shift, add or subtract, set the quotient bit and count down. T5 does the final correction if needed, and T6 signals done. We merged what was originally three states into T2, which brings a division from about 21 clock cycles down to about 9. That is also the first step in our low-power argument, because fewer clock cycles means fewer switching events."

---

## Slide 8: Circuit synthesis (the PLA)

### What it shows
- The PLA has **7 inputs and 9 outputs**, with **9 product terms** shared across the outputs.
- Silicon cost: **207 crosspoints versus 1,152 ROM bits**, which is about 18% (an 82% reduction).

### What it means

**What a PLA is.** Two grids of wires:
- The **AND grid** builds product terms (for example "state is 010 AND Pz=1").
- The **OR grid** combines those terms into the outputs.

You program which connections exist. Only the combinations you need are built.

**The 7 inputs:**

| Input | Meaning |
|---|---|
| G2 G1 G0 | the present state (3 wires) |
| qd | start: 1 = start |
| V | overflow: 1 = the answer won't fit |
| Pz | 1 = this is the last pass |
| N | 1 = the remainder is negative |

**The 9 outputs:**

| Output | Meaning |
|---|---|
| NG2 NG1 NG0 | the next state (3 wires, fed back to the state register) |
| T0 T1 T2 T5 T6 | five lines, one per state; exactly one is on at a time |
| CORR | 1 = do the correction (A ← A + B) |

**Important:** the 9 bits are only **control signals**. The 12-bit dividend never goes through the PLA. It lives in the datapath registers. The PLA only tells the datapath what to do.

**Where 207 and 1,152 come from:**
- PLA AND grid: 7 inputs × 2 (each input and its opposite) × 9 terms = **126** crosspoints.
- PLA OR grid: 9 terms × 9 outputs = **81** crosspoints.
- PLA total: 126 + 81 = **207**.
- ROM: it stores one word for every input combination. 2⁷ = 128 combinations × 9 bits per word = **1,152 bits**. This includes the three state codes (101, 110, 111) that never occur.
- 207 ÷ 1,152 = about 18%, so the PLA is about 82% smaller.

**Why smaller means lower power:** less hardware means less capacitance to charge and discharge, so less switching energy. It also means fewer chips and shorter wires.

**Why a PLA instead of a decoder plus gates:** the decoder (state to T-lines) and the next-state decision logic are both inside one chip. No separate decoder IC, no extra gate chips, and no wiring between them.

### What to say
> "The control logic is a PLA with 7 inputs, 9 outputs and just 9 shared product terms. The inputs are the three state bits plus four flags: start, overflow, last-pass and sign. The outputs are the next state, five state lines, and the correction signal. A ROM doing the same job would need 128 words of 9 bits, which is 1,152 bits, while the PLA needs only 207 crosspoints, about 82% less. Less hardware means less capacitance and less switching power, which supports our low-power constraint."

### Careful with
- A crosspoint and a ROM bit are not exactly the same thing. Say "roughly 5.6 times smaller".
- Don't say it removes "all delays". Say it removes the separate decoder and gate stages and the wiring between them.

---

## Slide 9: The PLA program table

### What it shows
The complete rulebook: **nine rows**, each one an AND term. State codes: T0=000, T1=001, T2=010, T5=011, T6=100. Codes 101, 110, 111 never occur (don't-cares).

### How to read a row
Read each row as "IF these inputs match, THEN these outputs go on." A "–" means that input is ignored.

| Row | Plain meaning |
|---|---|
| 1 | In T0, no start: stay in T0 |
| 2 | In T0, start: go to T1 |
| 3 | In T1, overflow (V=1): abort, back to T0 |
| 4 | In T1, no overflow (V=0): go to T2 |
| 5 | In T2, not the last pass (Pz=0): stay in T2 |
| 6 | In T2, last pass (Pz=1): go to T5 |
| 7 | In T5: go to T6 (always) |
| 8 | In T5 and N=1: also switch on CORR |
| 9 | In T6: back to T0 |

### Follow one whole division through the rows

| Step | Row that fires |
|---|---|
| Waiting | 1 |
| Start pressed | 2 |
| Overflow check passes | 4 |
| Loop passes 1–5 | 5 (five times) |
| Loop pass 6 (last) | 6 |
| Correction state | 7 (and also 8 if N=1) |
| Finished | 9 |

Row 3 is the side path: overflow, so abort without running the loop.

### The sharing trick (the banner at the bottom)
Row 8 has no next state of its own. It only adds CORR, and it fires **together with row 7**. The OR grid combines their outputs. Without this sharing we would need a separate full row, making 10 terms.

More sharing:
- NG2 is just row 7 (it is the same signal as T5).
- NG1 = rows 4 + 5 + 6.
- NG0 = rows 2 + 6.
- Row 6 feeds three outputs (NG1, NG0 and T2).

### What to say
> "This is the complete PLA program table, nine product terms. Each row is one AND term: the left side is the present state and the flags, the right side is the next state and the control lines. Rows 1 and 2 are idle and start. Row 4 passes the overflow check, and row 3 is the abort path. Row 5 keeps the machine in the loop until the last pass, and row 6 moves it on to the correction state. Rows 7 and 8 work together: row 7 always advances to done, and row 8 adds the correction signal only when the remainder is negative. That sharing is why we need nine terms and not ten."

### Be careful about Pz
Say "**Pz goes high on the sixth and final pass**". That matches the table: five passes with Pz=0, then the sixth with Pz=1, giving six passes in total. Avoid saying "Pz means the counter is zero", because the counter is only checked before it is decremented.

---

## Slide 10: Verification traces

### What it shows
Two examples, one where the final correction fires and one where it is skipped.

| | Trace 1: −2743 ÷ +45 | Trace 2: +70 ÷ +20 |
|---|---|---|
| Loop operations | 6 | 6 |
| End correction | 1 (state T5) | 0 (bypassed) |
| Non-restoring total | **7** | **6** |
| Restoring total | **8** | **10** |

Bottom line: non-restoring always costs **6 or 7** operations; restoring costs **6 to 12**.

### What it means

**"Conditional" means "only sometimes".** The correction depends on one thing: is the final remainder negative (N=1)?
- Trace 1 ends with A = −2 (negative), so the correction fires: −2 + 45 = 43.
- Trace 2 ends with A = 10 (not negative), so it is already a valid remainder and the correction is skipped.

N=1 at the end happens exactly when the **last quotient bit is 0**:
- Trace 1 quotient bits: 1,1,1,1,0,**0** → correction.
- Trace 2 quotient bits: 0,0,0,0,1,**1** → no correction.

**Where the restoring numbers come from.** Restoring costs 1 operation for a quotient bit of 1, and 2 operations for a 0 (it must add B back).
- Trace 1: four 1s and two 0s → 4×1 + 2×2 = **8**.
- Trace 2: four 0s and two 1s → 4×2 + 2×1 = **10**.

**The general rule:**
- Non-restoring: 6 loop operations, plus 1 only if the last quotient bit is 0 → always 6 or 7.
- Restoring: 12 minus the number of 1s in the quotient → between 6 and 12.
- Non-restoring is never worse than restoring. At best they tie (a quotient of 111111 costs 6 for both).

### What to say
> "We chose two traces on purpose. In trace 1, −2743 divided by 45, the loop ends with a remainder of −2, which is negative. So in state T5 the controller adds B once and gets 43. That is 6 loop operations plus 1 correction, 7 in total, against 8 for restoring. In trace 2, 70 divided by 20, the loop ends with a remainder of 10, which is already valid, so the correction is skipped. That is 6 operations against 10 for restoring. The rule is that the correction fires only when the final remainder is negative. So non-restoring always costs 6 or 7 operations, while restoring costs between 6 and 12."

---

## Slide 11: Worked example, (−2743) ÷ (+45)

### Setup (explain this first)
- 2743 in 12-bit binary is `101010 110111`.
- Top 6 bits `101010` = **42** → into A.
- Bottom 6 bits `110111` = **55** → into Q.
- B = 45.
- Signs: As = 1, Bs = 0, so Qs = 1 XOR 0 = **1** (negative quotient).
- Overflow check: is 42 ≥ 45? No → continue. Set N=0, P=6.

### The table (as on the slide)

| Iter | A,Q before | shift-in | 2A+bit | op (N) | A after | sign | Q0 ← | Q after | new N | new P |
|---|---|---|---|---|---|---|---|---|---|---|
| 1 | 42, 55 | 1 | 85 | −B (N=0) | 40 | + | 1 | 47 | 0 | 5 |
| 2 | 40, 47 | 1 | 81 | −B (N=0) | 36 | + | 1 | 31 | 0 | 4 |
| 3 | 36, 31 | 0 | 72 | −B (N=0) | 27 | + | 1 | 63 | 0 | 3 |
| 4 | 27, 63 | 1 | 55 | −B (N=0) | 10 | + | 1 | 63 | 0 | 2 |
| 5 | 10, 63 | 1 | 21 | −B (N=0) | −24 | − | 0 | 62 | 1 | 1 |
| 6 | −24, 62 | 1 | −47 | +B (N=1) | −2 | − | 0 | 60 | 1 | 0 |

### What each column means
- **A,Q before:** the values at the start of the pass.
- **shift-in:** the top bit of Q, which slides into the bottom of A when we shift.
- **2A + bit:** shifting left doubles A, and the shift-in bit is added as the new lowest bit.
- **op (N):** if N=0, subtract B. If N=1, add B.
- **A after:** the result of the add or subtract.
- **sign:** the sign of the new A. It decides the quotient bit.
- **Q0 ←:** the new quotient bit, shifted into the bottom of Q. Not negative → 1, negative → 0.
- **Q after:** Q shifted left with the new bit added. Final Q = 60.
- **new N:** 1 if the new A is negative. It decides add or subtract next pass.
- **new P:** the counter counts down by 1 each pass.

### Walk through row 1 (use this as your example)
Start A=42, Q=55 (`110111`). The top bit of Q is 1, so shift-in = 1. Shifting gives 2×42 + 1 = **85**. N=0, so subtract: 85 − 45 = **40**. That is not negative, so the quotient bit is **1** and N stays 0. Q shifts left and takes the new bit, becoming 47. The counter drops from 6 to 5.

### The key rows
- **Rows 1–4:** A stays positive, every quotient bit is 1, N stays 0.
- **Row 5:** 21 − 45 = **−24**. Negative, so quotient bit = 0 and N becomes 1. The divisor did not fit.
- **Row 6:** N=1, so we **add**: 2×(−24) + 1 = −47, then −47 + 45 = **−2**. Still negative, so quotient bit = 0, N stays 1.

### Correction and result
- The loop ends with A = −2 and N=1, so state T5 fires CORR: −2 + 45 = **43**.
- Q = `111100` = **60**.
- Quotient sign = 1 → **−60**.
- Remainder 43 takes the dividend's sign → **−43**.
- **Check:** 45 × 60 + 43 = 2700 + 43 = 2743 ✓.

### Which controller states ran
T0 (idle) → T1 (overflow check passed) → T2 × 6 (the six rows above) → T5 (N=1, so CORR) → T6 (done) → T0.

### What to say
> "Here is the first trace in detail. We start with 2743, which is 101010 110111 in binary. The top six bits, 42, go into A, and the bottom six bits, 55, go into Q. The divisor B is 45, and the quotient sign is 1 XOR 0 = 1. Overflow check: 42 is less than 45, so we proceed.
>
> In pass 1 we shift: 2 times 42 plus the shifted-in bit 1 gives 85. N is 0, so we subtract 45 and get 40. That is positive, so the quotient bit is 1. Passes 2 to 4 are the same and A stays positive. In pass 5, 21 minus 45 is minus 24, which is negative, so the quotient bit is 0 and N becomes 1. In pass 6, N is 1 so we add instead: minus 47 plus 45 gives minus 2.
>
> The loop ends with a negative remainder, so state T5 fires the correction, adding 45 to get 43. The quotient is 111100, which is 60. The signs give a quotient of minus 60 and a remainder of minus 43. Check: 45 times 60 plus 43 is 2743."

### Why add in pass 6?
The previous remainder was negative (N=1). Adding B moves A back toward zero. Subtracting would make it even more negative.

### A is only 7 bits, so how can 85 fit?
The adder works modulo 128, so the shifted value can wrap around. The result after the add or subtract always lies between −B and +B, so it fits in 7 bits, and the answer is still correct.

---

# Presentation script (bullet points, slides 7 to 11)

Say each bullet in your own words. *[Italics]* = an action. About 5 minutes in total.

### Slide 7: State controller
- Five states: T0 idle, T1 overflow check, T2 loop, T5 correction, T6 done.
- T0: wait for start.
- T1: overflow means abort; no overflow means enter the loop.
- T2: one full pass in one clock (shift, add/subtract, quotient bit, countdown). Stays here 6 times.
- T5: correction, only if the remainder is negative.
- T6: answer ready, back to idle.
- Result: about 9 clock cycles instead of about 21.
- Fewer cycles means less switching, so lower power.
- *[If asked about T3 and T4]*
  - T3 and T4 were merged into T2 (one clock tick can do all three jobs).
    - PLA uses the 3-bit codes, not the names.

### Slide 8: PLA
- PLA = AND grid, then OR grid.
- 7 inputs: 3 state bits + start, overflow, last-pass, sign.
- 9 outputs: 3 next-state bits + 5 state lines + correction.
- The 9 bits are control signals only. The 12-bit data stays in the registers.
- 9 product terms, shared between outputs.
- ROM: 128 words x 9 bits = 1,152 bits.
- PLA: 126 + 81 = 207 crosspoints, about 82% less.
- Less hardware means less power.

### Slide 9: PLA table
- Each row = one product term (IF inputs match, THEN outputs).
- Row 1: idle, no start, stay.
- Row 2: start, go to T1.
- Row 3: overflow, abort.
- Row 4: no overflow, go to T2.
- Row 5: not last pass, stay in T2.
- Row 6: last pass, go to T5.
- Row 7: T5 always goes to T6.
- Row 8: negative remainder, also switch on correction.
- Row 9: done, back to idle.
- Rows 7 and 8 share, so 9 terms, not 10.

### Slide 10: Two traces
- Trace 1: -2743 / 45.
  - Remainder ends at -2 (negative), so correction fires.
  - 6 + 1 = 7 operations (restoring: 8).
- Trace 2: 70 / 20.
  - Remainder ends at 10, so correction skipped.
  - 6 operations (restoring: 10).
- Correction is conditional: only when the last quotient bit is 0.
- Non-restoring: always 6 or 7. Restoring: 6 to 12.

### Slide 11: Worked example
- Setup:
  - 2743 = 101010 110111, so A = 42 and Q = 55.
  - B = 45.
  - Quotient sign = 1 XOR 0 = 1 (negative).
  - 42 < 45, so no overflow.
- Pass 1: 2x42 + 1 = 85, minus 45 = 40, bit 1.
- Passes 2 to 4: stays positive, bits 1.
- Pass 5: 21 - 45 = -24, negative, bit 0, N = 1.
- Pass 6: N = 1 so add: -47 + 45 = -2, bit 0.
- Correction: -2 + 45 = 43.
- Result:
  - Q = 111100 = 60, so quotient -60.
  - Remainder -43 (takes the dividend's sign).
- Check: 45 x 60 + 43 = 2743.
- *[End: "Thank you, happy to take questions."]*

---

# Simple questions your teacher may ask (with short answers)

**Q0. Where are T3 and T4?**
T3 and T4 were merged into T2: our first design had seven states (T0 to T6), and shift, add-or-subtract, and set-bit-and-count can all happen in one clock tick. We kept the names T5 and T6 for comparison. The PLA uses the 3-bit codes, not the names, and the unused codes 101, 110, 111 are don't-cares.

**Q1. What does your controller do?**
It tells the datapath what to do on each clock tick. It does not do any arithmetic itself.

**Q2. How many states does it have, and what are they?**
Five: T0 idle, T1 overflow check, T2 one loop pass, T5 correction, T6 done.

**Q3. Where are T3 and T4?**
They were in the original 7-state design (add/subtract, and quotient bit plus countdown). We merged them into T2 because they can all happen in a single clock tick. We kept the names T5 and T6.

**Q4. Why merge the states?**
Fewer clock cycles (9 instead of 21) means less switching and less power. The results are identical.

**Q5. What is a PLA?**
A programmable logic chip made of an AND grid and an OR grid. It turns the present state and the flags into the next state and the control signals.

**Q6. What are the 7 inputs and 9 outputs?**
Inputs: three state bits, plus qd, V, Pz and N. Outputs: three next-state bits, five state lines T0, T1, T2, T5, T6, and CORR.

**Q7. What is a product term?**
One AND gate in the PLA, for example "state is 010 AND Pz=1". Each row in the table is one product term.

**Q8. Why only 9 product terms?**
Terms are shared between outputs. For example, row 6 feeds three outputs, and row 8 rides on row 7 instead of needing a full row of its own.

**Q9. How does a PLA compare with a ROM?**
A ROM stores a word for every input combination: 128 × 9 = 1,152 bits. Our PLA needs 207 crosspoints (126 in the AND grid and 81 in the OR grid), about 82% less.

**Q10. Why does a PLA save ICs and wires?**
The state decoder and the next-state logic are inside one chip, so there is no separate decoder IC, no extra gate chips, and no wiring between them.

**Q11. What is the difference between the 9 outputs and the 9 product terms?**
The 9 outputs are the columns on the right (the signals the PLA produces). The 9 product terms are the rows. It is a coincidence that both are 9.

**Q12. Does the 12-bit dividend go through the PLA?**
No. It is held in the datapath registers A and Q. The PLA only sends control signals.

**Q13. How do six operations happen with only a few control bits?**
The controller stays in state T2 for six clock ticks. Each tick, the T2 line tells the datapath to do one full pass. The counter P counts the passes.

**Q14. How does the machine know when the loop is finished?**
The counter P raises the Pz flag on the sixth pass, and the PLA then moves from T2 to T5 (row 6).

**Q15. What is the correction step?**
One extra add of B in state T5, done only if the final remainder is negative. It turns the negative remainder into a valid one.

**Q16. When does the correction happen?**
Only when N=1 at the end of the loop, which is exactly when the last quotient bit is 0.

**Q17. Why are there two traces on slide 10?**
One needs the correction (−2743 ÷ 45) and one does not (70 ÷ 20). Together they show the correction is conditional.

**Q18. How many ALU operations does non-restoring use?**
Six in the loop, plus one correction only if needed: always 6 or 7.

**Q19. How many does restoring use?**
12 minus the number of 1s in the quotient, so between 6 and 12.

**Q20. Is non-restoring always faster?**
It is never slower. In the best case for restoring (a quotient of all 1s) both cost 6.

**Q21. What is N for?**
It stores whether A was negative after the last pass. N=0 means subtract next, N=1 means add next.

**Q22. How is the quotient bit decided?**
By the sign of the new A. Not negative means 1, negative means 0.

**Q23. Why is A 7 bits and B only 6?**
A can hold negative values between −B and +B, so it needs an extra sign bit. B is a positive number.

**Q24. How is the overflow detected?**
Before the loop we compare the top 6 bits of the dividend (the initial A) with B. If A ≥ B the quotient would need more than 6 bits, so V=1 and the division aborts. This also catches divide by zero.

**Q25. How are the signs handled?**
We divide the magnitudes. The quotient sign is the XOR of the two signs, and the remainder takes the dividend's sign. For −2743 ÷ +45: quotient −60, remainder −43.

**Q26. How do you know the answer is right?**
45 × 60 + 43 = 2743, so quotient times divisor plus remainder gives back the dividend.

**Q27. What is left to do?**
The design is verified on paper with two hand traces. The next step is to simulate it in Proteus and compare the results.

**Q28. Which numbers should I remember?**
- 5 states, 9 cycles (vs 21).
- 7 × 9 × 9 PLA: 9 terms, 207 crosspoints (126 + 81), ROM 1,152 bits.
- Trace 1: A=42, Q=55, B=45 → quotient −60, remainder −43, 7 operations (restoring 8).
- Trace 2: 70 ÷ 20 → quotient 3, remainder 10, 6 operations (restoring 10).
