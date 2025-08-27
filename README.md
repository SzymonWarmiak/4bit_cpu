# 4-bit CPU in Logisim Evolution

This repository contains a custom **4-bit CPU** designed and implemented in **Logisim Evolution**.  
The processor demonstrates fundamental digital design concepts: registers, arithmetic logic unit (ALU), program counter, memory, and a custom microinstruction format.  
It is capable of executing simple programs stored in ROM, using RAM for data, and performing conditional jumps based on flags.

---

## Block Diagram

The high-level architecture of the CPU is shown below:

![Block Diagram](docs/block_diagram.png)

Key components:
- **ROM** – holds program instructions (microinstructions, 32 bits wide).
- **Program Counter (PC)** – selects the current instruction.
- **Control Unit (CU)** – decodes the instruction and generates control signals.
- **ALU** – performs arithmetic and logical operations.
- **Registers** – store intermediate values and flags.
- **RAM** – stores data and mapped I/O.
- **Bus** – 4-bit data path connecting all units.

---

## Main CPU

The top-level integration of all components:

![Main CPU](docs/main_cpu.png)

Features:
- Central 4-bit bus
- Separate modules for ROM, RAM, ALU, and I/O
- Control signals from ROM drive the entire datapath
- LED and button interface mapped through I/O registers

---

## ROM

Program memory is implemented as a microcoded ROM.  
Each instruction is **32 bits**, with dedicated fields for ALU control, register read/write, memory access, jump address, and end flag.

![ROM](docs/rom_memory.png)

Instruction format:

DATA | R_READ | R_WRITE | CF1F2 | STATE | RAM_ADR | RAM_R | RAM_WRITE | RAM_EN | JMPADR | END


- **DATA** – immediate constant
- **R_READ / R_WRITE** – select registers
- **CF1F2** – ALU operation (00 ADD, 01 AND, 10 OR, 11 XOR)
- **STATE** – bus source control
- **RAM fields** – data and I/O access
- **JMPADR** – conditional/unconditional jump target
- **END** – signals the end of the program and resets PC

---

## RAM

Random-access memory is implemented using registers with enable and address decoding logic.

![RAM](docs/ram.png)

- 16×4-bit RAM cells
- Supports `READ`, `WRITE`, `ENABLE` signals
- Shares address space with I/O (memory-mapped)

---

## ALU

The arithmetic logic unit is built from a **1-bit slice**, replicated to handle 4-bit data.  
Operations are selected by control signals `F1 F2`.

![ALU 1-bit](docs/alu_1bit.png)

Supported operations:
- **00 – ADD**
- **01 – AND**
- **10 – OR**
- **11 – XOR (COMPARE)**

Flags (Zero, Carry) are latched into a separate register and used for conditional jumps.

---

## Programs

Programs are stored in ROM as 32-bit words written in hexadecimal format.  

### Example: Blink

10006F00
00006F00
10006F00
00006F0F

This program toggles an LED connected to the I/O port.

### Example: Compare and Jump

F0100000 ; Load A with 1111
00208F00 ; Load B with input from I/O
08334000 ; XOR (compare) A and B, update flags
08000040 ; Jump to address 0x04 if Zero flag set
0000000F ; End (reset if not equal)
A0006F00 ; Output 1010 on LEDs
0000000F ; End

## Repository Structure

├── cpu.circ # Main Logisim Evolution circuit
├── docs/ # Block diagrams and screenshots
│ ├── block_diagram.png
│ ├── main_cpu.png
│ ├── rom_memory.png
│ ├── ram.png
│ └── alu_1bit.png
├── programs/ # Example programs in HEX
└── README.md

## Future Work

- Extend ALU with subtraction and shift operations
- Add stack and subroutine support
- Implement interrupt handling
- Create a Python-based assembler for easier programming
