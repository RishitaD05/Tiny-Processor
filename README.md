# Tiny FSM-Controlled Processor (4-bit ISA) | Verilog FPGA Project

## 📌 Project Overview
This project implements a **Tiny FSM-Controlled Processor** using **Verilog HDL**, designed for deployment on the **Boolean Board (Xilinx Spartan-7 FPGA)**.  
The processor executes instructions stored in a ROM using a **4-bit opcode-only Instruction Set Architecture (ISA)** and performs arithmetic operations on three 4-bit operands taken from FPGA slide switches.

The computed output is displayed on **8 on-board LEDs**.

This project demonstrates the complete relationship between:
- **ISA (Instruction Set Architecture)**
- **Control FSM (Finite State Machine)**
- **Datapath (Registers + ALU)**

---

## 🎯 Objectives
- Design a ROM-based processor with a **4-bit ISA**
- Implement a multi-cycle **Fetch → Decode → Execute → Output/Halt** architecture
- Implement a **Moore FSM** control unit to control datapath operations
- Perform arithmetic operations using an **8-bit internal datapath**
- Display results on FPGA **LED[7:0]**
- Validate functionality using a Verilog testbench
- Implement and demonstrate the design on Spartan-7 FPGA

---

## 🏗️ Hardware Platform
- **Board:** Boolean Board  
- **FPGA:** Xilinx Spartan-7 (XC7S50)

---

## ⚙️ Instruction Set Architecture (ISA)

| Opcode | Mnemonic | Operation |
|--------|----------|-----------|
| 0000 | LOADIN | A_reg ← A, B_reg ← B, C_reg ← C |
| 0001 | MAC1 | RR ← (A_reg × B_reg) + C_reg |
| 0010 | MAC2 | RR ← A_reg + (B_reg × C_reg) |
| 0011 | ADD3 | RR ← A_reg + B_reg + C_reg |
| 0100 | OUT | LED ← RR |
| 1111 | HALT | Stop processor execution |

All operations use **8-bit unsigned arithmetic**.

---

## 🔄 Processor Execution Cycle
The processor follows a multi-cycle instruction flow:

1. **FETCH**  
   - IR ← ROM[PC]

2. **DECODE**  
   - Opcode is decoded  
   - PC ← PC + 1

3. **EXECUTE**  
   - Operation performed depending on instruction

4. **OUT / HALT**
   - OUT transfers RR to output register and drives LED output  
   - HALT freezes PC and stops execution until reset

---

## 🧩 Processor Architecture
The processor consists of the following major blocks:

- **Instruction ROM (8 × 4-bit)**
- **Program Counter (PC) (3-bit)**
- **Instruction Register (IR) (4-bit)**
- **Datapath Registers**
  - A_reg, B_reg, C_reg (8-bit operand registers)
  - RR (8-bit Result Register)
  - OUT_reg (8-bit Output Register)
- **ALU / Arithmetic Unit**
  - Supports multiplication and addition operations
- **Moore FSM Control Unit**
  - Controls fetch, decode, execute, output, and halt stages

---

## 🔌 Input / Output Mapping

### Inputs
| Signal | Width | Description |
|--------|-------|-------------|
| clk | 1-bit | System clock |
| reset | 1-bit | Synchronous active-high reset |
| SW_A | 4-bit | Operand A from switches |
| SW_B | 4-bit | Operand B from switches |
| SW_C | 4-bit | Operand C from switches |

### Outputs
| Signal | Width | Description |
|--------|-------|-------------|
| LED | 8-bit | Output result shown on LEDs |
| done | 1-bit | Pulses HIGH during OUT execution |

---

## 📍 Top-Level Module Interface

```verilog
module tiny_processor (
  input        clk,
  input        reset,
  input  [3:0] SW_A,
  input  [3:0] SW_B,
  input  [3:0] SW_C,
  output [7:0] LED,
  output       done
);
