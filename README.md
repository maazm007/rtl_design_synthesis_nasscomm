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

![Pic1](https://github.com/user-attachments/assets/c2cd11f0-b489-44b1-b03c-99a4cfee8e72)

<img width="960" alt="Pic2" src="https://github.com/user-attachments/assets/a59685f6-9a2e-4c0b-bf32-cc8a76c5d2cd" />

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

![Pic3](https://github.com/user-attachments/assets/b24fb60a-acc8-4999-b64e-1f978bc30821)

<img width="960" alt="Pic4" src="https://github.com/user-attachments/assets/45d833ec-fd55-49bc-b475-0887d0d6eb36" />

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

***Following image shows the difference between different flavors of AND Gate:***

<img width="960" alt="Pic5" src="https://github.com/user-attachments/assets/904e3825-23b5-414d-8b2e-e3f50cbba55a" />

  
> ***All the three files belongs to 2-input AND Gate, but their Area and Leakage Powers are different. Larger area of gates signifies Wide Transistors***

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

![Pic6](https://github.com/user-attachments/assets/81e7b78b-1be0-4925-b831-3caf91242705)

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

![Pic7](https://github.com/user-attachments/assets/b4223d8d-922a-41cb-8a79-b5713c12d66d)

<img width="960" alt="Pic8" src="https://github.com/user-attachments/assets/376cff67-7d29-4dff-a76f-711295373428" />

## ***Lab 7: Synchronous and Asynchronous Set/Reset***
In Synchronous Circuits, Set/Reset pin is synchronized with the edge of the clock (either posedge or negedge). Set/Reset signal is only sampled on the clock edges. While in Asynchronous Circuits, Set/Reset pin is independent of the edge of clock. Set/Reset signal holds the higher priority in the case of asynchronous circuits.

***Now, we are going to perform the simulation and synthesis of D Flip-Flop using Synchronous and Asynchronous Set/Reset***

* D Flip-Flop with Asynchronous Reset<p> </p>
**Following are the commands:**   
<p> </p>   


```
iverilog dff_asyncres.v tb_dff_asyncres.v
./a.out
gtkwave tb_dff_asyncres.vcd
```

<img width="960" alt="Pic9" src="https://github.com/user-attachments/assets/eda76ae0-4d80-409c-9972-cc24ba3ae3fe" />
<p> </p>  

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

![Pic10](https://github.com/user-attachments/assets/a0dfb330-0ec2-46e7-b9ce-3d19eafa27e3)

* D Flip-Flop with Synchronous Reset <p> </p>
**Following are the commands:** 
<p> </p>   


```
iverilog dff_syncres.v tb_dff_syncres.v 
./a.out
gtkwave tb_dff_syncres.vcd
```

<img width="960" alt="Pic11" src="https://github.com/user-attachments/assets/cea9809e-7d06-4e56-b96c-ee18b5f08d5f" />
<p> </p>  

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

![Pic12](https://github.com/user-attachments/assets/d8d619d5-a415-4ba5-9702-83a926b91cc3)

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
Let's take an example of Boolean expression ```Y = A ? 1 : B```  
Above Boolean expression can be synthesized into 2x1 Multiplexer. Below image represent the block diagram, along with the logic optimization using Boolean theorems.

<img width="488" alt="Pic13" src="https://github.com/user-attachments/assets/83f1989d-3ebe-4b43-bec7-311a12fea53a" />  
<p> </p>

***Following are the commands to perform optimization:***
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check2.v
synth -top opt_check2
opt_clean -purge
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog opt_check2_netlist.v
```

![Pic14](https://github.com/user-attachments/assets/d4989caf-d812-427b-ae63-cb70e728ba39)

Let's take another example of Boolean expression ```Y = A ? (B ? (A & C) : C) : (!C)```
Above Boolean expression can be synthesized using two 2x1 Multiplexer. Below image represent the block diagram, along with the logic optimization using Boolean theorems.

<img width="816" alt="Pic15" src="https://github.com/user-attachments/assets/cfd7529d-9fc6-4a35-ad65-dec673446a50" />
<p>  </p>

***Following are the commands to perform optimization:***
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check4.v
synth -top opt_check4
opt_clean -purge
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog opt_check4_netlist.v
```

![Pic16](https://github.com/user-attachments/assets/8594c113-51ec-4d5d-b57a-f45c229b3c61)

Let's take another example of Verilog file ```multiple_module_opt.v```
Below image represent the block diagram, along with the logic optimization using Boolean theorems.

![Pic17](https://github.com/user-attachments/assets/f226ea9a-190f-499f-a36d-1f576cec6b6e)
<p> </p>

***Following are the commands to perform optimization:***
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

![Pic18](https://github.com/user-attachments/assets/4a893d0a-f666-499b-813e-0b319cb41380)

Let's take another example of Verilog file ```multiple_module_opt2.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

![Pic19](https://github.com/user-attachments/assets/f1fde985-43db-481e-b303-981850c53d02)
<p> </p>

***Following are the commands to perform optimization:***
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

![Pic20](https://github.com/user-attachments/assets/db17e83f-6bab-4d85-80cd-ab5701cc3bbd)

## ***Lab 9: Sequential Logic Optimization***
Let's take 5 sequential circuits one by one and analyze their behavior and understands the possible optimization that can be done. Starting with Verilog file ```dff_const1.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

<img width="460" alt="Pic21" src="https://github.com/user-attachments/assets/f22dbf5b-a74b-41fd-8f48-5d058042c57d" />
<p> </p>

***Following are the commands to perform optimization:***
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Pic22](https://github.com/user-attachments/assets/8a195754-9f65-4b35-98f0-3ac8bb88f552)

![Pic23](https://github.com/user-attachments/assets/2bbed217-42b2-4173-b391-fc59b1280afc)

***Conclusion: We expected one D Flip-Flop through our logic simulation. Bingo! One D Flip-Flop has been inferred after synthesizing the circuit***

Let's take another example of Verilog file ```dff_const2.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

<img width="499" alt="Pic24" src="https://github.com/user-attachments/assets/fb5bd8c9-4c75-4b50-9024-3605e9d3086b" />
<p> </p>

***Following are the commands to perform optimization:***
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const2.v
synth -top dff_const2
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Pic25](https://github.com/user-attachments/assets/bb52a0f3-1640-4c59-ba37-29ffc70428e0)

![Pic26](https://github.com/user-attachments/assets/0e53a72f-0e6e-4e60-b6fa-31f64a4d3c4f)

***Conclusion: We expected one D Flip-Flop through our logic simulation. But we found that output Q remains 1 irrespective of the input. Hence the logic has been optimized after synthesis***

Let's take another example of Verilog file ```dff_const3.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

<img width="619" alt="Pic27" src="https://github.com/user-attachments/assets/d3bd4bbf-35b7-4e94-901d-80754d921766" />
<p> </p>

***Following are the commands to perform optimization:***
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
synth -top dff_const3
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Pic28](https://github.com/user-attachments/assets/87a6626d-71f5-4ab8-9a38-2d100ae623ad)

<img width="248" alt="Pic29" src="https://github.com/user-attachments/assets/b6bf76d1-30d8-4b46-a354-79b6263ae065" />

![Pic30](https://github.com/user-attachments/assets/0b02391d-157d-4918-b996-0727fc03e050)

***Conclusion: We expected two D Flip-Flop through our logic simulation. Bingo! Two D Flip-Flop has been inferred after synthesizing the circuit***

Let's take another example of Verilog file ```dff_const4.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

<img width="737" alt="Pic31" src="https://github.com/user-attachments/assets/ad9f8098-897f-4f15-9d77-083277f14250" />
<p> </p>

***Following are the commands to perform optimization:***
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const4.v
synth -top dff_const4
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Pic32](https://github.com/user-attachments/assets/68228e99-012e-4261-98d0-7f923fd3a300)

![Pic33](https://github.com/user-attachments/assets/154cfc85-ab5d-4bda-bf9e-515935faf0ca)

***Conclusion: We expected two D Flip-Flop through our logic simulation. But our logic got optimized and uses no D Flip-Flop after synthesis***

Let's take another example of Verilog file ```dff_const5.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

<img width="733" alt="Pic34" src="https://github.com/user-attachments/assets/169e7719-2498-4a9a-880e-6a50901d8516" />
<p> </p>

***Following are the commands to perform optimization:***
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const5.v
synth -top dff_const5
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Pic35](https://github.com/user-attachments/assets/685cb064-a9e1-4c5e-bb01-f5b97578ee0a)

<img width="242" alt="Pic36" src="https://github.com/user-attachments/assets/8e35c1a0-e04e-466f-9702-6bad05e67d02" />

![Pic37](https://github.com/user-attachments/assets/de06970c-b048-4411-af72-bd733f08cf14)

***Conclusion: We expected two D Flip-Flop through our logic simulation. Bingo! Two D Flip-Flop has been inferred after synthesizing the circuit***

Let's take another example of Verilog file ```counter_opt.v```
Below image represents the block diagram, along with the logic optimization using Boolean theorems.

<img width="800" alt="Pic38" src="https://github.com/user-attachments/assets/ea281381-1fd3-4d7e-aa96-7cc22c919554" />
<p> </p>

***Following are the commands to perform optimization:***
```
read_liberty -lib /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/maazm007/Desktop/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Pic39](https://github.com/user-attachments/assets/1dae5a9f-943e-4853-b8e3-fcb4c68aa8ed)

![Pic40](https://github.com/user-attachments/assets/c3a9c5a8-0192-42f8-a624-d765dda3db6e)

***Conclusion: We expected three D Flip-Flop through our logic simulation. But only one D Flip-Flop has been inferred after synthesizing the circuit. Because of presence of Unused Outputs, the logic has been optimized***

----------------------------------------------------------------------------------------------------------------------

## ***Day 4: GLS, Blocking vs Non-Blocking and Synthesis Simulation Mismatch***  













