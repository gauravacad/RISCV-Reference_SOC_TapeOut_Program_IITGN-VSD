Day 1: Introduction to Verilog RTL Design & Synthesis
---
We will start exploring Verilog designs, gain hands-on experience with Icarus Verilog (iverilog) tool for open-source simulation, and then how to visualise the waveform i.e. timing simulation output using Gtkwave tool.



## Table of Content 

- 🔍 **Introduction to iverilog tool, design and test bench**  
- ⚡ **Getting Started with Icarus Verilog (iverilog)**  
- 🧪 **Lab: Simulating a 2-to-1 Multiplexer**  
- 📖 **Verilog Code Walkthrough & Analysis**  
- 🏗️ **Introduction to Yosys & Gate Libraries**  
- 🔧 **Synthesis Lab with Yosys**  
- 📝 **Summary & Key Takeaways**  

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

<img width="767" height="308" alt="Image" src="https://github.com/user-attachments/assets/11d927c8-8776-4409-a8d2-2f871c7406f6" />


### 🔹 What is a Testbench?
- A **Testbench** is **not hardware**, but a simulation environment.  
- It is written in Verilog to:
  - Apply **inputs/stimuli** to the design.  
  - Observe **outputs** to verify correctness.  
  - In our repository all the testbench will start with `tb` followed with design/ verilog file (.v)
- Example: `tb_goodmux.v` (testbench for multiplexer).

---

### 🔹 Lab: Simulating a 2-to-1 Multiplexer
This is shown under Day1-section two `Day1: Labs using iverilog and gtkwave`
step 1: Clone the directory under `VLSI` folder

```bash
$ mkdir VLSI
$ cd VLSI
$ git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
$ cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```
Step 2: We need to change the directory to `verilog_design` folder, Now.

- We will use prebuilt **Design** (Verilog RTL) from the `verilog_design` folder.  
- we will use the **Testbench** with initial word `tb_<design_name>` to simulate the design.  
- Compile both design and testbench using `iverilog` will lead an `.out` file.
- It is a compiled simulation executable created by iverilog Tool.   
   ```bash
   $ iverilog good_mux.v tb_good_mux.v -o good_mux.out
   ```
Step3: Running `.out` → simulation execution
- VCD generation → happens during simulation execution (only if $dumpfile + $dumpvars are present in the testbench).
   ```bash
   $ ./good_mux.out
   
   ```
step 4: View the waveform using gtkwave viewer. 
- we need to append the signal in the viewer window here.
```bash
$ gtkwave tb_good_mux.vcd
```
<img width="1291" height="419" alt="Image" src="https://github.com/user-attachments/assets/3c297358-906e-4472-996f-209994646b4c" />
