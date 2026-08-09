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


## RV Day 3

### RV_D3SK1 - Digital Logic Design

### RV_D3SK1_L2 - Labs for sequential calculator

This laboratory exercise focuses on the design and verification of a **combinational calculator** using **TL-Verilog** in the **Makerchip IDE**.

The calculator accepts two input values and performs four basic arithmetic operations:

- Addition
- Subtraction
- Multiplication
- Division

The operation is selected using a **2-bit encoded operation signal `$op[1:0]`**.

---

## 1. Combinational Calculator Specification

The combinational calculator consists of two 32-bit input operands and one 32-bit output.

The inputs are:

```text
$val1[31:0]
$val2[31:0]
```

The calculator performs four arithmetic operations in parallel:

```text
Addition       → $sum[31:0]
Subtraction    → $diff[31:0]
Multiplication → $prod[31:0]
Division       → $quot[31:0]
```

The `$op[1:0]` signal determines which operation result is selected and connected to `$out[31:0]`.

### Operation Encoding

| `$op[1:0]` | Operation |
|------------|-----------|
| `2'b00` | Addition |
| `2'b01` | Subtraction |
| `2'b10` | Multiplication |
| `2'b11` | Division |

Therefore:

```text
$op = 2'b00 → $out = $val1 + $val2

$op = 2'b01 → $out = $val1 - $val2

$op = 2'b10 → $out = $val1 * $val2

$op = 2'b11 → $out = $val1 / $val2
```

A division-by-zero condition is also handled in the design. If `$val2` is zero, the quotient is forced to zero.

### Combinational Calculator Architecture

```text
                 ┌─────────────────┐
                 │    Addition     │
                 │  $val1+$val2    │
                 └────────┬────────┘
                          │
                 ┌────────▼────────┐
                 │   Subtraction   │
                 │  $val1-$val2    │
                 └────────┬────────┘
                          │
                 ┌────────▼────────┐
                 │ Multiplication  │
                 │  $val1*$val2    │
                 └────────┬────────┘
                          │
                 ┌────────▼────────┐
                 │     Division    │
                 │  $val1/$val2    │
                 └────────┬────────┘
                          │
                       $op[1:0]
                          │
                    ┌─────▼─────┐
                    │  4-to-1   │
                    │    MUX    │
                    └─────┬─────┘
                          │
                       $out[31:0]
```

![Combinational Calculator](./assets/day3/day3_combinational_calculator.jpeg)

*Figure 1: RV_D3SK1_L3 laboratory specification showing the architecture of the combinational calculator and encoded operation selection.*

---



## 2. TL-Verilog Implementation

The third image shows the TL-Verilog implementation of the combinational calculator.

The test environment generates small random values using `$rand1[3:0]` and `$rand2[3:0]`.

These values are assigned to the 32-bit calculator operands.

### TL-Verilog Code

```tlv
|calc
    @0
        $val1[31:0] = $rand1[3:0];
        $val2[31:0] = $rand2[3:0];

        $sum[31:0]  = $val1 + $val2;
        $diff[31:0] = $val1 - $val2;
        $prod[31:0] = $val1 * $val2;

        $quot[31:0] = $val2 == 0 ? 32'd0 : $val1 / $val2;

        $out[31:0] =
            $op[1:0] == 2'b00 ? $sum :
            $op[1:0] == 2'b01 ? $diff :
            $op[1:0] == 2'b10 ? $prod :
                                $quot;

        // Assertions to end simulation
        *passed = *cyc_cnt > 40;
        *failed = 1'b0;
```

### Code Explanation

The first two statements assign the generated input values:

```tlv
$val1[31:0] = $rand1[3:0];
$val2[31:0] = $rand2[3:0];
```

The four arithmetic operations are then calculated:

```tlv
$sum[31:0]  = $val1 + $val2;
$diff[31:0] = $val1 - $val2;
$prod[31:0] = $val1 * $val2;
```

For division, a conditional operator is used:

```tlv
$quot[31:0] = $val2 == 0 ? 32'd0 : $val1 / $val2;
```

This prevents division by zero.

The final output is selected using `$op[1:0]`:

```tlv
$out[31:0] =
    $op[1:0] == 2'b00 ? $sum :
    $op[1:0] == 2'b01 ? $diff :
    $op[1:0] == 2'b10 ? $prod :
                        $quot;
```

This behaves like a **4-to-1 multiplexer**, where the operation selector chooses one of the four arithmetic results.

![TL-Verilog Code](./assets/day3/day3_tlv_code.jpeg)

*Figure 2: TL-Verilog implementation showing the input assignment, arithmetic operations, division-by-zero protection, output selection, and simulation completion condition.*

---

## 3. Hardware Diagram

The fourth image shows the hardware diagram generated by Makerchip.

The diagram represents the logical structure of the calculator after the TL-Verilog description is elaborated.

The two inputs are connected to the arithmetic operations:

```text
$val1 ─────────┐
               ├──► Addition
$val2 ─────────┘

$val1 ─────────┐
               ├──► Subtraction
$val2 ─────────┘

$val1 ─────────┐
               ├──► Multiplication
$val2 ─────────┘

$val1 ─────────┐
               ├──► Division
$val2 ─────────┘
```

The four results are then controlled by `$op[1:0]`.

```text
             ┌───────────┐
$sum ───────►│           │
$diff ──────►│           │
$prod ──────►│  4-to-1   ├────► $out
$quot ──────►│    MUX    │
             │           │
$op ────────►│  Select   │
             └───────────┘
```

This demonstrates an important principle of combinational logic: **the output depends only on the current input values and control signals**.

There is no storage element or clock-dependent state required for the arithmetic calculation itself.

![Hardware Diagram](./assets/day3/day3_diagram.jpeg)

*Figure 3: Makerchip-generated hardware diagram showing the combinational calculator datapath and operation selection.*

---

## 4. Waveform Verification

The fifth image shows the simulation waveform generated by the Makerchip environment.

The waveform is used to verify the behavior of the calculator over multiple simulation cycles.

The important signals include:

| Signal | Description |
|--------|-------------|
| `$rand1` | First generated random input |
| `$rand2` | Second generated random input |
| `$val1` | First calculator operand |
| `$val2` | Second calculator operand |
| `$sum` | Addition result |
| `$diff` | Subtraction result |
| `$prod` | Multiplication result |
| `$quot` | Division result |
| `$op` | Operation selector |
| `$out` | Final calculator output |

The operation selection is:

```text
$op = 2'b00 → Addition

$op = 2'b01 → Subtraction

$op = 2'b10 → Multiplication

$op = 2'b11 → Division
```

For every simulation cycle, the selected operation determines the value appearing at `$out`.

For example, if:

```text
$val1 = 10
$val2 = 5
```

then:

```text
$op = 2'b00 → $out = 15

$op = 2'b01 → $out = 5

$op = 2'b10 → $out = 50

$op = 2'b11 → $out = 2
```

The waveform allows these relationships to be observed and verified directly.

### Simulation Completion

The simulation is terminated after the cycle counter exceeds 40:

```tlv
*passed = *cyc_cnt > 40;
*failed = 1'b0;
```

This indicates successful completion of the laboratory simulation.

![Waveform](./assets/day3/day3_waveform.jpeg)

*Figure 4: Simulation waveform showing the calculator inputs, arithmetic results, operation selector, and final output during verification.*

---
