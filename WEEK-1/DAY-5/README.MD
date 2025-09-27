# Day 5: Optimization in Synthesis

Welcome to Day 5 of the RTL workshop! Today, we will cover optimization in Verilog synthesis, focusing on `if-else` statements, `for` loops, generate blocks, and explore how improper coding can lead to inferred latches. Labs are included for hands-on experience.

---
## Contents

- [1. If-Else Statements in Verilog](#1-if-else-statements-in-verilog)
- [2. Inferred Latches in Verilog](#2-inferred-latches-in-verilog)
- [3. Labs for If-Else and Case Statements](#3-labs-for-if-else-and-case-statements)
  - [Lab 1: Incomplete If Statement](#lab-1-incomplete-if-statement)
  - [Lab 2: Synthesis Result of Lab 1](#lab-2-synthesis-result-of-lab-1)
  - [Lab 3: Nested If-Else](#lab-3-nested-if-else)
  - [Lab 4: Synthesis Result of Lab 3](#lab-4-synthesis-result-of-lab-3)
  - [Lab 5: Complete Case Statement](#lab-5-complete-case-statement)
  - [Lab 6: Synthesis Result of Lab 5](#lab-6-synthesis-result-of-lab-5)
  - [Lab 7: Incomplete Case Handling](#lab-7-incomplete-case-handling)
  - [Lab 8: Partial Assignments in Case](#lab-8-partial-assignments-in-case)
- [4. For Loops in Verilog](#4-for-loops-in-verilog)
- [5. Generate Blocks in Verilog](#5-generate-blocks-in-verilog)
- [6. What is an RCA (Ripple Carry Adder)?](#6-what-is-an-rca-ripple-carry-adder)
- [7. Labs on Loops and Generate Blocks](#7-labs-on-loops-and-generate-blocks)
  - [Lab 9: 4-to-1 MUX Using For Loop](#lab-9-4-to-1-mux-using-for-loop)
  - [Lab 10: 8-to-1 Demux Using Case](#lab-10-8-to-1-demux-using-case)
  - [Lab 11: 8-to-1 Demux Using For Loop](#lab-11-8-to-1-demux-using-for-loop)
  - [Lab 12: 8-bit Ripple Carry Adder with Generate Block](#lab-12-8-bit-ripple-carry-adder-with-generate-block)
- [Summary](#summary)

---

## 1. If-Else Statements in Verilog
- Mainly used to create priority  logic.
- **If-else statements** are used for conditional execution in behavioral modeling, typically within procedural blocks (`always`, `initial`, tasks, or functions).
- **condition**: An expression evaluating to true (non-zero) or false (zero).
- **begin ... end**: must be used for multiple statements. Omit if only 1 statement is present.
- The `else` part is optional.

### Syntax
```verilog
if (condition) begin
    // Code block executed if condition is true
end else begin
    // Code block executed if condition is false
end
```
---
#### Nested If-Else

```verilog
if (condition1)
begin
    // Code for condition1 true
end 
else if (condition2) 
begin
    // Code for condition2 true
end 
else  //Default condition V. Imp. 
begin
    // Code if no conditions are true
end
```
---

## 2. Inferred Latches in Verilog

**Inferred latches** occur when a combinational logic block does not assign a value to a variable in all possible execution paths. This causes the synthesis tool to infer a latch, which may not be the designer’s intention.

- The Figure shows how latch adds in the circuit unintentionally.

<img width="424" height="218" alt="Image" src="https://github.com/user-attachments/assets/20c9990f-70e1-4af7-8297-c810e807cd48" />

---

### Example of Latch Inference

```verilog
module ex (
    input wire a, b, sel,
    output reg y
);
    always @(a, b, sel) begin
        if (sel == 1'b1)
            y = a; // No 'else' - y is not assigned when sel == 0
    end
endmodule
```

**Problem**: When `sel` is 0, `y` is not assigned, incomplete output assigment will leads to unintentional latch.

#### Solution Mantra: `code with default` Yes!!: Add Else for the incomplete `if` and Similarly, use Default Case for `Case Statement`

```verilog
module ex (
    input wire a, b, sel,
    output reg y
);
    always @(a, b, sel) begin
        case(sel)
            1'b1 : y = a;
            default : y = 1'b0; // Default assignment
        endcase
    end
endmodule
```
> [!Tip]  
> Incomplete "If-Else" will leads to latches. So, always complete your if-else statement specially when the design has intended combinatinal logics.

> [!Tip] 
> Partial assignemnts of outputs in case statement when default case is even present. Signal(s) missing for the sensitivity list (this is why @* should be used).

> [!Tip] 
> "Parallel_case" are Evil Twins!,due to overlapping cases latches are infered accidential. (e.g. 2'b1?) leads to **10** and **11** as true consditions.
---                 

## 3. Labs for **If-Else** and **Case** Statements

### Lab 1: Incomplete If Statement

```verilog
module incomp_if (input i0, input i1, input i2, output reg y);
always @(*) begin
    if (i0)
        y <= i1;
end
endmodule
```
## RTL Simulation waveform for incomplete-If satatments.

- Case I: incomplete if statements Bad coding style

<img width="1006" height="401" alt="Image" src="https://github.com/user-attachments/assets/410a8ca0-54db-4c88-a77f-5dbc0e493c33" />

- When io= low and i1= low, the output is constant 
- When i2=high output follow i3


### Lab 2: Synthesis Result of Lab 1

- **Inference of Latch can be seen in the snapshot while mapping design with standard cells. Here i0 and i2 both are low, Y latches. 


<img width="611" height="410" alt="Image" src="https://github.com/user-attachments/assets/16cfb899-9ed7-401d-9f33-9e6dfabc630a" />

### Lab 3: Nested If-Else

```verilog
module incomp_if2 (input i0, input i1, input i2, input i3, output reg y);
always @(*) begin
    if (i0)
        y <= i1;
    else if (i2)
        y <= i3;
end
endmodule
```

- Case:- Incomplete output assignment or defalut value even when If statements are complete.
---

<img width="996" height="390" alt="Image" src="https://github.com/user-attachments/assets/603761a2-84df-4775-8d9e-f4b99a2f86fc" />

---

### Lab 4: Synthesis Result of Lab 3

- This section explores the show command and explains the symbols used in the **circuit diagrams** using yosys tool for both if cases.  

<img width="538" height="334" alt="Image" src="https://github.com/user-attachments/assets/8a9bb25c-bc4c-4bb8-94f3-013b4c52535d" />

---

<img width="612" height="334" alt="Image" src="https://github.com/user-attachments/assets/6274ef43-d6d7-4551-bdff-1b0fe72b8a33" />

---

### Lab 5: Complete Case Statement
- Many variants is read under **case statements** like Bad_case, Incomplete_case, partial case and complete case.
- code for the complete case is presented here.

```verilog
module comp_case (input i0, input i1, input i2, input [1:0] sel, output reg y);
always @(*) begin
    case(sel)
        2'b00 : y = i0;
        2'b01 : y = i1;
        default : y = i2;
    endcase
end
endmodule
```

<img width="991" height="402" alt="Image" src="https://github.com/user-attachments/assets/101fee17-432a-40a0-b7a7-a862098fb317" />

- Proper function and no latch inferred. 

---

### Lab 6: Synthesis Result of Lab 5

- Circuit diagram for the complete case

<img width="964" height="445" alt="Image" src="https://github.com/user-attachments/assets/03082e49-847c-40ea-9687-20380afa73ee" />

---

### Lab 7: Incomplete Case Handling

```verilog
module bad_case (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);
always @(*) begin
    case(sel)
        2'b00: y = i0;
        2'b01: y = i1;
        2'b10: y = i2;
        2'b1?: y = i3; // '?' is a wildcard; be careful with incomplete cases!
    endcase
end
endmodule
```
<img width="910" height="393" alt="Image" src="https://github.com/user-attachments/assets/1329ccab-1d8e-4414-87b4-2ba6ebd93a9d" />

- It can be viewed easily from the waveform that at condition `10 and 11` the output has same value
### Synthesis circuit diagram 

- The first diagram infers the latch which is clearly observed in other diagram

<img width="533" height="365" alt="Image" src="https://github.com/user-attachments/assets/eae1db38-ef0e-4a21-b337-b46d1a45723b" />

---

<img width="976" height="381" alt="Image" src="https://github.com/user-attachments/assets/c4892782-e3d4-4664-ad0e-022d1e01021e" />


---

### Lab 8: Partial Assignments in Case
- In this case two conditions results the confusion for the simulator or complier that is `10 and 11` results to latch.

<img width="306" height="314" alt="Image" src="https://github.com/user-attachments/assets/e0109524-4896-433d-8fb0-0b168e11d63e" />

```verilog
module partial_case_assign (
    input i0, input i1, input i2,
    input [1:0] sel,
    output reg y, output reg x
);
always @(*) begin
    case(sel)
        2'b00: begin
            y = i0;
            x = i2;
        end
        2'b01: y = i1;
        default: begin
            x = i1;
            y = i2;
        end
    endcase
end
endmodule
```
### RTL simulation waveform 

<img width="991" height="401" alt="Image" src="https://github.com/user-attachments/assets/3cc19978-e594-4b7f-a79f-8c7dea210c6b" />

---

### Circuit diagram with inferences 

<img width="537" height="415" alt="Image" src="https://github.com/user-attachments/assets/442d7fba-64e0-4463-a607-55df7f330b08" />

---

<img width="996" height="501" alt="Image" src="https://github.com/user-attachments/assets/f16ab3eb-063c-4cc3-ad59-07c62c3a79a8" />


> **Note:** Steps to perform the above labs are shown in [Day 1].
> Case is not mutual exclusive like if statements. That is Case won’t exit even the condition is matched, which can leads to infer latches.

---

## 4. For Loops in Verilog

A **for loop** is used within procedural blocks (`initial`, `always`, tasks/functions) to execute statements multiple times based on a loop counter.

### Syntax

```verilog
for (initialization; condition; increment) begin
    // Statements to execute
end
```

- Must be inside procedural blocks.
- Synthesizable only if the number of iterations is fixed at compile time.

#### Example: 4-to-1 MUX Using a For Loop

```verilog
module mux_4to1_for_loop (
    input wire [3:0] data, // 4 input lines
    input wire [1:0] sel,  // 2-bit select
    output reg y           // Output
);
    integer i;
    always @(data, sel) begin
        y = 1'b0; // Default output
        for (i = 0; i < 4; i = i + 1) begin
            if (i == sel)
                y = data[i];
        end
    end
endmodule
```

---

## 5. Generate Blocks in Verilog

A **generate block** is used to create hardware structures such as module instances or logic at compile time. Typically used with `for` loops and the `genvar` keyword.

#### Example

```verilog
genvar i;
generate
    for (i = 0; i < 4; i = i + 1) begin : gen_loop
        and_gate and_inst (.a(in[i]), .b(in[i+1]), .y(out[i]));
    end
endgenerate
```

---

## 6. What is an RCA (Ripple Carry Adder)?

An RCA adds binary numbers using a chain of full adders. To add `n` bits, you need `n` full adders. Each carry-out connects to the carry-in of the next stage.

![image](https://github.com/user-attachments/assets/f1ec27d4-b770-4d7a-a418-6435fc81f538)

---

## 7. Labs on Loops and Generate Blocks

### Lab 9: 4-to-1 MUX Using For Loop. **With my experience I found** that this will leads to infer a latch**. Let's see how?
- The given code is the below but the output is not assigned with default values.

```verilog
module mux_generate (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);
wire [3:0] i_int;
assign i_int = {i3, i2, i1, i0};
integer k;
always @(*) begin
    for (k = 0; k < 4; k = k + 1) begin
        if (k == sel)
            y = i_int[k];
    end
end
endmodule
```

### RTL simulations 

<img width="1015" height="398" alt="Image" src="https://github.com/user-attachments/assets/04edba57-4646-44bc-85d1-86bb9fed0c2b" />

---
### Ciruit diagram study 

<img width="531" height="343" alt="Image" src="https://github.com/user-attachments/assets/b3ebd32a-4118-442a-aa43-2bc2752e2aaf" />

---

<img width="864" height="541" alt="Image" src="https://github.com/user-attachments/assets/b7eb9050-1801-4cbf-99e8-48aed8be4836" />


```verilog
module mux_generate (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);
wire [3:0] i_int;
assign i_int = {i3, i2, i1, i0};
integer k;
always @(*) 
begin
  y = 1'b0;  // default which removes the LAtch
    for (k = 0; k < 4; k = k + 1) begin
        if (k == sel)
            y = i_int[k];
    end
end
endmodule
```

<img width="1028" height="543" alt="Image" src="https://github.com/user-attachments/assets/dd5d3b87-f396-44c6-8a86-ddf8c895ccc1" />

---

### Lab 10: 8-to-1 Demux Using Case

```verilog
module demux_case (
    output o0, output o1, output o2, output o3,
    output o4, output o5, output o6, output o7,
    input [2:0] sel,
    input i
);
reg [7:0] y_int;
assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;
always @(*) begin
    y_int = 8'b0;
    case(sel)
        3'b000 : y_int[0] = i;
        3'b001 : y_int[1] = i;
        3'b010 : y_int[2] = i;
        3'b011 : y_int[3] = i;
        3'b100 : y_int[4] = i;
        3'b101 : y_int[5] = i;
        3'b110 : y_int[6] = i;
        3'b111 : y_int[7] = i;
    endcase
end
endmodule
```

<img width="952" height="400" alt="Image" src="https://github.com/user-attachments/assets/45775b37-b187-4b4d-a60f-9281be228f05" />

---

### Lab 11: 8-to-1 Demux Using For Loop

```verilog
module demux_generate (
    output o0, output o1, output o2, output o3,
    output o4, output o5, output o6, output o7,
    input [2:0] sel,
    input i
);
reg [7:0] y_int;
assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;
integer k;
always @(*) begin
    y_int = 8'b0;
    for (k = 0; k < 8; k = k + 1) begin
        if (k == sel)
            y_int[k] = i;
    end
end
endmodule
```

<img width="952" height="400" alt="Image" src="https://github.com/user-attachments/assets/45775b37-b187-4b4d-a60f-9281be228f05" />

<img width="785" height="677" alt="Image" src="https://github.com/user-attachments/assets/e0fa54f8-ecd5-467d-bd04-b16c605f3725" />

---

### Lab 12: 8-bit Ripple Carry Adder with Generate Block

```verilog
module rca (
    input [7:0] num1,
    input [7:0] num2,
    output [8:0] sum
);
wire [7:0] int_sum;
wire [7:0] int_co;

genvar i;
generate
    for (i = 1; i < 8; i = i + 1) begin
        fa u_fa_1 (.a(num1[i]), .b(num2[i]), .c(int_co[i-1]), .co(int_co[i]), .sum(int_sum[i]));
    end
endgenerate

fa u_fa_0 (.a(num1[0]), .b(num2[0]), .c(1'b0), .co(int_co[0]), .sum(int_sum[0]));

assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```
**Full Adder Module:**
```verilog
module fa (input a, input b, input c, output co, output sum);
    assign {co, sum} = a + b + c;
endmodule
```
### RTL Simulation 

<img width="1210" height="434" alt="Image" src="https://github.com/user-attachments/assets/798f5757-a730-41e8-b15c-9c0a16fefcc1" />

----

### Circuit diagram 

<img width="610" height="684" alt="Image" src="https://github.com/user-attachments/assets/eb07c7bc-3596-45f6-a424-e0d4729cabef" />

---

### Comparison RTL vs GLS : Matched 

<img width="995" height="623" alt="Image" src="https://github.com/user-attachments/assets/9647a8b5-ac35-40a2-8bff-e715fbbda1b9" />
---

> **Note:** Steps to perform the above labs are already shown in [Day 1].

---

## Summary

- Use complete if-else and case statements to avoid unintended latch inference.
- For loops and generate blocks are powerful for writing scalable, synthesizable code.
- Always ensure every signal is assigned in every possible execution path for combinational logic.
- Use labs to reinforce concepts with practical Verilog code and synthesis results.

---
