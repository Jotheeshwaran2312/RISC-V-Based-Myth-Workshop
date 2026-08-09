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

---

## RV Day 3 - Combinational Logic

### RV_D3SK1 - Labs

#### RV_D3SK1_L3_Labs - Combinational Logic

The laboratory exercise focuses on the implementation of a **combinational calculator** using **TL-Verilog and the Makerchip IDE**.

The calculator accepts two input values and performs four arithmetic operations:

- Addition
- Subtraction
- Multiplication
- Division

The required operation is selected using a **2-bit encoded operation signal**.

---

##### 1. Combinational Calculator - Lab Specification

The first image shows the laboratory specification for the combinational calculator.

The circuit contains two 32-bit input values, `$val1[31:0]` and `$val2[31:0]`, which are supplied to four arithmetic blocks:

- Addition
- Subtraction
- Multiplication
- Division

Each arithmetic block produces an individual result. The 2-bit `$op[1:0]` signal is used as an encoded select input to choose one of these results and drive the final `$out[31:0]`.

The operation encoding is:

| `$op[1:0]` | Operation |
|------------|-----------|
| `2'b00` | Addition |
| `2'b01` | Subtraction |
| `2'b10` | Multiplication |
| `2'b11` | Division |

![Combinational Calculator](assets/day3/day3_combinational_calculator.png)

*Figure 1: RV_D3SK1_L3 laboratory specification showing the architecture of the combinational calculator.*

---

##### 2. Makerchip IDE

The second image shows the **Makerchip IDE** used to implement and simulate the design.

Makerchip provides an interactive environment for developing Verilog and TL-Verilog designs and visualizing their simulation behavior. The design can be edited, compiled and inspected using the diagram and waveform views.

![Makerchip IDE](assets/day3/day3_makerchip_ide.png)

*Figure 2: Makerchip IDE environment used for implementing and simulating the combinational calculator.*

---

##### 3. TL-Verilog Implementation

The third image shows the TL-Verilog implementation of the calculator.

The input values are generated using small 4-bit random values and assigned to 32-bit signals:

```tlv
$val1[31:0] = $rand1[3:0];
$val2[31:0] = $rand2[3:0];
