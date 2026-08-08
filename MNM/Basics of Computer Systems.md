---
tags: [lecture1, basics, cpu-history, flynn-taxonomy, pipelining, risc-cisc]
---

← [[Home]] | Next: [[x86 Based PC|Lecture 2 →]]

# Lecture 1 — Basics of Computer Systems

## 1. What is a Microprocessor?

> A **microprocessor (µP)** = a **small**, **single chip** device with **computing (data processing) capability**.

It is the **CPU** on a single chip. Internally it is made of:
- **ALU** (Arithmetic and Logic Unit) — does math/logic
- **Registers** — small fast storage inside the CPU
- **Control Unit** — generates control signals, sequences operations
- **Internal CPU interconnection** — wires joining these parts
- **Interface** — how the CPU talks to the outside world (via ALU/Registers/Control Unit/Timing signals)

![[attachments/L1_microprocessor-block-diagram.png]]

---

## 2. Historical Background (evolution of switching devices)

The whole point of this history is: **each generation was smaller, faster, cheaper, and more reliable than the last.**

### a) Vacuum Tube (earliest electronic switch)
- **Bulky**, consumed **lots of power**, **unreliable** — in large systems one tube failed every couple of hours.
- Parts: **Cathode** (heated element, emits electrons via *thermionic emission*), **Anode/Plate** (positively charged, attracts electrons) → current flows cathode→anode (conventional current is anode→cathode).

![[attachments/L1_historical-vacuum-tube.png]]

### b) Transistors (1947)
- Solid-state circuit, built from **semiconductor electronics** using **doping**.
- **MOS (Metal-Oxide Semiconductor)** transistor = a voltage-controlled switch with 3 terminals: **gate (g)**, **drain (d)**, **source (s)**.
  - **nMOS**: electrons flow from Drain→Source. `g=0` → OFF, `g=1` → ON.
  - **pMOS**: holes flow from Source→Drain. `g=0` → ON, `g=1` → OFF.
  - **PMOS ON state (Gate LOW):** holes from the P-type source/drain flow into the N-type region under the gate, forming a conductive **P-channel**; holes flow from Source (higher potential) to Drain (lower potential).

![[attachments/L1_historical-transistors-mos.png]]

### c) Integrated Circuit (1959)
- Multiple interconnected components (**transistors, resistors, capacitors**) built together on one piece of silicon.
- **Chip manufacturing flow:**
  `Silicon ingot → Slicer → Blank wafers → 20–30 processing steps → Patterned wafer → Dicer → Individual dies → Die Tester → Tested dies → Bond die to package → Packaged dies → Part Tester → Tested Packaged dies → Ship to Customers`

![[attachments/L1_historical-ic-manufacturing.png]]

- **Packaging**: Wafer (silicon, ~8 in diameter) → cut into **IC chip** (few mm to 15 mm square) → placed into a **Package**.
  - Package material: **Plastic** (thermoset/thermoplastic), **metal** (commonly *Kovar*), or **ceramic**.
  - Why package? → **resists physical breakage**, **keeps out moisture**, gives **heat resistance**.
  - Cross-section of a chip shows layers: **Aluminum (Al)** interconnect (~90nm), **Silicon oxide film (SiO₂)**, **Diffusion layer** (~1µm), **Silicon substrate** (~300µm).

![[attachments/L1_ic-packaging-crosssection.png]]

---

## 3. Moore's Law (1965)

> **Gordon Moore**, *"Cramming More Components onto Integrated Circuits"*, Electronics, 1965.

- **Claim:** component density on a chip roughly **doubles every year** (later revised to ~every 2 years).
- 1965 → 32 components, 1966 → 64, ..., 1975 → 2¹⁶.
- **Today (this course's reference point): ~2³² transistors.**

![[attachments/L1_moores-law-1965.png]]

---

## 4. Microprocessor Evolution (µP Evaluation)

Trend over generations: **smaller size, lower cost, lower power consumption, higher reliability, more versatility.**

Chronology: `4004 (4-bit) → 4040 → 8008 → 8080 (8-bit) → 8085 → 8086 (16-bit,1MB) → 8088 → 80286 (16-bit,16MB) → 80386 (32-bit,4GB) → 80486 → Pentium (P5) → RISC → Itanium (P7)`

**x86 instruction set** = the machine language instruction set first introduced with the **8086** processor (still the base of modern x86 CPUs).

### Word-size architecture comparison table

| Architecture | Typical use | Properties |
|---|---|---|
| **8-bit** | Embedded systems, legacy apps (video games) | Registers store 0–255. Codes one "octet" at a time. Addresses up to 64 KB RAM (2¹⁶). |
| **16-bit** | Embedded systems, early Personal PC | Registers store 0–65,535. **Data bus = 16 bits.** Codes 2 octets at a time. Addresses up to **1 MB** (2²⁰). • **8088**: first x86 in a PC — 16-bit *internal* architecture but **external 8-bit data bus**. • **8086**: also 16-bit internal, but its **external data bus is 16 bits** (unlike the 8088). |
| **32-bit** | PCs, Laptops | Processes 32 data bits (0 to ~4.29 billion). Addresses up to **4 GB** (2³²). Data bus = 32 bits. |
| **64-bit** | Modern computers, Servers | Handles 64-bit data (huge range). Theoretically addresses up to 16 exabytes (2⁶⁴), though OS limits are lower. **Note: all x86 processors are still 32-bit *internally*, but Pentium has a 64-bit *external* data bus.** |

![[attachments/L1_up-evolution-architecture-table.png]]

### Performance growth over time (Fig. 1.17)
Workstation performance (relative to VAX-11/780 as the yardstick) climbed steadily 1987→2003 (MIPS → DEC Alpha → Intel Pentium 4/Xeon), improving **~1.5–1.6× per year**.

![[attachments/L1_performance-increase-workstations-fig117.png]]

---

## 5. Basics of Computer Architecture

> A computer = a **"data processing machine"** made of **Hardware** (physical parts) + **Software** (programs directing the hardware).

A **bus** = a collection of signal wires connecting the components of a computer system.

The **system bus** connects **Processor ↔ Memory** and **Processor ↔ I/O**, and is made of 3 parts:

![[attachments/L1_computer-architecture-bus-diagram.png]]

| Bus | Direction | Purpose |
|---|---|---|
| **Data Bus** | **Bidirectional** | Transfers actual data CPU↔memory↔I/O. Its **width** determines transfer rate, size of internal registers, and processing power (e.g., 8086 = 16-bit data bus; 80486 = 32-bit). |
| **Address Bus** | **Unidirectional** (CPU always supplies the address) | Determines max addressable memory size. 8086: 20-bit address bus → 2²⁰ = 1 MB addressable. Pentium: 32-bit address bus → 2³² = 4 GB addressable. |
| **Control Bus** | Signals travel **both ways** (some lines bidirectional) | Carries control signals: Memory Read, Memory Write, I/O Read, Interrupt Acknowledge, etc. E.g. "Memory Read" goes CPU→memory; "Interrupt" signal comes from I/O device→CPU. |

### Worked example: Memory banking from 8086 to Core 2
Shows how memory is organized into byte-wide banks as address/data bus widths grow:
- **8088**: 20-bit address, 8-bit data → 1 MB, single 8-bit bank.
- **8086 / 80286 / 80386SX**: 24-bit address, 16-bit data → odd/even byte banks of 8 MB each (80386SL/SLC: 32 MB).
- **80386DX / 80486**: 32-bit address, 32-bit data → 4 banks × 1 GB each = 2³²/2² = **1 GB per bank**.
- **Pentium/Pro/II/III**: 32-bit address, 64-bit data → 8 banks × 512 MB each = 2³²/2³ = **512 MB per bank**.

![[attachments/L1_memory-system-8086-core2-ram.png]]

---

## 6. Flynn's Taxonomy (classifying computer architectures)

Two axes: **parallelism in the instruction stream** and **parallelism in the data stream**.

|            | **1 Instruction Stream** | **Many Instruction Streams** |
|---|---|---|
| **1 Data Stream** | **SISD** — traditional von Neumann single-CPU computer (e.g. IBM PC, workstations) | **MISD** — may be pipelined computers (e.g. Z = sin(x)+cos(x)+tan(x) computed by different processors on the same data) |
| **Many Data Streams** | **SIMD** — Vector processors, fine-grained data-parallel computers (e.g. Cray's vector processing machine, GPUs) | **MIMD** — Multi-computers / multiprocessors (e.g. Silicon Graphics machines) |

![[attachments/L1_flynns-taxonomy.png]]

![[attachments/L1_sisd-simd-misd-mimd.png]]

- **SISD**: one Control Unit → one Processor Unit ↔ one Memory Module (classic von Neumann, instruction stream + data stream both single).

---

## 7. Scalar vs Vector Processors

- **Vector processing** exploits **data parallelism**: performs the **same computation** on linear arrays of numbers ("vectors") using **one instruction**.
- **Maximum Vector Length (MVL)** = max elements a vector ISA supports (typical 64–128, range 64–4996).
- **Scalar ISA** (RISC or CISC): one instruction → 1 operation → e.g. `Add.d F3, F1, F2` (r1+r2→r3).
- **Vector ISA**: one instruction → N operations → e.g. `addv.d v3, v1, v2` operates across an entire vector register.
- Historical vector machines: **Control Data Corporation STAR-100** (1974), **Texas Instruments ASC** (1972). Modern equivalent: **GPU (Graphics Processing Unit)**.

![[attachments/L1_scalar-vs-vector-1.png]]

**Scalar processing** = loop, one add per iteration: `for i=1 to n: c[i]=a[i]+b[i]`
**Vector processing** = one instruction over the whole array: `c[1:n] = a[1:n] + b[1:n]`

![[attachments/L1_scalar-vs-vector-2.png]]

---

## 8. Pipeline vs Superpipeline vs Superscalar

| Type | Behavior |
|---|---|
| **Pipelined processor** | Performs **one pipeline stage per clock cycle**. |
| **Superpipelined processor** | Splits each pipeline stage's work into **two non-overlapping halves**, each executing in **half a clock cycle** → effectively **two pipeline stages per clock cycle**; more instructions can be "in flight" simultaneously → more parallelism. |
| **Superscalar processor** | Maintains a parallel pipeline, one stage per clock cycle, **but has multiple functional units** (e.g., several ALUs) so more than one instruction's *same stage* can be processed at once. Improves throughput of scalar instruction execution. |

![[attachments/L1_pipeline-superpipeline-superscalar.png]]

---

## 9. Multicore & Hyper-Threading

- **Hyper-Threading (Intel)**: lets **one physical core act like 2 logical/virtual processors** to the OS and applications → e.g. **2 thread engines per core**, so a **4-core CPU can run up to 8 threads simultaneously**. Analogy: like adding extra lanes to a highway — more cars (threads) move through at once.

![[attachments/L1_multicore-hyperthreading.png]]

### Hyper-Threading vs Multithreading

| Hyper-Threading | Multithreading |
|---|---|
| **Technology**: makes 1 **physical processor** appear as 2 separate (virtual/logical) processors to OS & apps | **Mechanism**: allows multiple **threads** to exist within one process, executing independently but sharing process resources |
| A physical processor is divided into two virtual/logical processors | A **process** is divided into multiple threads |

![[attachments/L1_hyperthreading-vs-multithreading-table.png]]

### Advances in Processor Microarchitecture (timeline)
`Single-Cycle Processor → Pipelined Processor → Deep-Pipelined Processor (≈20 stages) → Superscalar Processor → Out-of-Order Processor (Intel Pentium Pro 1995, MIPS R10000 1996) → Multi-Core Processor`
- Out-of-order execution was introduced to **solve dependency problems** created by pipelined superscalar processors.

![[attachments/L1_processor-microarchitecture-advances.png]]

### In-Order vs Out-of-Order Execution

| In-Order Execution | Out-of-Order Execution |
|---|---|
| Executes instructions in the **same order** they appear in code | Can execute instructions in a **different order** than written, as long as the final result is correct |
| Simpler control logic, fewer hardware resources | More hardware needed: instruction scheduling, dependency checking, **register renaming**, **reorder buffer** |
| Limits parallelism/throughput — must wait for prior instructions to finish | Exploits more parallelism/throughput by reordering, overlapping, or skipping instructions based on dependencies |
| Best for **low-power, low-cost, embedded** processors | Best for **high-performance, general-purpose** processors |

### Multicore vs Multiprocessor
- **Multicore**: one physical **Processor** chip contains multiple **Cores**, each with its own CPU + L1 cache, sharing an on-chip **L2 cache**, connected to system memory.
- **Multiprocessor**: **two+ separate physical processor chips** (each itself possibly multicore), connected via a **System Bus** to shared system memory.

![[attachments/L1_multicore-vs-multiprocessors.png]]

### Multi-core Architecture (structural view)
- **Single-Core**: one register file + ALU ("the single core") inside the CPU chip, connected via bus interface → system bus → I/O bridge → main memory / I/O bus (USB, graphics, disk controllers).
- **Multi-Core**: the CPU chip **replicates multiple processor cores** (each with its own register file + ALU) on a **single die**, all sharing one **bus interface**.

![[attachments/L1_multicore-architecture-diagram.png]]

---

## 10. The Instruction Set Architecture (ISA)

> The **ISA** is the part of the architecture **visible to the programmer**. It defines:
> - **Operations** — how many, which ones
> - **Operands** — how many, where located
> - **Number and types of registers**
> - **Instruction formats** — size, layout
> - **Storage access / addressing modes**
> - **Exceptional conditions** (interrupts, errors)

Modern ISAs: **x86/Pentium, PowerPC, DEC Alpha, MIPS**.
Example: `y = x + b` → assembly `add r1, r2, r5`

### CISC vs RISC

| CISC | RISC |
|---|---|
| Emphasis on **hardware** | Emphasis on **software** |
| Multiple instruction **sizes and formats** | Instructions of the same set, **few formats** |
| **Fewer registers** | **More registers** |
| **More addressing modes** | **Fewer addressing modes** |
| Extensive use of **microprogramming** | Complexity pushed into the **compiler** |
| Instructions take a **varying** amount of cycle time | Instructions take **one cycle time** |
| **Pipelining is difficult** | **Pipelining is easy** |

![[attachments/L1_cisc-vs-risc-table.png]]

*(Recall from Lecture 3: the 8086 is explicitly called out as CISC with **variable instruction size**, e.g. `MOV AX,BX` = 1 byte vs `MOV BX,4050H` = 3 bytes.)*

---

## 11. From Source Code to Executable File

1. **Source file** (assembly/C) → **Assembler** → **Object file**
2. Multiple object files + a **Program library** → **Linker** → **Executable file**

**Object file** = what you get compiling one or several source files. Can be a full executable, a library, or an intermediate file. Contains: **native code, linker information, debugging symbols**, etc.
An **Object file** structure (UNIX assembler) has 6 sections: `Object file header | Text segment | Data segment | Relocation information | Symbol table | Debugging information`.

- **Symbolic Address**: names used in source code.
- **Relocatable Address**: addresses in the object file, not yet fixed, resolved later.
- The **Linker** searches object files + program libraries (e.g., the C library for `printf`), resolves cross-references (`jal ???` in the object file becomes `jal printf` / `jal sub` in the final executable), and combines everything into one executable file.

![[attachments/L1_source-to-executable-file.png]]

---

## 12. Abstraction

> **Abstraction** = investigating into the depths to reveal more information, **but** an abstraction **omits "unneeded" detail** to help cope with complexity.

Pipeline of abstraction layers, from complex/unstructured down to simple/structured:

```
High-level language program (C)  →  [C compiler]  →  Assembly language program (MIPS)  →  [Assembler]  →  Binary machine language program (MIPS)
```
- **C code** is complex & unstructured (human-readable, high abstraction).
- **Assembly** is simple & structured (one operation per line, close to hardware).
- **Binary machine code** is what the CPU actually executes (0s and 1s).

Each layer **hides the detail of the layer below it**, letting the programmer work at a higher, more manageable level without worrying about literal transistor/bit-level operations.

![[attachments/L1_abstraction-diagram.png]]

---
← [[Home]] | Next: [[x86 Based PC|Lecture 2 — x86 Based PC →]]
