
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
- **Missing Sentivity List** e.g. `Bad_mux` case in Lab 1.
- **Blocking and Non-Blocking Assignment** `ternary_mux` case as in Lab2
- **Non Standard Verilog Coding**
- **Non-synthesizable constructs**: Use of delays, initial blocks, or other code not supported by synthesis.
- **Incomplete or ambiguous coding**: E.g., missing `else` clauses, improper sensitivity lists.
- **Tool interpretation differences**: Simulation and synthesis tools may interpret ambiguous RTL differently.

**Key Point:** Always write synthesizable, unambiguous RTL and follow good coding practices to minimize mismatches.

---

## 3. Blocking vs. Non-Blocking Assignments in Verilog

Verilog offers two types of procedural assignments:

### 3.1 Blocking Statements (`=`)
A one-step process i.e evaluate the RHS of the expression and update the LHS without any delay.
- **Syntax:** `=`
- **Execution:** Sequential, executes in the order it is written.
- That is first statement is evaluated before the second statement.
- use must be inside `always` block.
- **Suitable for:** Combinational logic (e.g., `always @(*)`).
- **Example:**  
  ```verilog
  always @(*) y = a & b;
  ```
> [!TIP]
>  Blocking assignment always suffers from the problem of Race condition when the assignment happens to it from two processes concurrently.
>  Hence, Avoid the blocking statement usage unless you are pretty sure!!

### 3.2 Non-Blocking Statements (`<=`)
Non-blocking is a two-step process i.e. a) Evaluate the RHS expression at the beginning of the time step and. b) Update the LHS at the end of the time step.
- **Syntax:** `<=`
- **Execution:** Scheduled, executes concurrently or parallelly at the end of the time step.
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

✅ To start with let's Summarise GLS Flow

- Design verilog + Testbench -> Yosys + Standard `sky130_fd_sc_hd__tt_025C_1v80.lib`  → Netlist .v file
- iverilog + Design netlist +  `primitives` + `sky130_fd_sc_hd.v` → Design .vcd file
- gtkwave .vcd file to check the mismatch or matched RTL vs GLS.

<img width="1776" height="832" alt="Image" src="https://github.com/user-attachments/assets/a9d31a4f-63a8-411b-84fd-e9e33f2d071d" />

---

### Lab 1: Ternary Operator MUX

Verilog code for a simple 2:1 multiplexer using a ternary operator:

```verilog
module ternary_operator_mux (input i0, input i1, input sel, output y);
  assign y = sel ? i1 : i0;
endmodule
```
- **Function:** `y = i1` if `sel = 1`; else `y = i0`.

---

### Lab 2: Synthesis Using Yosys

Synthesize the above MUX using Yosys.  
_Follow the standard Yosys synthesis flow._

ternerary oerator 

<img width="570" height="340" alt="Image" src="https://github.com/user-attachments/assets/6082e03a-3ac9-45f7-9277-f99a97afd914" />

---

### Lab 3: Gate-Level Simulation (GLS) of MUX

Run GLS for the synthesized MUX.  
Use this command (adjust paths as needed):

```shell
iverilog /path/to/primitives.v /path/to/sky130_fd_sc_hd.v ternary_operator_mux.v testbench.v
```
- RTL simuation waveform 

<img width="942" height="359" alt="Image" src="https://github.com/user-attachments/assets/b2a695aa-c884-40e3-b7d8-38ddbeabb7ba" />

---


- GLS simulation waveform 

<img width="938" height="485" alt="Image" src="https://github.com/user-attachments/assets/0a55bf60-8a56-4017-8696-25ee9b29f8cf" />

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

- Bad Multipliexer Desig: Yosys output viewer

<img width="572" height="282" alt="Image" src="https://github.com/user-attachments/assets/b69f8801-a49f-489c-a00f-13ae573ab07f" />

---

- RTL level simulation for Bad Multipliexer Design

<img width="935" height="360" alt="Image" src="https://github.com/user-attachments/assets/3a5bb104-ada3-4760-bd1e-eadd195cd8a5" />

---

### Lab 5: GLS of Bad MUX

Perform GLS on the `bad_mux`.  
Expect simulation mismatches or warnings due to above issues.

<img width="937" height="456" alt="Image" src="https://github.com/user-attachments/assets/6bc5995b-f87e-44d5-a9f3-249b12d8d9d0" />

---

### Lab 6: Blocking Assignment Caveat


<img width="539" height="183" alt="Image" src="https://github.com/user-attachments/assets/f253af9a-e10a-4460-b4dd-5834d9456100" />

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
<img width="574" height="267" alt="Image" src="https://github.com/user-attachments/assets/9425efc4-264b-49ab-aedb-14980bbcab67" />

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

<img width="936" height="379" alt="Image" src="https://github.com/user-attachments/assets/0afde0be-3b7e-4998-9f5c-b508d90bf6cc" />

---

### Lab 7: Synthesis of the Blocking Caveat Module

Synthesize the module and observe the results difference between RTL and GLS clearly. We understand why blocking statement are used with utmost care.


<img width="920" height="489" alt="Image" src="https://github.com/user-attachments/assets/1a7508d3-8d94-49d0-b8ff-e2a3af32a8a5" />

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
