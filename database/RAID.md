---
title: Database RAID (Redundant array of independent Disks)
tags:
  - database
  - Raid
  - final
date: 2026-10-17
---
# RAID --  Redundant arrays of independent disks

disk organization technique that manages a larger number of disks, providing a view of a single disk

**Advantages**
- high capacity and high speed using multiple disks in parallel
- high reliability; redundant data storage, so that data can be recovered if a disk fails

>[!important] Multiple levels of RAID
>-> different levels of redundancy
>-> Error checking
>-> Cost & capacity

### Raid level-0

**characteristics**
- breaks a file into blocks of data 
- called block striping 
- store block across disks in the system
- simple to implement 
- provides no redundancy or error detection

**Example**

![[raid1|800]]

**Block stripping**

* with `n` disks block i of file goes to click `(i mod n)+1`
* if uses the $\lfloor i/n \rfloor$ th physical block of the disk to store logical block `i`

**Disk Number**: $$\text{Disk Number} = (i \bmod n) + 1$$
**Physical Block Index**: $$\text{Physical Block} = \lfloor i / n \rfloor$$

>[!question] **example**:
>For a system with $n = 8$ **disks**, to determine where **Logical Block 11** is stored2:
>disk number $$\text{disk number}=(11 \bmod 8)+1=4$$
and  physical block index $$\text{physical bloack} = \lfloor 11/8 \rfloor=1$$
so the answer is 



### Raid level-1

- a complete file is stored on a single disk
- a second disk contains an exact copy of the file 
- provides complete redundancy of data, no parallel

![[Drawing 2026-10-17 18.44.43.excalidraw|800]]

probability of combined failure is very small
-> Builds collapse
-> electrical outage

>[!important] **RAID 1+0/ RAID 10**-> Best of both level 0 and level 1(*Block level striping*)
>refers to monitoring with striping and use the term RAID level 1 to refer to mirroring without striping
>
>```c
>mirroiring → a data storage technique in which the same data is stored on two or more disks to provide data redundancy & fault tolerance
>```

### RAID level 2

- uses bit level stripping combined with error correcting code (ECC)
- each bit of data is stored in a parity bit, a parity bit is associated with each bit of data
- if the bits in byte contains even no 1 -> **parity 0**
- if the bits in byte contains odd no 1 -> **parity 1**
- ECC scheme -> hamming code

### RAID level 3

bit interleaved level parity organization, improves on level 2 by exploiting the fact that disk controllers unlike memory, systems, can detect whether a sector has been read correctly.
a single parity bit is usual for with error detection and correction

> Data is  divided into byte level and stored in multiple disk & one disk is used to store the **Parity**


![[raid3|800]]

**Example**

File  contains 1 0 1 1
parity =  d1$\oplus$ d2 $\oplus$ d3 $\oplus$ d4 = 1 $\oplus$ 0 $\oplus$ 1 $\oplus$ 1 = 1

when it is corrupted we need to update it 

### RAID Level 4

**bloack-interleaved parity organization.**

uses block-level striping like `RAID 0` and in adddition keeps a parity blocl on a separate disk for corresponding blacks from n other disks

if one of the disk fails, the parity block can be used with the corresponding block from the other disks to restore the block of the failed disk.

### RAID level 5

- block level stripping with distributed parity across all disks to improve performance and provide fault tolerance
- all disks can participate in data read unlike level of  where parity disks can not participate
- widely used in real life systems

> data are divided by blocks and stored in  disks, and parity presents 

**EXAMPLE**

![[Drawing 2026-10-17 20.35.08.excalidraw|800]]
### RAID level 6

- parity bits are stored
- block level striping with dual distributed parity 
- two independent parity blocks for protection against two simultaneous disk failures

> 
> **P : parity** -> `XOR` parity
> **Q: parity** -> `Solomon codes`
> 

- parity blocks are distributing across all disks
- advance storage fault tolerance

![[Drawing 2026-10-17 20.50.30.excalidraw|800]]

