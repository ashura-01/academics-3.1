---
tags: [digital-system-design, memory, ram, lecture-6]
---

# RAM — Memory & Programmable Logic (Part 2)

> This note covers: what RAM is, how read/write works, static vs dynamic RAM, the memory hierarchy, volatile vs non-volatile memory, how a RAM memory cell works, how a 4×4 RAM is built, two-dimensional decoding, and how to combine small RAM chips into a bigger memory.

Related note: see **"ROM and PLA"** (Lecture 5) for the other half of memory devices.

---

## 1. Quick Recap — What is Memory?

- **Memory Device**: stores binary information, and lets us get it back for processing later.
- **Memory Unit**: a collection of cells that store a large amount of binary information.

Two types:
1. **RAM** (this note)
2. **ROM** (see other note)

---

## 2. RAM (Random-Access Memory)

### Simple definition
RAM is where the computer keeps the **operating system, running programs, and data currently in use**, so the processor (CPU) can access them quickly.

### Comparing RAM and ROM
- RAM is roughly as fast as ROM, but:
  - RAM **can be changed** (read AND write)
  - RAM is **volatile** → loses data when power turns off
  - ROM is **read-only** and **non-volatile**

### Memory Unit / Memory Word — Definitions
- **Memory unit**: stores binary information in groups of bits, and each group is called a **word**.
- **Memory word**: a group of 1's and 0's — could represent a number, a character, an instruction, or any other binary-coded info.
- Most computer memories use words that are **multiples of 8 bits** — this group of 8 bits is called a **byte**.
  - Example: a 32-bit word = 4 bytes.

### Memory Address
- Every word stored in memory is given an **address**, starting from 0 up to **2^k − 1**, where **k = number of address lines**.

**Picture: `L6_memory_unit_block_and_address_table.png`**
![[L6_memory_unit_block_and_address_table.png]]

Reading this diagram (Block Diagram of a Memory Unit):
- **k address lines** go into the memory unit → this selects **2^k words**.
- **n data input lines** → used to write new data into the selected word.
- **n data output lines** → used to read the data of the selected word.
- **Read** and **Write** control lines tell the memory whether to fetch data out or store new data in.

The table on this slide (Fig 7-3) shows an example: a **1024 × 16 memory** (1024 words, each 16 bits). Each row = one address (shown in binary and decimal) and its stored content.
- **Question from the slide:** How many bytes is this memory module?
  → 1024 words × 16 bits = 16,384 bits = 2048 bytes = **2 KB**

---

## 3. RAM: Write and Read Operations (step-by-step procedure)

### To WRITE a new word into memory:
1. Apply the **binary address** of the word to the address lines.
2. Apply the **data bits** you want to store to the data input lines.
3. Activate the **Write** input.

### To READ a stored word from memory:
1. Apply the **binary address** of the word to the address lines.
2. Activate the **Read** input.

> That's it — this is the exact procedure used every time any data is read from or written to memory.

---

## 4. Memory Types: Static vs Dynamic RAM

Integrated circuit RAM comes in **two operating modes**:

### Static RAM (SRAM)
- Made of **internal latches** that store binary information.
- Stays valid as long as **power is applied**.
- **Faster** — has shorter read and write cycles.
- Used in **cache memory** (because cache needs to be fast).
- Disadvantage: **high power consumption, low density (fewer bits per chip), expensive**.

### Dynamic RAM (DRAM)
- Stores binary information as **electric charge on tiny capacitors** (built using MOS transistors).
- Problem: the charge **leaks/decays over time**.
- Solution: DRAM must be **refreshed** (recharged) every few milliseconds, or the data is lost.
- Advantages: **reduced power consumption**, and you can fit **many more units on a chip** (higher density) compared to SRAM.

| Feature | SRAM | DRAM |
|---|---|---|
| How it stores data | Latches | Capacitor charge |
| Needs refreshing? | No | Yes (every few ms) |
| Speed | Faster | Slower |
| Power use | High | Lower |
| Density (bits per chip) | Low | High |
| Typical use | Cache | Main memory |

---

## 5. Memory Hierarchy

**Picture: `L6_memory_hierarchy_pyramid.png`**
![[L6_memory_hierarchy_pyramid.png]]

This pyramid shows memory arranged from fastest/smallest (top) to slowest/largest (bottom):

1. **CPU Register** (top — fastest, smallest, temporary storage)
2. **Cache** (Level 1, Level 2) — also temporary storage
3. **RAM** (Physical RAM, Virtual Memory) — still temporary storage
4. **Storage Devices** (permanent storage): ROM/BIOS, Removable Drives, Network/Internet Storage, Hard Drive
5. **Input Sources** (bottom): Keyboard, Mouse, Removable Media, Scanner/Camera/Mic/Video, Remote Source, Other Sources

**Key relationship (very important to remember):**
- As you go **UP** the pyramid → **physical size of memory DECREASES**, but **speed increases** (access time decreases)
- As you go **DOWN** the pyramid → **memory access time INCREASES** (slower), but size/capacity increases

So there's always a trade-off: **small and fast** (top) vs **big and slow** (bottom).

---

## 6. Volatile vs Non-Volatile Memory

- **Volatile memory**: loses its information when power is turned off.
  - Example: **RAM** (both static and dynamic) is volatile.
- **Non-volatile memory**: keeps its information even when power is off.

**Examples of non-volatile memory:**
1. **Magnetic disks** — data is stored using the **direction of magnetization**.
2. **CD (Compact Disc)** — made of polycarbonate (a plastic). Data is stored as a spiral track made of **indentations ("pits") separated by flat areas ("land")**.
3. **ROM** — internal storage elements are set once, and after that can only be read.

---

## 7. RAM Memory Cell (the smallest building block of RAM)

**Picture: `L6_RAM_cell_and_4x4_RAM.png`**
![[L6_RAM_cell_and_4x4_RAM.png]]

### (a) The Memory Cell — Logic Diagram
- The storage part of one memory cell is modeled using an **SR latch** (Set-Reset latch), plus some extra gates around it.
- Inputs: **Select** (chooses this cell), **Input** (the data bit to write), **Read/Write** (decides direction)
- Output: **Output** (the data bit read out)

**How the Read/Write control works:**
- **Read/Write = 1** → performs a **READ** operation: it opens a path **from the latch to the output**, so we can read the stored value.
- **Read/Write = 0** → performs a **WRITE** operation: it opens a path **from the input to the latch**, so the new data bit gets stored.

### (b) Block Diagram symbol
- A simple box labeled **BC** (Binary Cell) with **Select**, **Input**, **Output**, and **Read/Write** lines — this is the simplified symbol used for one memory cell when building bigger memory arrays.

---

## 8. 4 × 4 RAM (building an actual small RAM array)

(Same picture as above: `L6_RAM_cell_and_4x4_RAM.png`, bottom half)

### How it's built:
- We arrange **binary cells (BC)** in a grid: **4 rows (words) × 4 columns (bits per word)** = 4×4 RAM.
- **Address inputs** go into a **2 × 4 decoder** → this decoder selects one of the 4 words (Word 0, Word 1, Word 2, or Word 3).
- **Memory enable (EN)** — turns the whole chip on/off.
- **Input data** lines run down each column, feeding all 4 cells in that column (but only the selected word's cells will actually store it).
- **Read/Write** line is shared and tells all cells whether this is a read or write operation.
- **Output data** — each column's cells feed into an **OR gate**, and the OR gate output becomes that bit's final output.

### WRITE operation (step-by-step):
1. The decoder selects one word using the address.
2. Data on the input lines gets transferred into the **4 binary cells of the selected word only**.
3. All the **other (non-selected) words are disabled** — they don't change.

### READ operation (step-by-step):
1. The decoder selects one word using the address.
2. The 4 bits of that selected word pass through the **OR gates** to reach the output terminals.

> **Why OR gates on the output?** Since only one word is selected at a time (all others output 0 due to being disabled), the OR gate simply "picks up" whichever word is active and passes it to the output — like a mux built from OR gates.

---

## 9. Commercial RAM & Two-Dimensional Decoding

**Picture: `L6_commercial_RAM_and_2D_decoding.png`**
![[L6_commercial_RAM_and_2D_decoding.png]]

### Commercial RAM basics
- Real/commercial RAM chips have **thousands of words**, with each word being **1 to 64 bits**.
- **Rule:** A memory with **2^k words** of **n bits/word** needs **k address lines**, going into a **k × 2^k decoder**.

### Why Two-Dimensional Decoding?
- Problem: if you have a huge memory (like 1024 words), using **one big decoder** (like a 10-input decoder for 1024 outputs) is inefficient and takes a LOT of wiring/hardware.
- **Solution — Two-Dimensional Decoding:** Arrange memory cells in a shape as close to a **square** as possible, and use **two smaller decoders** (each handling k/2 inputs) instead of one giant decoder.
  - One decoder handles **row selection** (call it X)
  - The other decoder handles **column selection** (call it Y)
  - Together, X and Y pick out exactly one memory cell/word in the 2D grid — like coordinates on a graph.

### Example from the slide
- For a **1K-word memory** (1024 words), we'd normally need 10 address bits.
- Split into two 5-bit decoders: one **5×32 decoder for X** (rows) and one **5×32 decoder for Y** (columns) → together they can address 32 × 32 = 1024 positions.
- Example binary address `01100 10100` → X = 01100, Y = 10100 → this equals decimal **404** in the diagram.

**Question from slide:** *How many words can be selected [at once]?*
→ Only **one** word is selected at any time — even with 2D decoding, exactly one row-line and one column-line intersect to pick exactly one cell/word.

---

## 10. Building Bigger Memory from Smaller Chips

### 64K × 8 RAM chip (the basic building block)

**Picture: `L6_64Kx8_RAM_chip.png`**
![[L6_64Kx8_RAM_chip.png]]

- This single chip has a **capacity of 64K words, each 8 bits**.
- Pins: **DATA** (input/output data), **ADRS** (address), **CS** (Chip Select — turns this specific chip on/off), **R/W** (Read/Write control)

**Slide questions (with answers):**
- *How many chips are needed to construct 256K × 8?*
  → 256K ÷ 64K = **4 chips**
- *What is the size of the decoder?*
  → We need to choose between 4 chips → that needs a **2-to-4 decoder**

### Constructing 256K × 8 RAM — step by step reasoning

1. **How many 64K × 8 RAM chips are needed for 256KB total capacity?**
   → 256K ÷ 64K = **4 chips**

2. **How many address lines are needed to access 256K bytes, and how many go to ALL chips?**
   → 256K = 2^18, so we need **18 address lines total**.
   → Each individual 64K chip only needs **16 address lines** (since 64K = 2^16) — so **16 lines connect to the address input of every chip**.

3. **How many lines must be decoded for chip select?**
   → The remaining **2 address lines** (18 − 16 = 2) are used for **chip selection** — decoded by a 2-to-4 decoder to activate exactly one of the 4 chips at a time.

### Full 256K × 8 RAM circuit

**Picture: `L6_256Kx8_RAM_and_32x8_ROM.png`**
![[L6_256Kx8_RAM_and_32x8_ROM.png]]

**How it all connects (256K × 8 RAM, top half of image):**
- **Address has 18 bits total**: 16 LSBs (Lines 0–15) go to the address input of **every** chip. The 2 MSBs (Lines 16–17) go into a **2-to-4 decoder**.
- The decoder's 4 outputs connect to the **Chip Select (CS)** pin of each of the 4 RAM chips — so **only one chip is active at any given time**.
- Each chip covers a different address range:
  - Chip 0 → addresses 0–65,535
  - Chip 1 → addresses 65,536–131,071
  - Chip 2 → addresses 131,072–196,607
  - Chip 3 → addresses 196,608–262,143
- All 4 chips' **DATA** pins are **three-state outputs**, tied together to form the same **8 data output lines** — this works safely because only one chip is ever "active" (driving the bus) at a time; the others stay in high-impedance (off) state.

**32 × 8 ROM chip (bottom half of image) — bonus comparison:**
- This is the ROM structure from the previous lecture, shown again here for comparison: a **5 × 32 decoder** takes inputs I₀–I₄ and generates 32 minterm lines, which connect (through fixed/programmed links) to 8 OR gates producing outputs A₀–A₇.
- Notice the **similarity**: both RAM and ROM use decoders to select among many words — the difference is RAM can be **written to** as well as read, while ROM's pattern is fixed.

---

## Quick Recap (Cheat Sheet)

- **RAM** = readable AND writable, **volatile** (loses data on power-off), used for OS/programs/data currently in use.
- **Write procedure:** address → data → activate Write. **Read procedure:** address → activate Read.
- **SRAM** = latches, fast, no refresh needed, used in cache, expensive/low density.
- **DRAM** = capacitor charge, needs refreshing every few ms, cheaper/high density, used as main memory.
- **Memory Hierarchy:** Register → Cache → RAM → Storage devices → Input sources. Going up = faster & smaller. Going down = slower & bigger.
- **Volatile:** RAM. **Non-volatile:** ROM, magnetic disks, CDs.
- **RAM memory cell** = SR latch + gates; Read/Write=1 → read path; Read/Write=0 → write path.
- **4×4 RAM** = decoder selects a word; write updates only that word's cells; read passes selected word's bits through OR gates.
- **Two-dimensional decoding**: split one big decoder into 2 smaller ones (row × column) to save hardware for large memories — still only 1 word gets selected.
- **Combining chips:** total address lines = lines needed for full range; low-order bits go to all chips' address pins; high-order bits get decoded to drive each chip's **Chip Select**; outputs are tied together using **three-state (tri-state)** logic.
