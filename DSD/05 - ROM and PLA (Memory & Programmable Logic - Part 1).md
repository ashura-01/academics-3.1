---
tags: [digital-system-design, memory, rom, pla, lecture-5]
---

# ROM and PLA — Memory & Programmable Logic (Part 1)

> This note covers: what memory is, ROM (Read Only Memory), how ROM is built internally, how to use ROM to build logic circuits, the different types of ROM, and PLA (Programmable Logic Array).

---

## 1. What is a Memory Device?

**Memory Device** = a device where we can **store** binary information, and later **read it back** when we need to process it.

**Memory Unit** = a group of many memory cells that together can store a large amount of binary information.

There are **two main types of memory** in digital systems:
1. **RAM** – Random Access Memory (covered in the next note)
2. **ROM** – Read Only Memory (covered here)

---

## 2. ROM (Read Only Memory)

### Simple definition
ROM is a memory device that stores a **fixed** set of binary information — the data is written once and after that, we can only **read** it, not change it (in normal operation).

### Key properties
- **Non-volatile** → it keeps its data even when the power is turned OFF.
- Used to **start up a computer** and load the operating system (because the boot instructions must survive power-off).
- The information stored must first be decided by the user (designer), and then it is permanently built into the chip to create the required connection pattern.

### How ROM works internally
- A ROM chip contains a **decoder** and a set of **OR gates**, all inside a single IC (chip).
- Because everything is inside one chip, ROM is great for building **complex combinational circuits** without needing lots of separate wires.
- Once the pattern (which connections exist) is fixed, it stays fixed even without power.
- Internally, ROM has **special links (fuses)** that can be **broken or kept** — this is literally how you "program" it.

### ROM Structure (the important part!)

**Picture: `L5_ROM_block_diagram.png`**
![[L5_ROM_block_diagram.png]]

This is a block diagram of a ROM chip. It has:
- **n input lines** (these carry the address)
- **m output lines** (these carry the word / stored data)
- It is written as **2ⁿ × m ROM**

How to read this:
- With **n input lines**, you can create **2ⁿ different addresses** (just like n bits can represent 2ⁿ numbers).
- Each unique combination of input bits is called an **address**.
- Each combination of bits that comes OUT of the output lines is called a **word**.
- **Number of bits per word = m** (same as the number of output lines).
- So a ROM is fully described by 2 numbers: **how many words** (2ⁿ) and **how many bits per word** (m). Written as **2ⁿ × m**.

### Worked Example — 32 × 8 ROM
- A 32 × 8 ROM = 256-bit ROM (because 32 words × 8 bits = 256 bits total).
- It has 32 words, each word is 8 bits.
- Since 32 = 2⁵ → we need **5 input lines** (5-bit address).
- Input `00000` → selects word number 0
- Input `11111` → selects word number 31

**Practice questions from the slide (try these yourself):**
- Design a 2048-bit ROM with word size = 8 bits each. (Hint: 2048 ÷ 8 = 256 words = 2⁸ → 8 address lines, size = 256 × 8)
- Design a 2048-bit ROM with word size = 4 bits each. (Hint: 2048 ÷ 4 = 512 words = 2⁹ → 9 address lines, size = 512 × 4)

### Internal Logic Construction — Example: 32 × 4 ROM

**Picture: `L5_32x4_ROM_internal_logic.png`**
![[L5_32x4_ROM_internal_logic.png]]

How to read this diagram:
- On the left there is a **5 × 32 decoder**. It takes 5 address inputs (A₀ to A₄) and turns them into 32 separate output lines (called **minterms** 0 to 31) — only ONE of these 32 lines is "high/active" at a time, depending on the address.
- Each of these 32 decoder-output lines can be connected through a **link (fuse)** to any of the 4 **OR gates** (F₁, F₂, F₃, F₄) on the right.
- There are **128 fuses total** in this example (32 lines × 4 OR gates = 128 possible connections).
- **Programming the ROM = deciding which fuses stay connected and which are broken.**
- If a link is kept, that minterm contributes to that output function. If the link is broken, it doesn't.

This is exactly how ROM builds any logic function: **decoder generates every possible minterm, and OR gates pick which minterms belong to which output.**

---

## 3. Using ROM to Build Combinational Logic Circuits

**Golden rule:** For an **n-input, m-output** combinational circuit, you need a **2ⁿ × m ROM**.

**Process / Method:**
1. Write down the **truth table** of the circuit you want to build (or express outputs as sum of minterms).
2. The truth table tells you *exactly* what to program into the ROM.
3. Each row of the truth table = one address (input combination) → and the output columns tell you which fuses to keep connected.

### Example
**Picture: `L5_ROM_truth_table_example.png`**
![[L5_ROM_truth_table_example.png]]

Given:
- F₁(A₁, A₀) = Σ(1, 2, 3)
- F₂(A₁, A₀) = Σ(0, 2)

The truth table directly gives us the programming information — wherever F₁ or F₂ = 1 in that row, we keep that link connected in the ROM.

> **Remember:** When implementing logic with ROM, functions **must** be written as sum of minterms or as a truth table — because ROM works by selecting/combining minterms.

### ROM with AND-OR Gates (a smaller worked example)

**Picture: `L5_ROM_AND_OR_gates.png`**
![[L5_ROM_AND_OR_gates.png]]

- This is a **4 × 2 ROM** (2 inputs A₀, A₁ → 2×4 decoder → 4 minterms; 2 outputs F₁, F₂).
- The 2×4 decoder produces 4 lines: 00, 01, 10, 11.
- Each line connects (through a link) to the OR gates that create F₁ and F₂.
- **ROM Size = 4 × 2**

### ROM with AND-OR-Invert Gates

**Picture: `L5_ROM_AND_OR_INVERT_gates.png`**
![[L5_ROM_AND_OR_INVERT_gates.png]]

- Same structure as above, but now there is an **inverter (NOT gate)** placed **after each OR gate**.
- **Why inverters exist:** Some ROM chips are built this way in hardware, so the final output is the complement of the OR gate result. Designers must plan for this when programming the ROM.

---

## 4. Bigger Example — ROM that computes "square of a number"

**Task:** Design a combinational circuit using ROM. The circuit takes a **3-bit number** as input and outputs a **binary number equal to the square** of that input.

**Picture: `L5_square_example_truth_table.png`**
![[L5_square_example_truth_table.png]]

- Inputs: A₂ A₁ A₀ (3 bits → 8 possible input values: 0 to 7)
- Outputs: B₅ B₄ B₃ B₂ B₁ B₀ (6 bits, because 7² = 49, which needs 6 bits to represent)
- Example rows: input 3 (011) → output 9 (001001); input 7 (111) → output 49 (110001)

**Picture: `L5_square_example_block_diagram.png`**
![[L5_square_example_block_diagram.png]]

- Since we have **3 inputs**, we need **2³ = 8 words**.
- The largest output value needs **4 non-zero/variable bits** to be generated by ROM (some output bits like B₀ and B₁ turn out to be simple, so this example only needed an **8 × 4 ROM**, with B₁ always 0 and B₀ = A₀ directly wired, without needing ROM storage for those two).
- **Lesson:** Not every output bit needs full ROM circuitry — some bits might follow simple patterns and can be wired directly, saving ROM size.

---

## 5. Types of ROM (very important — commonly asked)

| Type | How it's programmed | Can you change it later? |
|---|---|---|
| **Mask ROM** | Programmed by the **manufacturer** during the last step of fabrication (chip-making), based on a truth table the customer provides. | ❌ No — permanent. Only worth it for **large quantities** of the same design. |
| **PROM** (Programmable ROM) | User programs it themselves using a special "PROM programmer" device — this **physically breaks links** (fuses) inside the chip. | ❌ No — irreversible. Once a link is broken, it can't be reconnected. If you need to change the pattern, you must **discard the chip** and use a new one. Good for **small quantities**. |
| **EPROM** (Erasable PROM) | Programmed electrically, but can be **erased using ultraviolet (UV) light** for some time, which resets internal gates. | ✅ Yes — can be reprogrammed after erasing. |
| **EAROM** (Electrically Alterable ROM) | Same idea as EPROM, but erased using an **electrical signal** instead of UV light. | ✅ Yes |

### Why is it called "Read-Only" Memory?
- **Memory** = a storage unit.
- **Read** = the contents of a word (found using its address) are made available at the output.
- So ROM = a memory unit with a **fixed word pattern** that you can read using an address, but the pattern itself **cannot be changed during normal operation**.

### Common Uses of ROM
- Implementing complex combinational circuits directly from a truth table
- Converting between binary codes (example: ASCII ↔ EBCDIC)
- Arithmetic functions such as multipliers
- Displaying characters on a screen (CRT)
- Systems that need a large number of inputs/outputs
- Control units of digital systems — a control unit that uses ROM to store control information is called a **microprogrammed control unit**

---

## 6. PLA — Programmable Logic Array

### Simple definition
PLA is a programmable chip with:
- Programmable **AND gates**, followed by
- Programmable **OR gates**

### PLA vs ROM — What's the difference?
- ROM has a **full decoder**, which means it generates **every single possible minterm** (all 2ⁿ of them), whether you need them or not.
- PLA does **NOT** fully decode. Instead of a decoder, PLA uses a group of **AND gates**, and **each AND gate can be programmed to create only the product term you actually need**.
- This makes PLA **more economical/efficient** especially when there are a lot of **don't-care conditions**, because you don't waste space generating minterms you'll never use.
- Functions in PLA are implemented in **Sum of Products (SOP)** form, by keeping/breaking the correct links.

### PLA Structure

**Picture: `L5_PLA_structure_diagram.png`**
![[L5_PLA_structure_diagram.png]]

Reading the diagram:
- **n inputs** → go through inverters (so you have both the normal and complemented version of each input available) → feed into
- **k product terms** (a group of k AND gates) → feed into
- **m sum terms** (a group of m OR gates) → each optionally passes through an inverter → gives **m outputs**

Key formulas:
- **PLA size = n × m × k** (inputs × outputs × product terms)
- Example: a typical PLA might have 16 inputs, 48 product terms, and 8 outputs.
- **Number of programmable links in a PLA = (2n × k) + (k × m) + m**
  - `2n × k` → because both true and complement of each input connect to each AND gate (that's why it's 2n, not n)
  - `k × m` → each AND gate output can connect to each OR gate
  - `m` → each output can optionally pass through an invert-or-not switch
- Compare this to ROM, where the number of links is simply **2ⁿ × m**.

### Small Example of PLA

**Picture: `L5_PLA_example_circuit.png`**
![[L5_PLA_example_circuit.png]]

- Inputs, n = 3 (A, B, C)
- Product terms, k = 3
- Outputs, m = 2 (F₁, F₂)
- So PLA size = n × m × k = 3 × 2 × 3 = **18**

Notes:
- Just like ROM, PLA can be **mask-programmable** (manufacturer does it, customer submits a PLA program table) or **field-programmable**.
- A field-programmable PLA is called **FPLA**, and it works similar to a PROM (user programs it themselves).

---

## 7. PLA Implementation — Full Worked Example

**Given truth table:**

| A | B | C | F₁ | F₂ |
|---|---|---|----|----|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 0 | 0 |
| 0 | 1 | 0 | 0 | 0 |
| 0 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 | 0 |
| 1 | 0 | 1 | 1 | 1 |
| 1 | 1 | 0 | 0 | 0 |
| 1 | 1 | 1 | 1 | 1 |

**Step 1 — Simplify using K-maps (Karnaugh maps)**

**Picture: `L5_PLA_kmap_simplification.png`**
![[L5_PLA_kmap_simplification.png]]

From the K-maps:
- **F₁ = AB' + AC**
- **F₂ = AC + BC**

**Step 2 — Build the PLA program table**

**Picture: `L5_PLA_program_table.png`**
![[L5_PLA_program_table.png]]

How the table works:
- Each row = one **product term** used by the outputs (here: AB', AC, BC)
- Columns under "Inputs" show whether each variable is **1** (true), **0** (complemented), or **–** (don't care / not used in that term)
- Columns under "Outputs" show whether that product term is used (**1**) in F₁ and/or F₂ (**–** means not used)
- The last row (**T / T/C**) tells you whether the output inverter should be **bypassed (T)** or the output should be **complemented (C)**

**Step 3 — Draw the actual PLA circuit**

**Picture: `L5_PLA_full_diagram.png`**
![[L5_PLA_full_diagram.png]]

This shows the 3 inputs (with inverters for complements), 3 AND gates (product terms 1, 2, 3 for AB', AC, BC), and 2 OR gates producing F₁ and F₂ (with optional invert stage).

### Tips for Designing with PLA
- Try to **reduce the number of distinct product terms** — this saves AND gates.
- The number of *literals* in a term doesn't matter much (since all input variables are already available), what matters is the **number of unique product terms**.
- Always check **both the true form and complemented form** of a function — pick whichever needs **fewer product terms**, or whichever shares terms with other outputs (**shared terms save space**).

---

## 8. PLA Example 2 (with sharing of product terms)

**Given:**
- F₁(A, B, C) = Σ(3, 5, 6, 7)
- F₂(A, B, C) = Σ(0, 2, 4, 7)

**Task:** Implement with a PLA having 3 inputs, 4 product terms, 2 outputs.

**Picture: `L5_PLA_example2_kmaps.png`**
![[L5_PLA_example2_kmaps.png]]

This slide shows 4 K-maps:
- F₁ = AC + AB + BC (3 terms — too many if used directly)
- F₂ = B'C' + A'C' + ABC
- F₁' (complement of F₁) = B'C' + A'C' + A'B'
- F₂' (complement of F₂) = B'C + A'C + ABC'

**Trick used here:** By comparing F₁ and F₁', and F₂ and F₂', we look for **the version that shares product terms between F₁ and F₂**, so we only need 4 total unique product terms instead of building each function separately with its own 3 terms.

Final chosen equations:
- **F₁ = (B'C' + A'C' + A'B')'** ← this is F₁ using its complement form, inverted
- **F₂ = B'C' + A'C' + ABC**

Notice F₁ and F₂ **share two product terms**: B'C' and A'C'. This is the whole point of the "check both forms" tip above — sharing terms means only 4 unique product terms are needed (B'C', A'C', A'B', ABC) instead of more.

**Final PLA program table:**

**Picture: `L5_PLA_example2_program_table.png`**
![[L5_PLA_example2_program_table.png]]

- Product terms used: B'C', A'C', A'B', ABC (4 terms — matches the requirement of "4 product terms")
- F₁ column shows **C** (meaning F₁ needs to be **complemented** at output, since we built it from F₁' form)
- F₂ column shows **T** (output taken directly, true form)

> **Practice task from slide:** Draw the full PLA circuit diagram for this example yourself, using the program table above as your guide (same style as `L5_PLA_full_diagram.png`).

---

## 9. Applications of PLA

- Used to provide **control over datapath** in a processor
- Used to build a **counter**
- Used to build a **decoder**
- Used as a **bus interface** in programmed I/O
- Used to define the different **states in an instruction set**, and produce the next state (conditional branching) — this is core to how CPU control units work

---

## Quick Recap (Cheat Sheet)

- **ROM** = fixed data, non-volatile, read-only during normal use, built from **decoder + OR gates**, size = **2ⁿ × m**
- To build logic with ROM: write the **truth table / sum of minterms**, then decide which fuse-links stay connected
- **Types of ROM:** Mask ROM (factory-made, permanent) → PROM (user-programmed once, permanent) → EPROM (UV-erasable) → EAROM (electrically erasable)
- **PLA** = programmable AND gates + programmable OR gates, does **NOT** fully decode (unlike ROM), more efficient with don't-cares
- **PLA size = n × m × k**; **Number of links = 2n×k + k×m + m**
- To design with PLA: simplify with K-maps, check both true and complement forms, try to **share product terms** across outputs, then build the PLA program table
