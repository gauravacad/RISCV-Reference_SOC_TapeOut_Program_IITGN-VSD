
## Welcome to Day 4 of the RTL Workshop! 
# Day 4: Gate-Level Simulation (GLS), Blocking vs. Non-Blocking in Verilog, and Synthesis-Simulation Mismatch

Day-4’s session focuses on three essential topics in the digital design that is :
- **Gate-Level Simulation (GLS)**
- **Blocking vs. Non-Blocking Assignments in Verilog**
- **Synthesis-Simulation Mismatch**

We’ll learn both the theory in first part and practical implications, complete with hands-on labs in second part onwards for deeper understanding.
Here’s is the practical, compact guide to Gate-Level Simulation (GLS) so, we can run it confidently and avoid the usual gotchas.
---

## Table of Contents

- [1. Gate-Level Simulation (GLS)](#1-gate-level-simulation-gls)
- [2. Synthesis-Simulation Mismatch](#2-synthesis-simulation-mismatch)
- [3. Blocking vs. Non-Blocking Assignments in Verilog](#3-blocking-vs-non-blocking-assignments-in-verilog)
  - [3.1 Blocking Statements](#31-blocking-statements)
  - [3.2 Non-Blocking Statements](#32-non-blocking-statements)
  - [3.3 Comparison Table](#33-comparison-table)
- [4. Labs](#4-labs)
- [5. Summary](#5-summary)
- [6. Conclusion](#6-conclusion)

---

## 1. Gate-Level Simulation (GLS)

**GLS** stands for **Gate-Level Simulation**. Gate Level Simulation (GLS) is a crucial step in the digital design verification process, occurring after the synthesis of the Register Transfer Level (RTL) code. 

- Functional correctness
- Timing behavior
- Power estimates
- Test structures (e.g., scan chains for DFT)

### How is it different from RTL simulations ?

- Unlike RTL simulations, which typically operate in a zero-delay environment, GLS can incorporate timing delays. 
- These delays can be specified using *Standard Delay Format* (`SDF`) files, allowing for more accurate modeling of real-world behavior.
- **Low-Level Design Aberrations:**: It consider individual gate interactions, timing delays, and other low-level design attributes.

### Significance of performing GLS?

- **Synthesis Validation**: Ensures and Verify that synthesis didn’t break functionality.
- **Timing Verification**: Simulates with realistic delays (from `SDF`:*Standard Delay Format* files), allowing you to check for timing violations (e.g., glitches, hold violations, non-reproducible metastability maskings,     and timing errors, etc. ).
- **Testability**: Confirms that scan chains and other test features work post-synthesis.
- **Exercise gated clocks**, cell libraries, resets, and power pins as they exist in silicon.


### When is GLS Performed?

- **After synthesis**: Once the RTL is converted into a gate-level netlist.
- **Before physical design**: To catch issues early, before layout.

### Types of GLS

- **Functional GLS**: Logic-only simulation, often with zero or unit delays.
- **Timing GLS**: Uses annotated timing data to check real-world timing behavior.

### How to annotate SDF (concept)?
- SDF files are applied to a module/instance to add delays. 
- The simulator will have a dedicated option/command to annotate SDF (or an API call inside the TB).

### What are some of the challenges ?
As they possess detailed representation of gates and their interactions.

- GLS simulations are inherently more complex than RTL simulations.
- - Need longer simulation times and higher memory requirements.
- Debugging issues are challenging due to potential initialization problems and X propagation (unknown states).


## Requirement of Files
- Need verilog models of standard cell library 
- Netlist of the design 
- Test bench of design
- Vcd File 
**Caution:** Make sure timescale in `SDF` and `HDL` **match** (or adjust units).
---

## 2. Synthesis-Simulation Mismatch

A **synthesis-simulation mismatch** occurs when the simulation results of RTL (pre-synthesis) do not match simulation results of the gate-level netlist (post-synthesis) or hardware. Reasons include:

- **Non-synthesizable constructs**: Use of delays, initial blocks, or other code not supported by synthesis.
- **Incomplete or ambiguous coding**: E.g., missing `else` clauses, improper sensitivity lists.
- **Tool interpretation differences**: Simulation and synthesis tools may interpret ambiguous RTL differently.

**Key Point:** Always write synthesizable, unambiguous RTL and follow good coding practices to minimize mismatches.

---

## 3. Blocking vs. Non-Blocking Assignments in Verilog

Verilog offers two types of procedural assignments:

### 3.1 Blocking Statements (`=`)

- **Syntax:** `=`
- **Execution:** Sequential, executes immediately.
- **Suitable for:** Combinational logic (e.g., `always @(*)`).
- **Example:**  
  ```verilog
  always @(*) y = a & b;
  ```

### 3.2 Non-Blocking Statements (`<=`)

- **Syntax:** `<=`
- **Execution:** Scheduled, executes concurrently at the end of the time step.
- **Suitable for:** Sequential logic (e.g., `always @(posedge clk)`).
- **Example:**  
  ```verilog
  always @(posedge clk) q <= d;
  ```

### 3.3 Comparison Table

| **Blocking (`=`)**                        | **Non-Blocking (`<=`)**                   |
|-------------------------------------------|--------------------------------------------|
| Uses `=` operator                         | Uses `<=` operator                         |
| Sequential, immediate execution           | Concurrent, scheduled at end of timestep   |
| Updates happen instantly in code order    | Updates applied after time step            |
| For combinational logic, temp variables   | For sequential logic, registers/flip-flops |
| Infers combinational logic (gates)        | Infers sequential logic (flip-flops)       |

---

## 4. Labs

### Lab 1: Ternary Operator MUX

Verilog code for a simple 2:1 multiplexer using a ternary operator:

```verilog
module ternary_operator_mux (input i0, input i1, input sel, output y);
  assign y = sel ? i1 : i0;
endmodule
```
- **Function:** `y = i1` if `sel = 1`; else `y = i0`.

![lab1](https://github.com/user-attachments/assets/3f5eb05a-1861-4bb8-940c-6ff9f2af87fb)

---

### Lab 2: Synthesis Using Yosys

Synthesize the above MUX using Yosys.  
_Follow the standard Yosys synthesis flow._

![lab2](https://github.com/user-attachments/assets/7a0cdc7c-cbbd-4943-bd3d-130a0d66b9b1)

---

### Lab 3: Gate-Level Simulation (GLS) of MUX

Run GLS for the synthesized MUX.  
Use this command (adjust paths as needed):

```shell
iverilog /path/to/primitives.v /path/to/sky130_fd_sc_hd.v ternary_operator_mux.v testbench.v
```

![lab3](https://github.com/user-attachments/assets/9acf45b3-2e42-4ac1-88ae-b4a494cc8d87)

---

### Lab 4: Bad MUX Example (Common Pitfalls)

Verilog code with intentional issues:

```verilog
module bad_mux (input i0, input i1, input sel, output reg y);
  always @ (sel) begin
    if (sel)
      y <= i1;
    else 
      y <= i0;
  end
endmodule
```

#### Issues:
- **Incomplete sensitivity list**: Should include `i0`, `i1`, and `sel`.
- **Non-blocking assignment in combinational logic**: Should use blocking assignments (`=`).

**Corrected version:**
```verilog
always @ (*) begin
  if (sel)
    y = i1;
  else
    y = i0;
end
```

![lab4](https://github.com/user-attachments/assets/4c2ede06-0605-4ff0-99cb-fc89844b89e4)

---

### Lab 5: GLS of Bad MUX

Perform GLS on the `bad_mux`.  
Expect simulation mismatches or warnings due to above issues.

![lab5](https://github.com/user-attachments/assets/2e698404-27b5-4c4a-a811-41b5fc13db77)

---

### Lab 6: Blocking Assignment Caveat

Verilog code:

```verilog
module blocking_caveat (input a, input b, input c, output reg d);
  reg x;
  always @ (*) begin
    d = x & c;
    x = a | b;
  end
endmodule
```

#### What’s wrong?
- The order of assignments causes `d` to use the old value of `x`—not the newly computed value.
- **Best Practice:** Assign intermediate variables before using them.

**Corrected order:**
```verilog
always @ (*) begin
  x = a | b;
  d = x & c;
end
```

![lab6](https://github.com/user-attachments/assets/42cac594-0008-4c7b-b415-43e6565b6081)

---

### Lab 7: Synthesis of the Blocking Caveat Module

Synthesize the corrected version of the module and observe the results.

![lab7](https://github.com/user-attachments/assets/833bfacc-3b76-40fa-814c-47f0d783a6e0)

---

## 5. Summary

- **Gate-Level Simulation (GLS):** Validates netlist functionality, timing, and testability after synthesis.
- **Synthesis-Simulation Mismatch:** Avoid by using synthesizable, unambiguous RTL code.
- **Blocking vs. Non-Blocking:** Use blocking (`=`) for combinational, non-blocking (`<=`) for sequential logic.
- **Labs:** Reinforce key concepts and highlight common RTL pitfalls.

---

> [!TIP]
>  Always simulate both your RTL and gate-level netlist, and review warnings from synthesis and simulation tools!

## 6. Conclusion
In short, despite being a time consuming activity and having many challenges in setup and debug, GLS is a great confidence booster in the quality of the design. It can uncover certain hidden issues which get missed out or difficult to find by RTL simulations. The probability of having sound sleep after tape out improves with GLS.

---
