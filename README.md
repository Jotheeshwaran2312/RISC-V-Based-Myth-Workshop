# RISC-V Based MYTH Workshop

This repository documents the comprehensive logs, architectural deep-dives, and hands-on laboratory exercises completed during the RISC-V Based MYTH Workshop.

---

## RV Day 1 - Introduction to RISC-V ISA and GNU compiler toolchain

### RV-D1SK1 - Introduction to RISC-V basic keywords

#### RV_D1SK1_L1_Introduction
The workshop outlines the complete open-source chip design flow, illustrating the connection between high-level application software and structural silicon layout.

* **RISC-V Architecture:** Translates software programs (like a C code script) via a GNU toolchain compiler into localized hardware execution parameters using standard RISC-V assembly language instruction sets.
* **Hardware Implementation:** Utilizes a parameterized HDL description framework (such as the `picorv32` CPU core structure) to define the register logic gates.
* **Physical Layout:** Converts the verified RTL layout into discrete physical logic components (like standard cell blocks) via an open-source synthesis implementation runner (`qflow`).

##### 📸 Flow Mapping Output
![RISC-V Inception Mapping](./assets/day1/day%201%20introduction.png)
*Figure 1: Complete hardware-software flow abstraction showing code processing levels from compiler execution down to structural standard cell layouts.*

---

## RV Day 3 - Combinational Logic

### RV_D3SK1 - Digital Logic Design

#### RV_D3SK1_L3 - Combinational Logic Lab

This laboratory exercise focuses on designing and verifying a combinational calculator using TL-Verilog in the Makerchip IDE.

The design demonstrates how two input values can be processed using four basic arithmetic operations:

- Addition
- Subtraction
- Multiplication
- Division

The operation is selected using a 2-bit encoded control signal `$op[1:0]`.

---

### 1. Combinational Calculator Specification

The first image shows the laboratory specification for the combinational calculator.

The calculator accepts two 32-bit input operands, `$val1[31:0]` and `$val2[31:0]`, and produces a 32-bit output `$out[31:0]`.

Four arithmetic operations are implemented in parallel:

- `$sum` - Addition
- `$diff` - Subtraction
- `$prod` - Multiplication
- `$quot` - Division

The `$op[1:0]` signal selects which arithmetic result is connected to the final output.

#### Operation Encoding

| `$op[1:0]` | Operation |
|------------|-----------|
| `2'b00` | Addition |
| `2'b01` | Subtraction |
| `2'b10` | Multiplication |
| `2'b11` | Division |

The division operation includes a zero-divisor check to avoid invalid division.

```text
$op = 2'b00  →  $out = $val1 + $val2
$op = 2'b01  →  $out = $val1 - $val2
$op = 2'b10  →  $out = $val1 * $val2
$op = 2'b11  →  $out = $val1 / $val2

