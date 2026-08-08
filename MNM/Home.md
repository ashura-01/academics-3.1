---
tags: [exam-notes, microprocessor, CSE3117]
---

# CSE3117 — Microprocessor & Microcontroller: Exam Notes

Course: **Architecture, Programming, and Interfacing (8086 to Pentium)** — Prof. Dr. Shamim Akhter, AUST

These notes are built from your 3 lecture slide decks and reorganized to actually make sense as a story, with every diagram embedded. Read them in this order:

1. [[Basics of Computer Systems|📘 Lecture 1 — Basics of Computer Systems]]
   History of computing hardware → Moore's Law → bus architecture → Flynn's taxonomy → pipelining/superscalar → multicore → RISC vs CISC → compilation pipeline.

2. [[x86 Based PC|📗 Lecture 2 — x86 Based Personal Computer]]
   Motherboard, chipsets (North/South Bridge, Intel Hub), buses (ISA/PCI/PCIe/USB/SATA), RAM modules, BIOS, PC memory map (real mode & Windows XP).

3. [[8086 Architecture|📙 Lecture 3 — The Architecture of 8086]]
   EU/BIU internal design, registers, the FLAG register bit-by-bit, real-mode segmented addressing (with worked examples), protected-mode descriptors, 64-bit flat memory.

---

## 🎯 Fast pre-exam revision checklist

- [ ] Can you draw the 8086 internal block diagram (EU + BIU) from memory?
- [ ] Can you list all 9 flags of the 8086 basic FLAG register and what each does?
- [ ] Can you compute a **physical address** from Segment:Offset (EA = SR×10H + offset)?
- [ ] Do you know why the instruction queue is **6 bytes**, and what "pipelining" means here?
- [ ] Can you explain **real mode vs protected mode** addressing differences?
- [ ] Do you know what a **descriptor** contains (Base, Limit, Access Rights) and how G bit changes the Limit?
- [ ] Can you name North Bridge vs South Bridge responsibilities?
- [ ] Can you compare **ISA vs PCI vs PCIe vs USB** bandwidths?
- [ ] Can you compare **CISC vs RISC**?
- [ ] Can you explain **pipeline vs superpipeline vs superscalar**?
- [ ] Do you know **Flynn's taxonomy** (SISD/SIMD/MISD/MIMD) with an example machine for each?

---
*All diagrams are stored in `attachments/` and embedded inline below — nothing external needed, works fully offline in Obsidian.*
