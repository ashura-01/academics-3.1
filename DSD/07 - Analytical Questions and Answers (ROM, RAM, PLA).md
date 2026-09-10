---
tags: [digital-system-design, memory, rom, ram, pla, analytical-questions, lecture-5, lecture-6]
---

# Analytical Questions & Answers — Memory (ROM, RAM, PLA)

> All the "think and calculate" / "design" / "compare" style questions found in Lecture 5 (ROM & PLA) and Lecture 6 (RAM), with full worked answers and reasoning. Use this as a practice/revision sheet.

---

## Section A — ROM Sizing & Design Questions (Lecture 5)

### Q1. Design a 2048-bit ROM having word size 8 bits each.
**Answer:**
- Total bits = 2048, word size = 8 bits
- Number of words = 2048 ÷ 8 = **256 words**
- 256 = 2⁸ → need **8 address lines**
- **ROM size = 256 × 8** (i.e., 2⁸ × 8)

### Q2. Design a 2048-bit ROM having word size 4 bits each.
**Answer:**
- Total bits = 2048, word size = 4 bits
- Number of words = 2048 ÷ 4 = **512 words**
- 512 = 2⁹ → need **9 address lines**
- **ROM size = 512 × 4** (i.e., 2⁹ × 4)

> **Concept used:** A ROM of size 2ⁿ × m has (2ⁿ × m) total bits. To find n, first find number of words (total bits ÷ word size), then express it as a power of 2.

### Q3. ROM with AND-OR gates: ROM Size = 4 × 2. How many links (fuses) does it have?
**Answer:**
- ROM size 4 × 2 means: 4 words (2 address lines → 2×4 decoder) and 2 output bits.
- Maximum possible links = (number of decoder outputs) × (number of OR gates) = 4 × 2 = **8 possible links**
- Not all 8 are necessarily used — only the ones needed to implement F₁ and F₂ are kept; the rest are broken/absent.
- (For reference, in the truth table example on that slide: F₁ = 1 for addresses 01,10,11 → 3 links; F₂ = 1 for addresses 00,10 → 2 links. Total links actually used = 5 out of the 8 possible.)

### Q4. Consider a 32×8 ROM (256-bit ROM). If the input is 00000, which word is selected? If the input is 11111, which word is selected?
**Answer:**
- Input `00000` → selects **word number 0**
- Input `11111` → selects **word number 31**
- (Because 32 words need 5 address lines, and 00000=0, 11111=31 in binary.)

### Q5. For an n-input, m-output combinational circuit, what size ROM do you need?
**Answer:**
- You need a **2ⁿ × m ROM**.
- Reasoning: n inputs → 2ⁿ possible input combinations (addresses) → each needs its own stored word; m outputs → each word must be m bits wide.

### Q6. Design a combinational circuit using ROM that accepts a 3-bit number and outputs its square (as a binary number). What size ROM is needed?
**Answer:**
- 3-bit input → 2³ = **8 words** needed (inputs 0 to 7)
- Largest output value = 7² = 49 → binary `110001`, which needs **6 bits** (B₅ to B₀)
- So a full ROM would be **8 × 6**
- BUT: looking at the truth table, output bit B₁ is always 0, and B₀ always equals A₀ directly (no computation needed) — these two bits can be wired directly instead of stored in ROM.
- So the actual ROM used only needs to generate F₁, F₂, F₃, F₄ (which map to B₅, B₄, B₃, B₂) → an **8 × 4 ROM** is sufficient, with B₁ tied to 0 and B₀ wired straight from A₀.
- **Lesson (comparison/optimization point):** Always check if some output bits are constant or directly equal to an input bit — this can reduce the ROM size needed.

---

## Section B — PLA Sizing & Design Questions (Lecture 5)

### Q7. A PLA has inputs n = 3, product terms k = 3, outputs m = 2. What is the PLA size?
**Answer:**
- PLA size = n × m × k = 3 × 2 × 3 = **18**

### Q8. What is the number of programmable links in a PLA, and how is it different from ROM?
**Answer:**
- PLA links = **2n × k + k × m + m**
  - `2n × k` = both true and complemented form of every input can connect to every AND gate
  - `k × m` = every AND gate output can connect to every OR gate
  - `m` = one optional invert/no-invert control per output
- ROM links = **2ⁿ × m** (every decoder output can connect to every OR gate)
- **Comparison:** ROM's decoder produces ALL 2ⁿ minterms whether needed or not, so ROM link count grows exponentially with number of inputs (2ⁿ). PLA only creates the k product terms you actually need, so its link count grows much more slowly (linearly in n and k). **This is why PLA is more efficient/economical than ROM when there are many don't-care conditions or when n is large** — you don't waste hardware generating unused minterms.

### Q9. Implement F₁(A,B,C) = Σ(3,5,6,7) and F₂(A,B,C) = Σ(0,2,4,7) using a PLA with 3 inputs, 4 product terms, 2 outputs. Show the reasoning for term selection.
**Answer:**
- Direct SOP forms need 3 terms each (F₁ = AC+AB+BC, F₂ = B'C'+A'C'+ABC) → using both directly = 6 terms, more than the 4 allowed.
- Trick: also simplify the **complements** F₁' and F₂':
  - F₁' = B'C' + A'C' + A'B'
  - F₂' = B'C + A'C + ABC'
- Compare all four options and look for **shared product terms** between one F₁ option and one F₂ option.
- Best combination found: use **F₁' form** (so F₁ = (B'C'+A'C'+A'B')′, output inverted) together with **F₂ direct form** (B'C'+A'C'+ABC).
- These two share the terms **B'C'** and **A'C'** → total unique terms needed = B'C', A'C', A'B', ABC = exactly **4 terms**. ✅ Fits the requirement.
- **Concept:** When designing a PLA, always check both the function and its complement, and look for term-sharing across multiple outputs — this minimizes total product terms needed.

### Q10. Draw the PLA circuit diagram for the Example 2 program table (practice question from slide).
**Answer (reasoning to draw it):**
- 3 inputs (A, B, C), each with an inverter to also generate A', B', C'.
- 4 AND gates, one for each product term: B'C', A'C', A'B', ABC — wire the correct true/complement input lines into each AND gate as per the program table.
- 2 OR gates: 
  - OR gate 1 (for F₁) receives B'C', A'C', A'B' → followed by an inverter (since F₁ needs "C" = complement at output)
  - OR gate 2 (for F₂) receives B'C', A'C', ABC → output taken directly ("T" = true, no inversion)
- This matches the same layout style as the first PLA example (5×32 decoder replaced by 3-input/4-AND-gate structure).

---

## Section C — RAM Sizing & Design Questions (Lecture 6)

### Q11. A memory is organized as 1024 × 16. How many bytes is this memory module?
**Answer:**
- Total bits = 1024 × 16 = 16,384 bits
- 16,384 bits ÷ 8 (bits per byte) = **2048 bytes = 2 KB**

### Q12. Two-Dimensional Decoding: for a 1K-word memory using 5×32 row decoder and 5×32 column decoder, how many words can be selected at a time?
**Answer:**
- Only **ONE word** is selected at a time.
- Even though there are 32 rows and 32 columns (32×32 = 1024 total positions), only **one row line AND one column line** are active simultaneously — their intersection is the single memory cell/word chosen. This is exactly like coordinates (X,Y) picking one point on a grid.
- (Example shown in slide: address `01100 10100` → X=01100 (row), Y=10100 (column) → together = decimal address 404.)

### Q13. How many 64K × 8 RAM chips are needed to build a memory with 256KB capacity?
**Answer:**
- 256K ÷ 64K = **4 chips**

### Q14. How many address lines are needed to access 256K bytes total? How many of these lines connect to the address input of ALL chips?
**Answer:**
- 256K = 2^18 → need **18 total address lines**.
- Each 64K chip only has 2^16 words → needs only **16 address lines**.
- So the **16 low-order address lines (bits 0–15) connect to every chip's address input** (all 4 chips share these).

### Q15. How many address lines must be decoded for the chip-select inputs of all chips?
**Answer:**
- Remaining address lines = 18 − 16 = **2 lines** (bits 16 and 17)
- These 2 lines go into a **2-to-4 decoder**, whose 4 outputs connect to the **Chip Select (CS)** pin of each of the 4 chips — ensuring only one chip is active at any time.

### Q16. For a 64K × 8 RAM chip, how many chips are needed to build 256K × 8, and what size decoder is required?
**Answer:**
- Chips needed = 256K ÷ 64K = **4 chips**
- Decoder required = **2-to-4 decoder** (2 input lines needed to select among 4 chips, since 2² = 4)

---

## Section D — Comparison / "Which is Better" Questions

### Q17. SRAM vs DRAM — Compare and state which is better for what use case.
**Answer:**

| Feature | SRAM (Static RAM) | DRAM (Dynamic RAM) |
|---|---|---|
| Storage mechanism | Internal **latches** | **Capacitor charge** (via MOS transistors) |
| Needs refreshing? | No | Yes — every few milliseconds (charge decays over time) |
| Speed | **Faster** — shorter read/write cycles | Slower |
| Power consumption | **High** | **Reduced/lower** |
| Density (bits per chip) | **Low** | **High** — more integration possible on one chip |
| Cost | **Expensive** | Cheaper |
| Best used for | **Cache memory** (speed matters most) | **Main memory / RAM** (capacity matters most, cost-sensitive) |

**Which is better?** Neither is universally "better" — it's a **trade-off**:
- Choose **SRAM** when speed is critical and the amount of memory needed is small → e.g., **CPU cache**.
- Choose **DRAM** when you need **large capacity at low cost**, and can tolerate slightly slower access and the need for refresh circuitry → e.g., **main system memory**.

### Q18. ROM vs RAM — Compare.
**Answer:**

| Feature                                | ROM                                                            | RAM                                     |
| -------------------------------------- | -------------------------------------------------------------- | --------------------------------------- |
| Can be written to (during normal use)? | ❌ No — read only                                               | ✅ Yes — read and write                  |
| Volatile?                              | **Non-volatile** (keeps data with no power)                    | **Volatile** (loses data with no power) |
| Typical use                            | Boot instructions / OS startup, fixed lookup data              | Currently running programs & data       |
| Speed                                  | Comparable to RAM (fast)                                       | Fast                                    |
| Changeable pattern                     | Fixed once programmed (except EPROM/EAROM which can be erased) | Freely changeable any time              |

**Which is better?** Depends on purpose:
- Need something that **survives power-off** and shouldn't change (e.g., boot code, fixed logic tables) → **ROM**.
- Need something that's **actively read/written during operation** (e.g., running programs, working data) → **RAM**.

### Q19. Volatile vs Non-Volatile Memory — Compare, with examples.
**Answer:**

| Type | Loses data on power-off? | Examples |
|---|---|---|
| **Volatile** | ✅ Yes | RAM (both SRAM and DRAM) |
| **Non-volatile** | ❌ No | Magnetic disks (direction of magnetization), CDs (pits & lands on polycarbonate), ROM (fixed storage elements) |

**Which is better?** Again a trade-off:
- Volatile memory (RAM) is fast, used for temporary/working data.
- Non-volatile memory (disks, CDs, ROM) is used for **permanent storage** that must survive power loss — but is generally slower to access than RAM.

### Q20. ROM vs PLA — Compare, and state when each is the better choice.
**Answer:**

| Feature | ROM | PLA |
|---|---|---|
| Decoding | **Full decoder** — generates ALL 2ⁿ minterms | **Partial** — only generates the product terms you actually program (via AND gates) |
| Links needed | 2ⁿ × m (grows exponentially with inputs) | 2n×k + k×m + m (grows much more slowly) |
| Best when... | Function needs **most/all minterms**, or n is small | There are **many don't-care conditions**, or only a few product terms are actually needed out of a possible huge number |
| Output form | Sum of minterms / truth table | Sum of Products (SOP), programmer chooses which terms to build |

**Which is better?** 
- If a circuit genuinely needs most of the possible minterms → **ROM** is simpler (just fill in a truth table).
- If a circuit has a **small number of needed product terms compared to total possible minterms** (lots of don't-cares) → **PLA is more economical**, since it doesn't waste hardware generating a full 2ⁿ decoder.

### Q21. Mask ROM vs PROM vs EPROM vs EAROM — Compare programmability.
**Answer:**

| Type         | Who programs it?                                           | Can it be changed later?                                 | Best for                                                                       |
| ------------ | ---------------------------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------ |
| **Mask ROM** | Manufacturer (during fabrication)                          | ❌ Never                                                  | **Large quantities** of the exact same chip (economical only at scale)         |
| **PROM**     | User (with a PROM programmer, physically breaks fuses)     | ❌ No — irreversible; must discard chip to change pattern | **Small quantities**, one-time custom programming                              |
| **EPROM**    | User (electrically), erased using **UV light**             | ✅ Yes — can be erased & reprogrammed                     | Development/testing where the pattern needs occasional updates                 |
| **EAROM**    | User (electrically), erased using an **electrical signal** | ✅ Yes                                                    | Similar to EPROM but erase is electrical, not UV-light-based (more convenient) |

**Which is better?** 
- For **mass production**, Mask ROM is cheapest per unit.
- For **small-batch or prototype work**, PROM is fine if you're sure you won't need to change it.
- For **anything that might need reprogramming**, EPROM/EAROM is better — EAROM is more convenient than EPROM since it doesn't need a UV lamp.

### Q22. Two-Dimensional Decoding vs Single Large Decoder — Compare, which is better for large memories?
**Answer:**

| Approach | Hardware needed | Scalability |
|---|---|---|
| **Single k-input decoder** | One big decoder with 2^k output lines | Becomes very large/impractical as k grows (e.g., a 10-input decoder needs 1024 output lines) |
| **Two-dimensional decoding** | Two smaller decoders, each with k/2 inputs (row + column) | Much more efficient — arranges cells close to a square, needs far less decoding hardware for the same total capacity |

**Which is better?** **Two-dimensional decoding is better for large memories** — it drastically reduces the amount of decoding hardware needed, which is why real/commercial RAM chips use this approach instead of one giant decoder.

---

## Quick Reference — Formulas Used in These Questions

- **ROM size** = 2ⁿ × m (n = address lines, m = bits per word)
- **Number of words** = total bits ÷ bits per word
- **Address lines needed (n)** = log₂(number of words)
- **PLA size** = n × m × k
- **PLA links** = 2n×k + k×m + m
- **ROM links** = 2ⁿ × m
- **Chips needed** = total capacity ÷ single chip capacity
- **Chip-select decoder inputs** = total address lines − address lines per chip
- **Bytes** = total bits ÷ 8
