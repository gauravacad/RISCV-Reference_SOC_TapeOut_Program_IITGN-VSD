Day 1: Introduction to Verilog RTL Design & Synthesis
---
We will start exploring Verilog designs, gain hands-on experience with Icarus Verilog (iverilog) tool for open-source simulation, and then how to visualise the waveform i.e. timing simulation output using Gtkwave tool.



## Table of Content 

- [🔍 Introduction to iverilog tool, design and test bench](#1--introduction-to-iverilog-tool-design-and-test-bench)  
- [🧪 Lab: Simulating a 2-to-1 Multiplexer](#2--Lab-simulating-a-2-to-1-multiplexer)  
- [📖 Verilog Code Walkthrough & Analysis](#3-Verilog-code-analysis)  
- [🏗️ Introduction to Yosys & Gate Libraries](#4--Introduction-to-yosys--gate-libraries)  
- [🔧 Synthesis Lab with Yosys](#5--Synthesis-lab-with-yosys)  
- [🎯 Summary & Key Takeaways](#6--Summary-&-Key-Takeways)  


## 1. 🔍 **Introduction to iverilog tool, design and test bench**  

### 🔹 What is Icarus Verilog tool (a.k.a. iverilog)?
- **Icarus Verilog** is an open-source Verilog simulation and synthesis tool.  
- It allows you to **compile and simulate Verilog code** from the command line.  
- Typically paired with **GTKWave** for waveform viewing.  

---

### 🔹 What is a Design?
- The **Design** is your **RTL (Register Transfer Level) description** of hardware.  
- Written in **Verilog**, it describes how the circuit should behave (our e.g., is a 2:1 multiplexer).  
- First example our design_name is: `good_mux.v` (2-to-1 multiplexer).
- In our repository all the files are under `verilog_files` folder.
- Each file has its own testbench for observing the waveforms.

<div align="center">
<img width="767" height="308" alt="Image" src="https://github.com/user-attachments/assets/11d927c8-8776-4409-a8d2-2f871c7406f6" />
</div>

### 🔹 What is a Testbench?
- A **Testbench** is **not hardware**, but a simulation environment.  
- It is written in Verilog to:
  - Apply **inputs/stimuli** to the design.  
  - Observe **outputs** to verify correctness.  
  - In our repository all the testbench will start with `tb` followed with design/ verilog file (.v)
- Example: `tb_goodmux.v` (testbench for multiplexer).

---

### 2. 🧪  Lab: Simulating a 2-to-1 Multiplexer
This is shown under Day1-section two `Day1: Labs using iverilog and gtkwave <br>`
🔹 Step 1: Clone the directory under `VLSI` folder

```bash
$ mkdir VLSI
$ cd VLSI
$ git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
$ cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```
🔹 Step 2: We need to change the directory to `verilog_design` folder, Now.

- We will use prebuilt **Design** (Verilog RTL) from the `verilog_design` folder.  
- we will use the **Testbench** with initial word `tb_<design_name>` to simulate the design.  
- Compile both design and testbench using `iverilog` will lead an `.out` file.
- It is a compiled simulation executable created by iverilog Tool.   
   ```bash
   $ iverilog good_mux.v tb_good_mux.v -o good_mux.out
   ```
🔹 Step 3: Running `.out` → simulation execution
- VCD generation → happens during simulation execution (only if $dumpfile + $dumpvars are present in the testbench).
   ```bash
   $ ./good_mux.out
   
   ```
🔹 Step 4: View the waveform using gtkwave viewer. 
- we need to append the signal in the viewer window here.
```bash
$ gtkwave tb_good_mux.vcd
```
<div align="center">
  <<img width="1291" height="419" alt="Image" src="https://github.com/user-attachments/assets/3c297358-906e-4472-996f-209994646b4c" />
</div>

## 3. 📖 Verilog Code Analysis

**The code for the multiplexer (`good_mux.v`):**

<div align="center">
<img width="528" height="290" alt="Image" src="https://github.com/user-attachments/assets/21527d38-4ee8-4c92-b893-aeace939e13a" />
</div>

```verilog
module good_mux (input i0, input i1, input sel, output reg y);
always @ (*)
begin
    if(sel)
        y <= i1;
    else 
        y <= i0;
end
endmodule
```

###  **How It Works**



- **Inputs:** `i0`, `i1` (data), `sel` (select line)
- **Output:** `y` (registered output)
- **Logic:** If `sel` is 1, `y` gets `i1`; if `sel` is 0, `y` gets `i0`.

---
## 4. 🏗️ Introduction to Yosys & Gate Libraries

###  What is Yosys?

**Yosys** is a powerful open-source synthesis tool for digital hardware. It takes your Verilog code and converts it into a gate-level netlist—a hardware blueprint.

#### Yosys Features

- **Synthesis:** Converts HDL to a logic circuit
- **Optimization:** Improves speed or area
- **Technology Mapping:** Matches logic to actual hardware cells
- **Verification:** Checks correctness
- **Extensibility:** Supports custom flows

###  Why Do Libraries Have Different Gate "Flavors"?

A `.lib` file contains many versions of each gate (like AND, OR, NOT) with different properties:

- **Performance:** Faster gates for critical paths, slower for power savings
- **Power:** Some gates use less energy
- **Area:** Smaller gates for compact chips
- **Drive Strength:** Stronger gates to drive more load
- **Signal Integrity:** Specialized gates for noise/performance
- **Mapping:** Synthesis tools pick the best flavor for your needs

---

## 5. 🔧 Synthesis Lab with Yosys
- My directory structure 
```bash
VLSI/
└── sky130RTLDesignAndSynthesisWorkshop/
    ├── DC_WORKSHOP/
    ├── my_lib/
    │   ├── lib/
    │   │   └── sky130_fd_sc_hd__tt_025C_1v80.lib
    │   └── verilog_model/
    ├── verilog_files/
    │   ├── good_mux.out
    │   ├── good_mux.v
    │   ├── tb_good_mux.v
    │   └── tb_good_mux.vcd
    ├── yosys_run.sh
    └── README.md
```
-Let’s synthesize the `good_mux` design using Yosys!

###  Step-by-Step Yosys Flow

1. **Start Yosys**
    ```shell
    yosys
    ```

2. **Read the liberty library**
    ```shell
    read_liberty -lib ../../mylib/lib/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```

3. **Read the Verilog code**
    ```shell
    read_verilog ./good_mux.v
    ```

4. **Synthesize the design**
    ```shell
    synth -top good_mux
    ```

5. **Technology mapping**
    ```shell
    abc -liberty ../../mylib/lib/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```

6. **Visualize the gate-level netlist**
    ```shell
    show
    ```

<div align="center">
  <img width="1860" height="1032" alt="Image" src="https://github.com/user-attachments/assets/313769cb-eea9-4f2e-8560-e69c7b11b877" alt="Yosys Gate-level Schematic" width="50%">
</div>

---

## 6. 🎯 Summary & Key Takeways

- I have learned about simulators, designs, and testbenches.
- I ran your first Verilog simulation with iverilog and visualized waveforms.
- Also I have analyzed the 2-to-1 mux code.
- Finally, I have explored `Yosys`, `iverilog`, `Gtkwave` and also , learned why gate libraries have different flavors.

