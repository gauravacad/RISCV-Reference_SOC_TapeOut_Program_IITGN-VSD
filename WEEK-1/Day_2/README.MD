# Day 2: Timing Libraries, Synthesis Approaches, and Efficient Flop Coding Styles

Welcome to Day 2 of the RTL Workshop. This day covers three crucial topics:
- Understanding the `.lib` timing library (sky130_fd_sc_hd__tt_025C_1v80.lib) used in open-source PDKs.
- Comparing hierarchical vs. flat synthesis methods.
- Exploring efficient coding styles for flip-flops in RTL design.

---


# Contents

- [Timing Libraries](#timing-libraries)
  - [SKY130 PDK Overview](#sky130-pdk-overview)
  - [Decoding tt_025C_1v80 in the SKY130 PDK](#decoding-tt_025c_1v80-in-the-sky130-pdk)
  - [Opening and Exploring the .lib File](#opening-and-exploring-the-lib-file)

- [Hierarchical vs. Flattened Synthesis](#hierarchical-vs-flattened-synthesis)
  - [Hierarchical Synthesis](#hierarchical-synthesis)
  - [Flattened Synthesis](#flattened-synthesis)
  - [Key Differences](#key-differences)

- [Flip-Flop Coding Styles](#flip-flop-coding-styles)
  - [Asynchronous Reset D Flip-Flop](#asynchronous-reset-d-flip-flop)
  - [Asynchronous Set D Flip-Flop](#asynchronous-set-d-flip-flop)
  - [Synchronous Reset D Flip-Flop](#synchronous-reset-d-flip-flop)

- [Simulation and Synthesis Workflow](#simulation-and-synthesis-workflow)
  - [Icarus Verilog Simulation](#icarus-verilog-simulation)
  - [Synthesis with Yosys](#synthesis-with-yosys)

---

## Timing Libraries

### SKY130 PDK Overview

The SKY130 PDK is an open-source Process Design Kit based on SkyWater Technology's 130nm CMOS technology. It provides essential models and libraries for integrated circuit (IC) design, including timing, power, and process variation information.

### Decoding tt_025C_1v80 in the SKY130 PDK

- **tt**: Typical process corner.
- **025C**: Represents a temperature of 25°C, relevant for temperature-dependent performance.
- **1v80**: Indicates a core voltage of 1.8V.
- In nut shell `sky130_fd_sc_hd__<corner>_<temp>C_<vdd>.lib` 
This naming convention clarifies which process, voltage, and temperature conditions the library models.

---
### 📘 What is a .lib in VLSI?
- A .lib file (Liberty file) is a **timing and characterization** library used in VLSI design.
- It contains the **behavior, timing, power, and area information of standard cells** (gates, flip-flops, latches, etc.) 
- Thses are used during **logic synthesis, place-and-route, and static timing analysis (STA)**.
- **.lib** = `the dictionary of cells`
### Opening and Exploring the .lib File

To open the sky130_fd_sc_hd__tt_025C_1v80.lib file we need an editor like  `gedit` or `vim` or `nano`.

1. **Install a text editor:**
   ```shell
   $ sudo apt install --assume-yes gedit
   ```
2. Touch the `~./lib/` and **Open the sky130_fd_sc_hd__tt_025C_1v80.lib file:**
   ```shell
   gedit sky  sky130_fd_sc_hd__tt_025C_1v80.lib
   ```

<img width="700" height="700" alt="Image" src="https://github.com/user-attachments/assets/7eda29a5-7e85-4e54-b3ae-271c1901fa9b">

## 📘 PVT Corners in VLSI
---
**PVT = Process, Voltage, Temperature**  
- It revelas weather our silicon will work across all its corner on any variation.
- Defines the operating conditions under which standard cells and circuits are characterized.
---
🔹 Extended PVT Corner Table
Corner   | Process                   | Voltage | Temp   | Use Case
---------|---------------------------|---------|--------|-------------------------------
SS       | Slow NMOS, Slow PMOS      | 1.60 V  | 125 °C | Worst-case delay (Setup check)
TT       | Typical NMOS, Typical PMOS| 1.80 V  | 25 °C  | Nominal condition
FF       | Fast NMOS, Fast PMOS      | 1.95 V  | -40 °C | Best-case delay (Hold check)
SF       | Slow NMOS, Fast PMOS      | 1.80 V  | 25 °C  | Stress-test (imbalanced devices)
FS       | Fast NMOS, Slow PMOS      | 1.80 V  | 25 °C  | Stress-test (imbalanced devices)

---
## 🔑 Why PVT matters?
- ✅ **Setup timing check** → Worst-case (SS, 1.6V, 125°C)  
- ✅ **Hold timing check** → Best-case (FF, 1.95V, -40°C)  
- ✅ **Power analysis** → High voltage + high temp → worst leakage  
---
## Hierarchical vs. Flattened Synthesis
To study the diffence between these styles , lets take an example of a `multiple_modules.v`.
The File consist of two submodule namd as `sub_module1` and `sub_module2` and are called in **top module** named `multiple_modules`.

```verilog
#Sub_module1: Function as an AND logic 
module sub_module2 (input a, input b, output y);
	assign y = a | b;
endmodule

#Sub_module2: Function as an OR logic
module sub_module1 (input a, input b, output y);
	assign y = a&b;
endmodule

#Top_module: multiple_modules
module multiple_modules (input a, input b, input c , output y);
	wire net1;
	sub_module1 u1(.a(a),.b(b),.y(net1));  //net1 = a&b
	sub_module2 u2(.a(net1),.b(c),.y(y));  //y = net1|c ,ie y = a&b + c;
endmodule
```
### Hierarchical Synthesis

- **Definition**: Retains/Preserves the *module hierarchy* as defined in RTL, synthesizing modules separately.
- **How it Works**: Tools like Yosys process each module independently, using commands such as `hierarchy` to analyze and set up the design structure.

**Advantages:**
- Speeds up synthesis for very large designs (incremental compilation).
- Easier to debug and read (submodules are preserved).
- Better reusability (submodules can be reused in other designs).
- Modular approach, aiding integration with other tools.

**Disadvantages:**
- The tool cannot always optimize across module boundaries.
- Reporting can require additional configuration.
- Might lead to slightly larger area because some redundant logic inside submodules can’t be merged away.

**Example:**
<img width="1055" height="576" alt="Image" src="https://github.com/user-attachments/assets/322138aa-a044-4604-a6d8-612fe24dc9c0" />

# The Hierarchical process in Nutshell
- Divide and Conquer Approach 
```bash
# read Lib
read_liberty -lib /home/vsdgp/vsdflow/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
#read design
read_verilog ./multiple_modules.v
#synthesise
synth -top multiple_modules
#generate netlist 
abc -liberty /home/vsdgp/vsdflow/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
#show the output
show multiple_modules
#write the netlist
write_verilog multiple_modules_hier1.v
```
## Option 2: Additional Learning  Explicit hierarchy (recommended)
```bash
# hierarchy -check -top multiple_modules
```
<img width="533" height="259" alt="Image" src="https://github.com/user-attachments/assets/9fb95794-0e23-4433-a0bd-a8b9db93bae7" />

### Flattened Synthesis

- **Definition**: Merges all modules into a single flat netlist, eliminating hierarchy.
- **How it Works**: The `flatten` command in Yosys collapses the hierarchy, allowing whole-design optimizations.

**Advantages:**
- Enables aggressive, cross-module optimizations.
- Results in a unified netlist, sometimes simplifying downstream processes.

**Disadvantages:**
- Longer runtime for large designs.
- Loss of hierarchy complicates debugging and reporting.
- Can increase memory usage and netlist complexity.

**Flatten switch with netlist as shown:**

<img width="923" height="177" alt="Image" src="https://github.com/user-attachments/assets/bf28dd46-6221-4534-81b0-df4074de1028" />


> **Important:** Hierarchical synthesis maintains sub-modules in the design, while flattening produces a netlist from the ground up.

## ⚡ Why NAND is preferred (Stacking reason: A question asked during the class session)

- PMOS devices are weaker than NMOS (lower mobility, ~2–3× worse drive current).
- In NOR gates, PMOS are in *series* → effective resistance adds up, making them slow and power-hungry.
- In NAND gates, PMOS are in *parallel* → no stacking penalty, so pull-up is fast. 
- Ttherefor, NAND gates scale better with more inputs.
- NOR gates suffer badly as the number of inputs increases (series PMOS stack = sluggish pull-up).
---

### Key Differences

| Aspect                | Hierarchical Synthesis             | Flattened Synthesis           |
|-----------------------|------------------------------------|------------------------------|
| Hierarchy             | Preserved                          | Collapsed                    |
| Optimization Scope    | Module-level only                  | Whole-design                 |
| Runtime               | Faster for large designs           | Slower for large designs     |
| Debugging             | Easier (traces to RTL)             | Harder                       |
| Output Complexity     | Modular structure                  | Single, complex netlist      |
| Use Case              | Modularity, analysis, reporting    | Maximum optimization         |

---

## Flip-Flop Coding Styles

Flip-flops are fundamental sequential elements in digital design, used to store binary data. Below are efficient coding styles for different reset/set behaviors.


<img width="1343" height="530" alt="Image" src="https://github.com/user-attachments/assets/ac448c9b-aefb-4394-8473-58624512e56c" />


### Asynchronous Reset D Flip-Flop
- Here irrespective of Clock the reset can play it role.
- **Asynchronous reset**: Overrides clock, setting q to 0 immediately.
- **Edge-triggered**: Captures input `d` on rising clock edge iff (if and only if) the reset is level low.

```verilog
module dff_asyncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
  always @ (posedge clk, posedge async_reset)
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```

### Synchronous Reset D Flip-Flop
- **Synchronous reset**: Cannot overrides the Clock, it will set q to 1 only when clock and synch reset are High.

```verilog
module dff_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk )
begin
	if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```


### Asynchronous and Synchronous Reset D Flip-Flop
- **Synchronous reset**: Takes effect only on the clock edge.
- **Asynchronous reset:* Takes effect even when clock signal is low.

```verilog
module dff_asyncres_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk , posedge async_reset)
begin
	if(async_reset)
		q <= 1'b0;
	else if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```
--

## Simulation and Synthesis Workflow

### Icarus Verilog Simulation

1. **Compile:**
   ```shell
   iverilog dff_asyncres.v tb_dff_asyncres.v
   ```
2. **Run:**
   ```shell
   ./a.out
   ```
3. **View Waveform:**
   ```shell
   gtkwave tb_dff_asyncres.vcd
   ```
![Screenshot_2025-05-30_10-45-13](https://github.com/user-attachments/assets/1176581e-fd6c-4b71-8af5-5d7d5f6dbcda)


### Synthesis with Yosys

1. Start Yosys:
   ```shell
   yosys
   ```
2. Read Liberty library:
   ```shell
   read_liberty -lib /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
3. Read Verilog code:
   ```shell
   read_verilog /path/to/dff_asyncres.v
   ```
4. Synthesize:
   ```shell
   synth -top dff_asyncres
   ```
5. Map flip-flops:
   ```shell
   dfflibmap -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
6. Technology mapping:
   ```shell
   abc -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
7. Visualize the gate-level netlist:
   ```shell
   show
   ```
![Screenshot_2025-05-30_11-03-00](https://github.com/user-attachments/assets/fa8337df-e0ec-4b01-9b18-5910768e4421)


---
## Summary
This overview provides you with practical insights into timing libraries, synthesis strategies, and reliable coding practices for flip-flops. Continue experimenting with these concepts to deepen your understanding of RTL design and synthesis.
