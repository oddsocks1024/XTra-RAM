# XTra-RAM

## Description
512K RAM Expansion (8bit ISA) for XT class PCs and earlier with configurable banks

**Features**
* 512K RAM
* 8 x 64K banks
* Each bank can be independently configured as:
  * Low Address
  * High Address
  * Disabled

## Bill of Materials

| Qty | Component                        |
|:---:|----------------------------------|
| 16  | 10KΩ Axial Resistor - ¼ watt     |
| 1   | 74LS245 20pin DIP                |
| 2   | 74LS157 16pin DIP                |
| 1   | 74LS151 16pin DIP                |
| 1   | AS6C4008-55PCN SRAM 32pin DIP    |
| 5   | 100nF Ceramic Capacitors         |
| 2   | 47uF Electrolytic Capacitors     |
| 1   | 10uF Electrolytic Capacitor      |
| 2   | 16pin DIP sockets (optional)     |
| 1   | 20pin DIP socket (optional)      |
| 1   | 32pin Wide DIP socket (optional) |

---

## Usage
To configure the XTra-RAM correctly, it is important to have a basic understanding of the PC memory map, especially conventional memory.
It is also important to understand how much RAM you currently have installed, including on expansion cards.

The XTra-RAM contains 512K which is split into 8 x 64K memory banks. Each bank can either be mapped to a low address, a high address or disabled entirely.
This gives the card the best chance of working with many difference memory configurations.

**Remember**

* RAM should be mapped contiguously into the address space. If there's a gap in the address space where no RAM is mapped, the PC will see no or less RAM.
* Never configure a memory bank on the XTra-RAM at both a high and low address. This will cause the same RAM to appear twice in the address space leading to memory corruption.
* Do not configure a memory bank on the XTra-RAM to be at the same address space as existing memory in the system, including other RAM expansions. This will cause a memory conflict.
* Be careful if mapping memory blocks into UMA, it requires much diligence than conventional memory and may conflict with your BIOS.


### DIP Switches

| Memory Bank  | Enable Bank High      | Enable Bank Low       | Disable Bank	        | High Map Address      | Low Map Address |
|:------------:|-----------------------|-----------------------|------------------------|-----------------------|-----------------|
| 8            | SW2-8: ON, SW1-8: OFF | SW2-8: OFF, SW1-8: ON | SW2-8: OFF, SW1-8: OFF | F0000h - FFFFFh (UMA) | 70000h - 7FFFFh |
| 7            | SW2-7: ON, SW1-7: OFF | SW2-7: OFF, SW1-7: ON | SW2-8: OFF, SW1-8: OFF | E0000h - EFFFFh (UMA) | 60000h - 6FFFFh |
| 6            | SW2-7: ON, SW1-7: OFF | SW2-6: OFF, SW1-6: ON | SW2-8: OFF, SW1-8: OFF | D0000h - DFFFFh (UMA) | 50000h - 5FFFFh |
| 5            | SW2-7: ON, SW1-7: OFF | SW2-6: OFF, SW1-6: ON | SW2-8: OFF, SW1-8: OFF | C0000h - CFFFFh (UMA) | 40000h - 4FFFFh |
| 4            | SW2-7: ON, SW1-7: OFF | SW2-6: OFF, SW1-6: ON | SW2-8: OFF, SW1-8: OFF | B0000h - BFFFFh (UMA) | 30000h - 3FFFFh |
| 3            | SW2-7: ON, SW1-7: OFF | SW2-6: OFF, SW1-6: ON | SW2-8: OFF, SW1-8: OFF | A0000h - AFFFFh (UMA) | 20000h - 2FFFFh |
| 2            | SW2-7: ON, SW1-7: OFF | SW2-6: OFF, SW1-6: ON | SW2-8: OFF, SW1-8: OFF | 90000h - 9FFFFh       | 10000h - 1FFFFh |
| 1            | SW2-7: ON, SW1-7: OFF | SW2-6: OFF, SW1-6: ON | SW2-8: OFF, SW1-8: OFF | 80000h - 8FFFFh       | 00000h - 0FFFFh |


**NOTE:** Some systems have configuration jumpers on the motherboard which limit the amount of memory the BIOS will detect and this may prevent all or some of the additional memory from being seen. Consult the documentation for your system. 


### Example 1

An IBM 5150, populated with 64K on the motherboard
With the XTra-RAM we can provide a total of 576K, as follows:

* Bank 1 must be configured as High (if it was low, it would conflict with the motherboard memory)
* Banks 2 - 8 must be configured as Low

Resulting Memory Map

| Address         |                    |             |
|:---------------:|:------------------:|:-----------:|
| 00000h - 0FFFFh | Motherboard Memory | 0K - 64K    |
| 10000h - 1FFFFh | XTra-RAM Bank 2    | 64K - 128K  |
| 20000h - 2FFFFh | XTra-RAM Bank 3    | 128k - 192K |
| 30000h - 3FFFFh | XTra-RAM Bank 4    | 192K - 256K |
| 40000h - 4FFFFh | XTra-RAM Bank 5    | 256K - 320K |
| 50000h - 5FFFFh | XTra-RAM Bank 6    | 320K - 384K |
| 60000h - 6FFFFh | XTra-RAM Bank 7    | 384K - 448K |
| 70000h - 7FFFFh | XTra-RAM Bank 8    | 448K - 512K |
| 80000h - 8FFFFh | XTra-RAM Bank 1    | 512K - 576K |


### Example 2

An IBM 5150, populated with 64K on the motherboard and an IBM 256K RAM card (total 320K).

With the XTra-RAM we can provide a total of 640K, as follows:

* Bank 1 must be configured as High - If  low, it would conflict with the motherboard memory
* Bank 2 must be configured as High - If low, it would conflict with the IBM RAM Card
* Banks 3 - 5 must (* see note) be disabled - if low, they would conflict with the IBM RAM Card, if high they would be in the UMA area
* Banks 6 - 8 must be configured as Low

| Address         |                    |             |
|:---------------:|:------------------:|:-----------:|
| 00000h - 0FFFFh | Motherboard Memory | 0K - 64K    |
| 10000h - 1FFFFh | IBM RAM Card       | 64K - 128K  |
| 20000h - 2FFFFh | IBM RAM Card       | 128k - 192K |
| 30000h - 3FFFFh | IBM RAM Card       | 192K - 256K |
| 40000h - 4FFFFh | IBM RAM Card       | 256K - 320K |
| 50000h - 5FFFFh | XTra-RAM Bank 6    | 320K - 384K |
| 60000h - 6FFFFh | XTra-RAM Bank 7    | 384K - 448K |
| 70000h - 7FFFFh | XTra-RAM Bank 8    | 448K - 512K |
| 80000h - 8FFFFh | XTra-RAM Bank 1    | 512K - 576K |
| 90000h - 8FFFFh | XTra-RAM Bank 2    | 576K - 640K |

* It is possible to configure and use RAM in the UMA area, but this is very system configuration specific.


### Example 3

An XT, populated with 512K on the motherboard.

With the XTra-RAM we can provide a total of 640K, as follows:

* Banks 1 - 2 must be configured as High - If low, it would conflict with the motherboard memory
* Banks 3 - 8 must (* see note) be disabled - if low, they would conflict with the motherboard memory, if high, they would be in the UMA area

| Address         |                    |             |
|:---------------:|:------------------:|:-----------:|
| 00000h - 0FFFFh | Motherboard Memory | 0K - 64K    |
| 10000h - 1FFFFh | Motherboard Memory | 64K - 128K  |
| 20000h - 2FFFFh | Motherboard Memory | 128k - 192K |
| 30000h - 3FFFFh | Motherboard Memory | 192K - 256K |
| 40000h - 4FFFFh | Motherboard Memory | 256K - 320K |
| 50000h - 5FFFFh | Motherboard Memory | 320K - 384K |
| 60000h - 6FFFFh | Motherboard Memory | 384K - 448K |
| 70000h - 7FFFFh | Motherboard Memory | 448K - 512K |
| 80000h - 8FFFFh | XTra-RAM Bank 1    | 512K - 576K |
| 90000h - 8FFFFh | XTra-RAM Bank 2    | 576K - 640K |


* It is possible to configure and use RAM in the UMA area, but this is very system configuration specific.

## Support Me
* [My Projects](https://projects.amiga-hardware.com) - Donate on this page
* [Order the XTra-RAM PCB]()
