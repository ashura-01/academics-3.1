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

#### Block stripping

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