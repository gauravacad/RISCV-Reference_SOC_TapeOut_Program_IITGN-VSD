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

### 🔹 How it all works together
1. This is shown under Day1-section two `Day1: Labs using iverilog and gtkwave`
1. We will use prebuilt **Design** (Verilog RTL) from the `verilog_design` folder.  
2. Use the **Testbench** with initial word `tb_<design_name>` to simulate the design.  
3. Compile both using `iverilog`.  
   ```bash
   iverilog -o sim.out mux2to1.v tb_mux2to1.v
  
