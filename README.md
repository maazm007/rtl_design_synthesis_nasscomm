#  ***NASSCOM RTL DESIGN AND SYNTHESIS Program 2025***

## ***Basic Details***

**Name:** Maaz Mahmood  
**College:** KIET Group of Institutions    
**Email ID:** maazms999@gmail.com  
**GitHub Profile:** [maazm007](https://github.com/maazm007?tab=repositories)  
**LinkedIN Profile:** [maazm-ece-vlsi](https://www.linkedin.com/in/maazms-ece-vlsi/)

---------------------------------------------------------------------------------------------------------------------------- 

## ***Day 1: Introduction to Verilog RTL Design and Synthesis***  

### ***What is Design File?***
Design File refers to source code file written in Hardware Descriptive Language like Verilog or VHDL that describe the behavior and structure of digital circuit to meet the desired specifications

### ***What is Testbench File?***
Testbench is the file use to verify the correctness of the digital circuit that we have designed using HDL. It is also written in either Verilog or VHDL. It applies the set of stimulus to the design to evaluate the functionality of the design.

### ***What is Simulator?***
Simulator is the software tool that helps to perform the simulation without physically implementing the hardware. Simulator takes design file and testbench file as its input. It generate the output whenever any input signal changes. In this workshop, we are going to use ```iverilog``` simulator and ```gtkwave``` to view the waveforms of our simulations.


## ***Lab 1: Cloning and Installing the SkyWater 130 PDK***

Use the following code to clone the required repository:
```
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
```


## ***Lab 2: Using iverilog to simulate the Multiplexer design***

Use the following command to perform the iverilog simulations:
```
iverilog good_mux.v tb_good_mux.v
```

After performing this step, ```a.out``` will be generated. To run the ***a.out*** file, type the below command in the terminal:

```
./a.out
```

After running this command, ```.vcd``` file will be generated. VCD stands for ***Value Change Dump***. ```.vcd``` file can opened using ```gtkwave``` waveform analyzer. To open the ***.vcd*** file using ***gtkwave***, type the below command in the terminal:

```
gtkwave tb_good_mux.vcd
```

[Picture 1]

[Picture 2]

### ***What is Synthesis?***
Synthesis refers to the automated process of converting a high-level hardware description written in Verilog or VHDL, into the gate level netlist consisting of logic gates, flip-flops, which can be physically implemented on silicon. Here, we will be using ```Yosys``` tool to perform the synthesis.

### ***What is .lib file?***
It stands for Liberty timing file. It contains the timing, power and functional information of standard cells used during synthesis. It also play an important role in Logic Synthesis. It contains different flavors of same gate modules. Different flavors refers to different specifications such as there would be three types of 2-input NAND Gate, slow, medium and fast. There variants of same cells plays a crucial role when determining the speed of operation of circuit. Some component of circuit uses fast cells while some uses slow cells so as to meet the Setup and Hold Time constraints.

## ***Lab 3: Using yosys tool to perform synthesis of the Multiplexer design***

Open the terminal and then invoke the YOSYS by simply typing ```yosys``` in the terminal. YOSYS prompt will be opened. Type the below commands to carryout the synthesis:
```
read_liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog good_mux.v
synth -top good_mux
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr good_mux_netlist.v
```
### ***What is abc command?***
```abc``` is the command used for technology mapping. It maps the optimized logic circuit to the actual gates present in a technology library.

[Picture 3]

[Picture 4]

---------------------------------------------------------------------------------------------------------------

## ***Day 2: Timing Libs, Hierarchical vs Flat Synthesis and Efficient Flop Coding Styles***  

## ***Lab 4: Analyzing ```sky130_fd_sc_hd_tt_025C_1v80.lib``` file***
The file ```sky130_fd_sc_hd_tt_025C_1v80.lib``` is a Liberty (.lib) file from the SkyWater SKY130 open-source Process Design Kit (PDK) for 130nm CMOS technology.

Each term in the file name has its own meanings:
* **sky130:** Technology node 130nm
* **fd_sc_hd:** It is standard cell library abbreviated as Fully Depeleted, Standard Cells, High Density
* **tt:** Process involved (typical)
* **025C:** 25°C temperature
* **1v80:** Supply Voltage of 1.80V

The ```.lib``` file contains following information:
* Cell Information like number of ports and area it occupy
* Timing Information such as Setup Time, Hold Time, Clock-to-Q delay, Slew Rates
* Power Information like Switching Power, Leakage Power, etc.

Following image shows the difference between different flavors of AND Gate:

[Picture 5]

**All the three files belongs to 2-input AND Gate, but their Area and Leakage Powers are different. Larger area of gates signifies Wide Transistors**

## ***Lab 5: Hierarchical vs Flat synthesis using multiple_modules.v***
We use Hierarchical Synthesis when we want to preserve the module hierarchy. For Example, ```multiple_modules.v``` contains two sub modules, so in order to preserve both the submodules in the synthesis, we choose Hierarchical Synthesis. Following are the commands to perform the hierarchical synthesis of ```multiple_modules.v```:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top multiple_modules
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show multiple_modules
write_verilog -noattr multiple_modules_hier.v
```

[Picture 6]

We use Flat Synthesis when we don't want to preserve the modules hierarchy. Here all the submodules are merged into the single module. Flat Synthesis is generally preferred for simpler designs. Following are the commands to perform flat synthesis of ```multiple_modules.v```:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top multiple_modules
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
flatten
show
write_verilog -noattr multiple_modules_flat.v
```

[Picture 7]

[Picture 8]

## ***Lab 7: Synchronous and Asynchronous Set/Reset***
In Synchronous Circuits, Set/Reset pin is synchronized with the edge of the clock (either posedge or negedge). Set/Reset signal is only sampled on the clock edges. While in Asynchronous Circuits, Set/Reset pin is independent of the edge of clock. Set/Reset signal holds the higher priority in the case of asynchronous circuits.

***Now, we are goin to perform the simulation and synthesis of D Flip-Flop using Synchronous and Asynchronous Set/Reset***

* D Flip-Flop with Asynchronous Reset
**Following are the commands:**
```
iverilog dff_asyncres.v tb_dff_asyncres.v
./a.out
gtkwave tb_dff_asyncres.vcd
```

[Picture 9]

```
yosys
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres.v
synth -top dff_asyncres
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr dff_asyncres_netlist.v
```

[Picture 10]

* D Flip-Flop with Synchronous Reset
**Following are the commands:**
```
iverilog dff_syncres.v tb_dff_syncres.v 
./a.out
gtkwave tb_dff_syncres.vcd
```

[Picture 11]

```
yosys
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_syncres.v
synth -top dff_syncres
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr dff_syncres_netlist.v
```

[Picture 12]

-----------------------------------------------------------------------------------------------------------------------------

## ***Day 3: Combinational and Sequential Optimizations***  
Optimization in both combinational and sequential circuits refers to the process of improving the circuit to achieve better performance, lower cost, reduced power consumption and reduce area occupied while maintaining correct functionality.

There are two techniques for Combinational Optimization:
* **Constant Propagation**
* **Boolean Optimization like Karnaugh-Map Reduction, Quine McCluskey Method, etc.**

There are four techniques for Sequential Optimization:
* **Constant Propagation**
* **State Optimization**
* **Retiming of Circuit**
* **Sequential Logic Cleaning**  

## ***Lab 8: Combinational Logic Optimization***
Let's take an example of Boolean expression ```Y = A ? B : 1```  
Above Boolean expression can be synthesized into 2x1 Multiplexer. Below image represent the block diagram, along with the logic optimization using Boolean theorems.

[Picture 13]

Following are the commands to perform optimization:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check2.v
synth -top opt_check2
opt_clean -purge
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog opt_check2_netlist.v
```

[Picture 14]

Let's take another example of Boolean expression ```Y = A ? (B ? (A & C) : C) : (!C)```
Above Boolean expression can be synthesized using two 2x1 Multiplexer. Below image represent the block diagram, along with the logic optimization using Boolean theorems.

[Picture 15]

Following are the commands to perform optimization:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check4.v
synth -top opt_check4
opt_clean -purge
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog opt_check4_netlist.v
```

[Picture 16]

Let's take another example of Verilog file ```multiple_module_opt.v```
Below image represent the block diagram, along with the logic optimization using Boolean theorems.

[Picture 17]

Following are the commands to perform optimization:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_module_opt.v
synth -top multiple_module_opt
flatten
opt_clean -purge
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog multiple_module_opt_netlist.v
```

[Picture 18]

Let's take another example of Verilog file ```multiple_module_opt2.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

[Picture 19]

Following are the commands to perform optimization:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_module_opt2.v
synth -top multiple_module_opt2
flatten
opt_clean -purge
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog multiple_module_opt2_netlist.v
```

[Picture 20]

## ***Lab 9: Sequential Logic Optimization***
Let's take 5 sequential circuits one by one and analyze their behavior and understands the possible optimization that can be done. Starting with Verilog file ```dff_const1.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

[Picture 21]

Following are the commands to perform optimization:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

[Picture 22]

[Picture 23]

***Conclusion: We expected one D Flip-Flop through our logic simulation. Bingo! One D Flip-Flop has been inferred after synthesizing the circuit***

Let's take another example of Verilog file ```dff_const2.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

[Picture 24]

Following are the commands to perform optimization:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const2.v
synth -top dff_const2
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

[Picture 25]

[Picture 26]

***Conclusion: We expected one D Flip-Flop through our logic simulation. But we found that output Q remains 1 irrespective of the input. Hence the logic has been optimized after synthesis***

Let's take another example of Verilog file ```dff_const3.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

[Picture 27]

Following are the commands to perform optimization:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
synth -top dff_const3
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
[Picture 28]

[Picture 29]

[Picture 30]

***Conclusion: We expected two D Flip-Flop through our logic simulation. Bingo! Two D Flip-Flop has been inferred after synthesizing the circuit***

Let's take another example of Verilog file ```dff_const4.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

[Picture 31]

Following are the commands to perform optimization:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const4.v
synth -top dff_const4
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
[Picture 32]

[Picture 33]

***Conclusion: We expected two D Flip-Flop through our logic simulation. But our logic got optimized and uses no D Flip-Flop after synthesis***

Let's take another example of Verilog file ```dff_const5.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

[Picture 34]

Following are the commands to perform optimization:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const5.v
synth -top dff_const5
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

[Picture 35]

[Picture 36]

[Picture 37]

***Conclusion: We expected two D Flip-Flop through our logic simulation. Bingo! Two D Flip-Flop has been inferred after synthesizing the circuit***

Let's take another example of Verilog file ```counter_opt.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

[Picture 38]

Following are the commands to perform optimization:
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

[Picture 39] 

[Picture 40]

***Conclusion: We expected three D Flip-Flop through our logic simulation. But only one D Flip-Flop has been inferred after synthesizing the circuit. Because of presence of Unused Outputs, the logic has been optimized***

----------------------------------------------------------------------------------------------------------------------

## ***Day 4: GLS, Blocking vs Non-Blocking and Synthesis Simulation Mismatch***  













