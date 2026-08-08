---
tags: [lecture3, 8086, registers, flags, segmentation, protected-mode, worked-examples]
---

← [[x86 Based PC|← Lecture 2]] | [[Home]]

# Lecture 3 — The Architecture of 8086

## 1. Internal Block Diagram of the 8086

- **8086 = 16-bit processor** — 16-bit registers, ALU, and data bus.
- All actions synchronized by a **system clock** (provides basic timing).
- A **control unit** provides control signals for overall functioning.
- **Two logical units**, interacting via the internal bus but working **separately** (this is the key to pipelining):
  - **Execution Unit (EU)**
  - **Bus Interface Unit (BIU)**

![[attachments/L3_internal-block-diagram-8086.png]]

### Execution Unit (EU)
Contains the **ALU**, the **control unit**, an **internal bus**, and a few **registers** (General purpose registers, Temporary registers, Flags).

- **AX (Accumulator Register)** is a part of the ALU — the **result of an ALU operation is stored in AX**.
- **20-bit physical address** is generated (by the BIU) for memory access.
- 8086 is **CISC** with **variable instruction size**:
  - `MOV AX, BX` → 1 byte instruction
  - `MOV BX, 4050H` → 3 byte instruction

Instruction byte layout (general): `Op code | D W mod reg r/m | Low Disp/Data | High Disp/Data | Low Data | High Data` (up to 6 bytes total).

![[attachments/L3_execution-unit-full-diagram.png]]

### Bus Interface Unit (BIU) & the Instruction Queue
- Contains: **Relocation Register File** (Segment registers + Instruction Pointer), **Bus Control Logic**, and the **6-byte instruction queue**.
- **Instructions are pre-fetched** ahead of execution time, placed in a **6-byte FIFO queue**.
- **Advantage of pre-fetching**: when an instruction is about to execute, it's likely already sitting in the on-chip queue rather than needing a memory fetch (memory access is slow compared to on-chip access).
- **Exception**: the queue must be **emptied on a branch instruction** (when the next instruction isn't the next sequential one).
- This pre-fetch scheme is a form of **pipelining**: execution and fetching happen **at the same time**.

![[attachments/L3_bus-interface-unit-queue.png]]

---

## 2. Register Model: 8086 → Core 2 (Figure 2-1)

Register set grows over generations but keeps backward-compatible naming:

| 64-bit (P4/Core2) | 32-bit | 16-bit | 8-bit |
|---|---|---|---|
| RAX | EAX | AX | AH / AL |
| RBX | EBX | BX | BH / BL |
| RCX | ECX | CX | CH / CL |
| RDX | EDX | DX | DH / DL |
| RBP | EBP | BP | — |
| RSI | ESI | SI | — |
| RDI | EDI | DI | — |
| RSP | ESP | SP | — |

- Shared across **8086, 8088, 80286** (16-bit registers only).
- **80386 and above**: 32-bit registers (E-prefix).
- **Pentium 4 and Core 2**: 64-bit registers (R-prefix), plus new registers **R8–R15** (found only in Pentium 4/Core 2).
- **RFLAGS/EFLAGS/FLAGS**: condition & control operation of the microprocessor.
- **RIP/EIP/IP**: Instruction Pointer — address of the **next instruction**.

### General/Multipurpose registers
- **EAX, EBX, ECX, EDX, EBP, EDI, ESI** — hold various data sizes (bytes, words, double words).

### Special-purpose register roles (mnemonic meanings)
| Register | Purpose |
|---|---|
| **AX (Accumulator)** | mul, div, offset |
| **BX (Base)** | index/offset address |
| **CX (Count)** | LOOP, REP, shift count |
| **DX (Data)** | I/O address, memory data, holds result of `mul` |
| **BP (Base Pointer)** | memory location in stack |
| **SI (Source Index)** | source of a data string |
| **DI (Destination Index)** | destination of a data string |
| **SP (Stack Pointer)** | top of stack |

### Register-size override table

| Register Size | Override letter | Bits Accessed | Example |
|---|---|---|---|
| 8 bits | B | 7–0 | `MOV R9B, R8B` |
| 16 bits | W | 15–0 | `MOV R9W, R8W` |
| 32 bits | D | 31–0 | `MOV R9D, R8D` |
| 64 bits | — | 63–0 | `MOV R9, R8` |

![[attachments/L3_register-model-8086-to-core2-fig21.png]]

---

## 3. The FLAG Register (Figure 2-2 — full EFLAG)

**General rules:**
- Flag bits **only change** after **arithmetic and logic** instructions.
- Flags **never change** for data transfer or program control operations.

![[attachments/L3_flag-register-full-fig22.png]]

### Basic 8086 flags (bits 0–11)

| Bit | Flag | Meaning |
|---|---|---|
| 0 | **C — Carry** | Carry out from the **MSB** (e.g. bit 7 for 8-bit op). |
| 2 | **P — Parity** | Set if result has an **even number of 1-bits** (checks lower byte). |
| 4 | **A — Auxiliary Carry** | Carry out from the **lower 4 bits (nibble)** — used for BCD arithmetic. |
| 6 | **Z — Zero** | Set if result = 0. |
| 7 | **S — Sign** | = MSB of the result (0 = positive, 1 = negative). |
| 8 | **T — Trap** | Single-step debugging flag. |
| 9 | **I — Interrupt** | Controls the **INTR pin**. `I=1` → INTR **enabled**. Set/cleared via **STI** (Set I Flag) / **CLI** (Clear I Flag). |
| 10 | **D — Direction** | `D=1` → string registers (DI, SI) **decrement**. Set/cleared via **STD** (Set D flag) / **CLD** (Clear D flag). |
| 11 | **O — Overflow** | Signed arithmetic overflow (carry into MSB ≠ carry out of MSB). |

- **DAA (Decimal Adjust AL)** and **DAS** do **BCD add/sub** and require the **A (auxiliary)** flag: if a nibble ≥ 10, subtract 10 and add 1 to the nibble on the left.

### Extended flags (80286 and later)

| Bit | Flag | Introduced | Meaning |
|---|---|---|---|
| 12–13 | **IOPL** (I/O Privilege level) | 80286+ | Sets required privilege level for I/O instructions. |
| 14 | **NT — Nested Task** | 80286+ | Indicates current task is nested within another task (protected mode). |
| 16 | **RF — Resume Flag** | 80386+ | Used with debugging to control resumption of execution after the next instruction. |
| 17 | **VM — Virtual Mode** | 80386+ | Selects virtual mode in a protected-mode system; allows multiple 1MB DOS memory partitions to coexist; used to simulate DOS inside modern Windows. |
| 18 | **AC — Alignment Check** | 80486SX+ | Activates if a word/doubleword is addressed on a **non-word/non-doubleword boundary**. |
| 19 | **VIF — Virtual Interrupt Flag** | Pentium+ | Copy of the Interrupt flag available to Pentium–Pentium 4; used in **multitasking** to give the OS virtual interrupt flags & interrupt-pending info. |
| 20 | **VIP — Virtual Interrupt Pending** | Pentium+ | Info about a virtual-mode interrupt pending, for Pentium–Pentium 4. |
| 21 | **ID — Identification** | Pentium+ | Indicates the CPU supports the **CPUID** instruction, which reports version number & manufacturer. |

![[attachments/L3_flag-register-nt-rf-vm-ac.png]]
![[attachments/L3_flag-register-vip-id.png]]

### 📐 BCD Addition (worked mechanism)

BCD digits are 4-bit nibbles that must stay in range 0–9. Adding two BCD numbers can produce invalid nibbles (≥10 or a carry) — fix by **adding 6 (0110) to any invalid nibble**.

Example: `43 + 35 = 78`
```
 0100 0011   (43)
+0011 0101   (35)
-----------
 0111 1000   (78) ✓ — both nibbles valid, no correction needed
```
Example: `75 + 35 = 110`
```
 0111 0101
+0011 0101
-----------
 1010 1010   ← both nibbles invalid (≥10, shown in red)
+0110 0110   ← add 6 to each invalid nibble
-----------
 0001 0001 0000  →  1 1 0  =  110 ✓
```

![[attachments/L3_bcd-addition-example.png]]

---

## 4. Worked Flag Examples

### 📝 Example 1 — CF, SF, AF
```
MOV AL, 35H
ADD AL, 0CEH
```
```
  35H     0011 0101
+ CEH  +  1100 1110
-----     ---------
 103H    1 0000 0011
```
- AL ends up holding `0000 0011`.
- **CF = 1** — carry out from bit D7 (MSB).
- **SF = 0** — sign bit (MSB of 8-bit destination AL) = 0.
- **AF = 1** — overflow/carry from bit D3 into D4 (the nibble boundary).

![[attachments/L3_example1-flags-cf-sf-af.png]]

### 📝 Example 2 — CF, ZF, OF (16-bit)
```
MOV BX, 45ECH
ADD BX, 7723H
```
```
  45ECH    0100 0101 1110 1100
+ 7723H  + 0111 0111 0010 0011
-------    -------------------
 BD0FH     1011 1101 0000 1111
```
- Sum goes into **BX** (destination register).
- **CF = 0** — no carry out from bit D15.
- **ZF = 0** — destination (BD0FH) is not zero.
- **OF = 1** — overflow into MSB (D15): a full-adder's `Cin XOR Cout` into the top bit signals signed overflow.

![[attachments/L3_example2-flags-cf-zf-of-adder.png]]

---

## 5. Segment Registers (summary)

| Register | Holds | Notes |
|---|---|---|
| **CS (Code)** | Starting address of the **Code Segment** | 64 KB on 8088–80286, 4 GB on 80386+ |
| **DS (Data)** | Starting address of the **Data Segment** | 64 KB on 8088–80286, 4 GB on 80386+ |
| **ES (Extra)** | Additional data segment, used for **string operations** to hold **destination** data | |
| **SS (Stack)** | Stack segment; entry address determined by **SS + SP**. **BP** also points into the Stack Segment. | |
| **FS, GS** | Supplementary segment registers | |

---

## 6. Real Mode Addressing

- Operates on the **first 1 MB** of memory — also called **real mode / conventional mode / DOS memory**.
- **Only 8086 and 8088 operate exclusively** in this mode.
- A memory location = **Segment address** (start of a 64K segment, stored in a segment register) **+ Offset address/displacement** (a location within that 64K segment).

**Physical Memory Address (EA) = (20-bit Segment address) + (16-bit offset address)**

Since 8086/8088 segment registers are only **16 bits**, internally the CPU **appends a 0H (one hex digit) at the rightmost end** of the segment register value before adding the offset.

![[attachments/L3_real-mode-addressing-diagram.png]]

### Physical address from a logical address (worked diagram)
Given: **DS Register = 2222H**, **Offset = 0016H**
- Base address = `22220H` (DS with 0 appended)
- Physical Address = `22220H + 0016H = 22236H`

![[attachments/L3_physical-address-calc-data-segment.png]]

### Example: Real-mode segment address ranges

| Segment register | Starting address | Ending address |
|---|---|---|
| 2000H | 20000H | 2FFFFH |
| 2001H | 20010H | 3000FH |
| 2100H | 21000H | 30FFFH |
| AB00H | AB000H | BAFFFH |
| 1234H | 12340H | 2233FH |

*(Each segment spans exactly 64 KB = 10000H, and successive segment-register values only 1 apart overlap heavily.)*

### 📐 Effective/Physical Address Calculation formula

**EA = Segment Register (SR) × 10H + Offset**

| # | SR | Offset | Calculation | EA |
|---|---|---|---|---|
| a | 1000H | 0023H | 10000 + 0023 | **10023H** |
| b | AAF0H | 0134H | AAF00 + 0134 | **AB034H** |
| c | 1200H | FFF0H | 12000 + FFF0 | **21FF0H** |

![[attachments/L3_effective-address-calc-examples.png]]

---

## 7. Default Segment and Offset Registers

- A program can access **four or six segments at a time**.
- Real mode addressing allows **relocation** — the same code can be placed at different physical locations without change.

![[attachments/L3_default-segment-offset-memory-map.png]]

**Convention: EA = CS:[IP]** → Segment start comes from the segment register; Offset is a literal or a CPU register.

| Segment | Default offset (16-bit: 8086/8088/80286) | Default offset (32-bit: 80386+) | Purpose |
|---|---|---|---|
| **CS** | IP | EIP | Program (code) |
| **SS** | SP, BP | ESP, EBP | Stack |
| **DS** | BX, DI, SI, 8/16-bit # | EBX, EDI, ESI, EAX, ECX, EDX, 8/32-bit # | Data |
| **ES** | DI, with string instructions | EDI, with string instructions | String destination |

![[attachments/L3_default-segment-offset-convention-table.png]]

### 📝 Example 3 — Multiple physical addresses at once
Given: `CS=1111H, DS=3333H, SS=2526H, IP=1232H, SP=1100H, offset in data segment=0020H`

**a) Code segment (CS:IP):**
Base = `11110H` → Physical = `11110H + 1232H = 12342H`

**b) Stack segment (SS:SP):**
Base = `25260H` → Physical = `25260H + 1100H = 26350H`

**c) Data segment (DS:offset):**
Base = `33330H` → Physical = `33330H + 0020H = 33350H`

![[attachments/L3_example3-physical-address-calc.png]]

---

## 8. Real Mode: Pros and Cons

**Advantages**
- **Relocation** (via segment registers): a *relocatable program* can be placed anywhere in memory and run without change; *relocatable data* likewise.

**Disadvantages**
- Complex hardware for memory addressing.
- Segments can begin **only on a 16-byte boundary** (a **"paragraph"**) — e.g. segment register `1201H` → segment starts at `12010H` (because of the internally appended 0H).
- Address computation delay.
- **No protection system.**

---

## 9. Protected Mode Memory Operation

- Can address **above the first 1 MB**, as well as within it. Used from **80286 onward**. This is where **Windows** operates.
- **80386 and above** use a **32-bit offset** instead of 16-bit (DOS uses a 16-bit environment; most Windows apps use a 32-bit environment called **WIN32**).
- **No paragraph-boundary limitation** — segments may start at **any address**.
- Segment registers **no longer hold a segment address directly**. Instead they hold a **SELECTOR** — an index that **selects a DESCRIPTOR** from a descriptor table.
- A **DESCRIPTOR** describes the segment's **location, length, privilege levels, and access rights**.

### Descriptors — Base, Limit, Access rights

| Field | 80286 | 80386 and above |
|---|---|---|
| **Base** | 24 bits (16 M memory) | 32 bits (4 G memory) |
| **Limit** | 16 bits | 20 bits |
| Example | SA=F00000H, EA=F0FFFFH, size=**64K** | SA=00F00000H, Limit=000FFH → 1 M to 1M/4K to 4GB |

- **G (Granularity) bit**:
  - `G=0` → Limit specifies segment size directly, **00000H–FFFFFH**.
  - `G=1` → **Limit = Limit × 4K**; internally FFFH is appended → `00000FFFH (4K) to FFFFFFFFH (4G)`.
- **AV (Available bit)**: `AV=1` → segment available; `AV=0` → not available.
- **D bit** (register/memory usage mode):
  - `D=0` → **DOS mode**: 16-bit instructions, compatible with 8086–80286 ("16-bit instruction mode").
  - `D=1` → all registers/offsets are **32-bit**.

![[attachments/L3_descriptors-80286-vs-80386-table.png]]

### 📝 Worked example — 80386+ descriptor with G bit
Given: **Base = 23000000H, Limit = 012FFH**

**With G = 0:**
- Segment start = `23000000H`
- Segment end = `23000000H + 012FFH = 230012FFH`
- Segment size = `12FFH + 1H = 1300H` (= 19 × 256 bytes)

**With G = 1** (actual limit = limit appended with FFFH → `012FFFFFH`):
- Segment start = `23000000H`
- Segment end = `23000000H + 012FFFFFH = 242FFFFFH`
- Segment size = `12FFFFFH + 1H = 1300000H = 2¹² × 1300H = 4K × 1300H`

![[attachments/L3_example-80386-descriptor-calc.png]]

### 📝 Example 4 — segment start/end with different G
Base = `10000000H`, Limit = `001FFH`

- **G = 0:** End = Base + Limit = `10000000H + 001FFH = 100001FFH`
- **G = 1:** (limit appended with FFFH) End = Base + Limit = `10000000H + 001FFFFFH = 101FFFFFH`

![[attachments/L3_example4-segment-start-end.png]]

---

## 10. Program Invisible Registers

Registers used internally by protected mode, **not directly accessible/visible** to normal programs:

- **Segment registers** (CS, DS, ES, SS, FS, GS) — each backed by a hidden **Descriptor cache** (Base address, Limit, Access) loaded from the descriptor table when the segment register is set.
- **TR (Task Register)** and **LDTR** — also backed by their own Base/Limit/Access cache. **LDTR** holds a selector from the **GDT** description row.
- **GDTR** (Global Descriptor Table Register) and **IDTR** (Interrupt Descriptor Table Register) — hold **Base address + Limit** of their respective tables. These are truly "program invisible."

Notes:
- The **80286** does **not** have FS/GS nor the program-invisible portions of these registers.
- 80286: Base = 24 bits, Limit = 16 bits.
- 80386/80486/Pentium/Pentium Pro: Base = 32 bits, Limit = 20 bits.
- Access rights: **8 bits in 80286**, **12 bits in 80386–Core2**.

![[attachments/L3_program-invisible-registers.png]]

---

## 11. Selectors and Descriptors (bit layout)

A 16-bit **Segment Register** (e.g. DS) in protected mode = **13-bit Selector + 1-bit TI + 2-bit RPL**:

- **RPL (Requested Privilege Level)**, bits 0–1: `00` = **highest** privilege, `11` = **lowest**.
  - `00` = Kernel, `01` = Device driver, `10` = OS services, `11` = Application.
- **TI (Table Indicator)**, bit 2: `TI=0` → **Global Descriptor Table (GDT)**; `TI=1` → **Local Descriptor Table (LDT)**.
- **Selector**, bits 3–15 (13 bits): selects **1 of 8192 descriptors** from either the global or local descriptor table.

**Sizing math:**
- Each descriptor table size = 2¹³ × 8 bytes (64 bits) = 2¹⁶ bytes = **64 KB**.
- An application could theoretically address = 8192 × 2 × 4G (32-bit system) = **64 TB** [80386].
- 80286: 8192 × 2 × 64KB.

![[attachments/L3_selectors-and-descriptors.png]]

---

## 12. Access Rights Byte

| Bit(s) | Field | Meaning |
|---|---|---|
| 0 | **A** | `A=0` segment not accessed; `A=1` segment has been accessed |
| 1 (R/W for data, R for code) | **R/W** | For **data**: `W=0` not writable, `W=1` writable. For **code**: `R=0` not readable, `R=1` readable |
| 2 | **ED/C** | For **data**: `ED=0` expands upward (normal data segment), `ED=1` expands downward (stack segment). For **code**: `C=0` ignore descriptor privilege level, `C=1` abide by privilege level |
| 3 | **E** | `E=0` descriptor describes a **data** segment; `E=1` describes a **code** segment |
| 4 | **S** | `S=0` system descriptor; `S=1` code or data segment descriptor |
| 5–6 | **DPL** | Descriptor Privilege Level |
| 7 | **P** | `P=0` descriptor undefined; `P=1` segment contains a valid base and limit |

- **DPL is always compared with RPL.**
- Access to the segment is only allowed if **RPL has higher-or-equal privilege** than DPL (i.e., numerically RPL ≤ DPL, since 00 is highest).

![[attachments/L3_access-rights-byte.png]]

---

## 13. Worked Example — 80286 Protected-Mode Segmentation

The **DS register = 0008H** is used as an index to pick **Descriptor 1** from the **Global Descriptor Table (GDT)**.

That descriptor row (read MSB→LSB) contains:
- **Limit** = `00FF` (2 bytes)
- **Base** = `100000` (3 bytes)
- **Access Right** = `92H` = `1001 0010` binary

This descriptor tells the CPU that the **DS register accesses memory locations 100000H–1000FFH** as a **data segment**.

![[attachments/L3_protected-mode-segmentation-80286-example.png]]

---

## 14. 64-bit Descriptor (Pentium 4 / Core 2)

- The **L bit** ("Large"/64-bit): selects **64-bit addresses** in a Pentium 4/Core2 with 64-bit extensions; `L=0` → **32-bit compatibility mode**.
- In 64-bit protected operation, the **code segment register is still used** to select a section of code from memory.
- The **64-bit descriptor has NO limit or base address field** — it only contains an **access rights byte** and control bits.
- In 64-bit mode there is no segment/limit stored in the descriptor; the base address of the segment (although not placed in the descriptor) is **00 0000 0000H**.
- ⇒ **All code segments start at address zero** for 64-bit operation, and there are **no limit checks** for a 64-bit code segment.

![[attachments/L3_64bit-p4-descriptor.png]]

---

## 15. Flat Mode Memory (Pentium 4 / Core 2, 64-bit)

- On Pentium-based computers (P4–Core 2), a **64-bit flat memory system**: "memory appears to the program as a **single contiguous address space**."
- **No real-mode support.**
- **No segmentation**, **no use of segment registers for addressing** — CS is only used to select a descriptor that defines access rights.
- **Starting address = 0000000000H**, **Ending address = FFFFFFFFFFH** → address space is **40 bits** wide.

> **Flat/linear memory model**: the CPU can directly and linearly address all available memory locations, without bank switching, segmentation, or paging schemes.

- **Single-tasking embedded applications**: flat memory is ideal — simplest interface, direct access, minimal design complexity.
- **General-purpose, multitasking systems**: flat memory must be **augmented with a memory-management scheme** (implemented via hardware + OS software) for resource allocation and protection. The flat model still gives the greatest flexibility for implementing such schemes at the physical-addressing level.

![[attachments/L3_flat-mode-memory-model-diagram.png]]

---

## 🧠 Quick Recap Table — Real Mode vs Protected Mode vs Flat Mode

| | **Real Mode** | **Protected Mode** | **Flat Mode (64-bit)** |
|---|---|---|---|
| CPUs | 8086, 8088 only | 80286 and above | Pentium 4 / Core 2 (no real-mode support) |
| Addressable memory | 1 MB | Up to 4 GB (80386+) | Full 40-bit space (2⁴⁰ bytes) |
| Segment register holds | Segment address directly | A **selector** → points to a **descriptor** | Not used for addressing; only selects access-rights descriptor |
| Segment start boundary | Only 16-byte ("paragraph") boundaries | Any address | All code segments start at address 0 |
| Protection | **None** | Yes — privilege levels, access rights | Access-rights only (no limit checks) |
| Offset size | 16-bit | 16-bit (DOS mode) or 32-bit (WIN32) | Full linear address |

---
← [[x86 Based PC|← Lecture 2]] | [[Home]]
