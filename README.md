# Advanced-Physical-Design-Using-OpenLane-Sky130


This project is done in the course ["Advanced Physical Design using OpenLANE/Sky130"](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/) by VLSI System Design Corporation. In this project a complete RTL to GDSII flow for PicoRV32a SoC is executed with Openlane using Skywater130nm PDK. Custom designed standard cells with Sky130 PDK are also used in the flow. Timing Optimisations are carried out. Slack violations are removed. DRC is verified.


## Day 1: Inception of open-source EDA, OpenLane and SKY130pdk.

<details>
<summary>Overview</summary>

	
 OpenLANE is an opensource tool or flow used for opensource tape-outs. The OpenLANE flow comprises a variety of tools such as Yosys, ABC, OpenSTA, Fault, OpenROAD app, Netgen and Magic which are used to harden chips and macros, i.e. generate final GDSII from the design RTL. The primary goal of OpenLANE is to produce clean GDSII with no human intervention. OpenLANE has been tuned to function for the Google-Skywater130 Opensource Process Design Kit.

## Inception of Opensource EDA

### How to talk to computers?

The RISC-V Instruction Set Architecture (ISA) is a language used to talk to computers whose hardware is based on RISC-V core. If a user wishes to run a certain application software on a computer, its corresponding C/C++/Java program must be converted into instructions by the compliler. The ouput of the compiler is hardware dependent. These instructions go as inputs to the assembler which outputs binary language that the hardware logic in the chip layout can make sense of. According to the bits received, the digital logic consisting of gates performs the function required by the user of the application software.

</details>

<details>
<summary>SoC Design & OpenLANE</summary>


#### Components of opensource digital ASIC design
The design of digital Application Specific Integrated Circuit (ASIC) requires three enablers or elements - Resistor Transistor Logic Intellectual Property (RTL IPs), Electronic Design Automation (EDA) Tools and Process Design Kit (PDK) data.

![ASIC](https://user-images.githubusercontent.com/86701156/124005001-35a32a80-d9f6-11eb-8fcc-0917ad337699.PNG)

- Opensource RTL Designs: github, librecores, opencores
- Opensource EDA tools: QFlow, OpenROAD, OpenLANE
- Opensource PDK data: Google Skywater130 PDK

The ASIC flow objective is to convert RTL design to GDSII format used for final layout. The flow is essentially a software also known as automated PnR (Place & route).

#### Simplified RTL2GDS Flow

![RTL2GDS flow](https://user-images.githubusercontent.com/86701156/124006238-a139c780-d9f7-11eb-8da9-6069b055fbe0.PNG)



- Synthesis: RTL Converted to gate level netlist using standard cell libraries (SCL)
- Floor & Power Planning: Planning of silicon area to ensure robust power distribution
- Placement: Placing cells on floorplan rows aligned with sites
  - Global Placement: for optimal position of cells
  - Detailed Placement: for legal positions
- Routing: Valid patterns for wires
- Signoff: Physical (DRC, LVS) and Timing verifications (STA)

#### OpenLANE ASIC Flow

![3  opensource ASIC digital designs-1](https://user-images.githubusercontent.com/83152452/185787620-8c999b89-2580-477d-aa20-1156d3e996c8.png)

![OpenLaneflow](https://user-images.githubusercontent.com/83152452/131135115-46148ff1-9489-48f6-a334-6702c25def59.png)

From conception to product, the ASIC design flow is an iterative process that is not static for every design. The details of the flow may change depending on ECO’s, IP requirements, DFT insertion, and SDC constraints, however the base concepts still remain. The flow can be broken down into 11 steps:

1. Architectural Design – A system engineer will provide the VLSI engineer with specifications for the system that are determined through physical constraints. 
   The VLSI engineer will be required to design a circuit that meets these constraints at a microarchitecture modeling level.

2. RTL Design/Behavioral Modeling – RTL design and behavioral modeling are performed with a hardware description language (HDL). 
   EDA tools will use the HDL to perform mapping of higher-level components to the transistor level needed for physical implementation. 
   HDL modeling is normally performed using either Verilog or VHDL. One of two design methods may be employed while creating the HDL of a microarchitecture:
   
    a. RTL Design – Stands for Register Transfer Level. It provides an abstraction of the digital   circuit using:
   
   - i. 	 Combinational logic
   - ii. 	 Registers
   - iii.  Modules (IP’s or Soft Macros)
 
    b. 	Behavioral Modeling – Allows the microarchitecture modeling to be performed with behavior-based modeling in HDL. This method bridges the gap between C and HDL allowing HDL design to be performed

3. RTL Verification - Behavioral verification of design

4. DFT Insertion - Design-for-Test Circuit Insertion

5. Logic Synthesis – Logic synthesis uses the RTL netlist to perform HDL technology mapping. The synthesis process is normally performed in two major steps:

     - GTECH Mapping – Consists of mapping the HDL netlist to generic gates what are used to perform logical optimization based on AIGERs and other topologies created 
       from the generic mapped netlist.
       
     - Technology Mapping – Consists of mapping the post-optimized GTECH netlist to standard cells described in the PDK
  
6. Standard Cells – Standard cells are fixed height and a multiple of unit size width. This width is an integer multiple of the SITE size or the PR boundary. Each standard cell comes with SPICE, HDL, liberty, layout (detailed and abstract) files used by different tools at different stages in the RTL2GDS flow.

7. Post-Synthesis STA Analysis: Performs setup analysis on different path groups.

8. Floorplanning – Goal is to plan the silicon area and create a robust power distribution network (PDN) to power each of the individual components of the synthesized netlist. In addition, macro placement and blockages must be defined before placement occurs to ensure a legalized GDS file. In power planning we create the ring which is connected to the pads which brings power around the edges of the chip. We also include power straps to bring power to the middle of the chip using higher metal layers which reduces IR drop and electro-migration problem.

9. Placement – Place the standard cells on the floorplane rows, aligned with sites defined in the technology lef file. Placement is done in two steps: Global and Detailed. In Global placement tries to find optimal position for all cells but they may be overlapping and not aligned to rows, detailed placement takes the global placement and legalizes all of the placements trying to adhere to what the global placement wants.

10. CTS – Clock tree synteshsis is used to create the clock distribution network that is used to deliver the clock to all sequential elements. The main goal is to create a network with minimal skew across the chip. H-trees are a common network topology that is used to achieve this goal.

11. Routing – Implements the interconnect system between standard cells using the remaining available metal layers after CTS and PDN generation. The routing is performed on routing grids to ensure minimal DRC errors.

The Skywater 130nm PDK uses 6 metal layers to perform CTS, PDN generation, and interconnect routing.

</details>

<details>
<summary>Opensource EDA tools</summary>

OpenLANE utilises a variety of opensource tools in the execution of the ASIC flow:
Task | Tool/s
------------ | -------------
RTL Synthesis & Technology Mapping | [yosys](https://github.com/YosysHQ/yosys), abc
Floorplan & PDN | init_fp, ioPlacer, pdn and tapcell
Placement | RePLace, Resizer, OpenPhySyn & OpenDP
Static Timing Analysis | [OpenSTA](https://github.com/The-OpenROAD-Project/OpenSTA)
Clock Tree Synthesis | [TritonCTS](https://github.com/The-OpenROAD-Project/OpenLane)
Routing | FastRoute and [TritonRoute](https://github.com/The-OpenROAD-Project/TritonRoute) 
SPEF Extraction | [SPEF-Extractor](https://github.com/HanyMoussa/SPEF_EXTRACTOR)
DRC Checks, GDSII Streaming out | [Magic](https://github.com/RTimothyEdwards/magic), [Klayout](https://github.com/KLayout/klayout)
LVS check | [Netgen](https://github.com/RTimothyEdwards/netgen)
Circuit validity checker | [CVC](https://github.com/d-m-bailey/cvc)

#### OpenLANE design stages

1. Synthesis
	- `yosys` - Performs RTL synthesis
	- `abc` - Performs technology mapping
	- `OpenSTA` - Performs static timing analysis on the resulting netlist to generate timing reports
2. Floorplan and PDN
	- `init_fp` - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
	- `ioplacer` - Places the macro input and output ports
	- `pdn` - Generates the power distribution network
	- `tapcell` - Inserts welltap and decap cells in the floorplan
3. Placement
	- `RePLace` - Performs global placement
	- `Resizer` - Performs optional optimizations on the design
	- `OpenDP` - Perfroms detailed placement to legalize the globally placed components
4. CTS
	- `TritonCTS` - Synthesizes the clock distribution network (the clock tree)
5. Routing
	- `FastRoute` - Performs global routing to generate a guide file for the detailed router
	- `CU-GR` - Another option for performing global routing.
	- `TritonRoute` - Performs detailed routing
	- `SPEF-Extractor` - Performs SPEF extraction
6. GDSII Generation
	- `Magic` - Streams out the final GDSII layout file from the routed def
	- `Klayout` - Streams out the final GDSII layout file from the routed def as a back-up
7. Checks
	- `Magic` - Performs DRC Checks & Antenna Checks
	- `Klayout` - Performs DRC Checks
	- `Netgen` - Performs LVS Checks
	- `CVC` - Performs Circuit Validity Checks

### Simplified RTL to GDSII flow 

The RTL to GDSII flow basically involves :
1. **RTL Design** -  The process begins with the RTL design phase, where the digital circuit is described using a hardware description language (HDL) like VHDL or Verilog. The RTL description captures the functional behavior of the circuit, specifying its logic and data paths.

2. **RTL Synthesis** - RTL synthesis converts the high-level RTL description into a gate-level netlist. This stage involves mapping the RTL code to a library of standard cells (pre-designed logic elements) and optimizing the resulting gate-level representation for area, power, and timing. The output of RTL synthesis is typically in a format called the gate-level netlist.

3. **Floor and Power Planning** - is a crucial step in the digital design flow that involves partitioning the chip's area and determining the placement of major components and functional blocks. It establishes an initial high-level layout and defines the overall chip dimensions, locations of critical modules, power grid distribution, and I/O placement.The primary goals of floor planning are: Area Partitioning, Power Distribution, Signal Flow and Interconnect Planning, Placement of Key Components, Design Constraints and Optimization.

4. **Placement** - Placement involves assigning the physical coordinates to each gate-level cell on the chip's layout. The placement process aims to minimize wirelength, optimize signal delay, and satisfy design rules and constraints. Modern placement algorithms use techniques like global placement and detailed placement to achieve an optimal placement solution.

5. **Clock Tree Synthesis** - Clock tree synthesis (CTS) is a crucial step in the digital design flow that involves constructing an optimized clock distribution network within an integrated circuit (IC). The primary goal of CTS is to ensure balanced and efficient clock signal distribution to all sequential elements (flip-flops, registers) within the design, minimizing clock skew and achieving timing closure.

6. **Routing** - Routing connects the gates and interconnects on the chip based on the placement information. It involves determining the optimal paths for the wires and vias that carry signals between different components. The routing process needs to adhere to design rules, avoid congestion, and optimize for factors like signal integrity, power, and manufacturability.

7. **Sign-off** - Sign-off analysis refers to the final stage of the electronic design process, where comprehensive verification and analysis are performed to ensure that the design meets all the necessary requirements and specifications. It involves a series of checks and simulations to confirm that the design is ready for fabrication and meets the desired functionality, performance, power, and reliability targets. 

8. **GDSII File Generation** - Once the layout is verified and passes all checks, the final step is to generate the GDSII file format, which represents the complete physical layout of the chip. The GDSII file contains the geometric information necessary for fabrication, including the shapes, layers, masks, and other relevant details.

### Introduction to OpenLANE
OpenLane is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, CVC, SPEF-Extractor, KLayout and a number of custom scripts for design exploration and optimization. It also provides a number of custom scripts for design exploration and optimization. The flow performs all ASIC implementation steps from RTL all the way down to GDSII. Currently, it supports both A and B variants of the sky130 PDK, the C variant of the gf180mcu PDK, and instructions to add support for other (including proprietary) PDKs are documented. OpenLane abstracts the underlying open source utilities, and allows users to configure all their behavior with just a single configuration file.

OpenLane integrated several key open source tools over the execution stages:
1. RTL Synthesis, Technology Mapping, and Formal Verification : yosys + abc
2. Static Timing Analysis: OpenSTA
3. Floor Planning: init_fp, ioPlacer, pdn and tapcell
4. Placement: RePLace (Global), Resizer and OpenPhySyn (formerly), and OpenDP (Detailed)
5. Clock Tree Synthesis: TritonCTS
6. Fill Insertion: OpenDP/filler_placement
7. Routing: FastRoute or CU-GR (formerly) and TritonRoute (Detailed) or DR-CU
8. SPEF Extraction: OpenRCX or SPEF-Extractor (formerly)
9. GDSII Streaming out: Magic and KLayout
10. DRC Checks: Magic and KLayout
11. LVS check: Netgen
12. Antenna Checks: Magic
13. Circuit Validity Checker: CVC


### OpenLane Installation
Prior to the installation of the OpenLane install the dependencies and packages using the command shown below :</br>
``` 
sudo apt-get update
sudo apt-get upgrade
sudo apt install -y build-essential python3 python3-venv python3-pip make git
```
Docker Installation :</br>
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
sudo docker run hello-world

sudo groupadd docker
sudo usermod -aG docker $USER
sudo reboot 


# Check for installation
sudo docker run hello-world
```

**Steps to install OpenLane, PDKs and Tools**</br>
```
cd $HOME
git clone https://github.com/The-OpenROAD-Project/OpenLane --recurse-submodules 
cd OpenLane
make
make test
cd /home/shubham/OpenLane/designs/ci
cp -r * ../
```

### Steps to run synthesis in OpenLane:


```
cd ~/OpenLane
make mount
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
run_synthesis
```
To view nelist
```
cd /home/shubham/OpenLane/designs/picorv32a/runs/RUN_2023.09.08_13.53.29/results/synthesis
vim picorv32a.v
```
#### OpenLANE Files

The openLANE file structure looks something like this:

- skywater-pdk: contains PDK files provided by foundry
- open_pdks: contains scripts to setup pdks for opensource tools 
- sky130A: contains sky130 pdk files


![Screenshot from 2023-09-10 23-58-56](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/1b5f697b-14bf-4b1c-937d-7fef6cb09061)
</details>

## Day 2: Floorplanning & Placement and library cells
<details>
<summary> Floorplanning</summary>

#### Utilization Factor & Aspect Ratio  

Two parameters are of importance when it comes to floorplanning namely, Utilisation Factor and Aspect Ratio. They are defined as follows:

```
Utilisation Factor =  Area occupied by netlist
                     __________________________
                        Total area of core
```

```
Aspect Ratio =  Height
               ________
                Width
```
                                  
A Utilisation Factor of 1 signifies 100% utilisation leaving no space for extra cells such as buffer. However, practically, the Utilisation Factor is 0.5-0.6. Likewise, an Aspect ratio of 1 implies that the chip is square shaped. Any value other than 1 implies rectanglular chip.

#### Pre-placed cells

Once the Utilisation Factor and Aspect Ratio has been decided, the locations of pre-placed cells need to be defined. Pre-placed cells are IPs comprising large combinational logic which once placed maintain a fixed position. Since they are placed before placement and routing, the are known as pre-placed cells.

#### Decoupling capacitors

Pre-placed cells must then be surrounded with decoupling capacitors (decaps). The resistances and capacitances associated with long wire lengths can cause the power supply voltage to drop significantly before reaching the logic circuits. This can lead to the signal value entering into the undefined region, outside the noise margin range. Decaps are huge capacitors charged to power supply voltage and placed close the logic circuit. Their role is to decouple the circuit from power supply by supplying the necessary amount of current to the circuit. They pervent crosstalk and enable local communication.

#### Power Planning

Each block on the chip, however, cannot have its own decap unlike the pre-placed macros. Therefore, a good power planning ensures that each block has its own VDD and VSS pads connected to the horizontal and vertical power and GND lines which form a power mesh.

#### Pin Placement

The netlist defines connectivity between logic gates. The place between the core and die is utilised for placing pins. The connectivity information coded in either VHDL or Verilog is used to determine the position of I/O pads of various pins. Then, logical placement blocking of pre-placed macros is performed so as to differentiate that area from that of the pin area.

#### Floorplan run on OpenLANE & view in Magic

* Importance files in increasing priority order:

1. ```floorplan.tcl``` - System default envrionment variables
2. ```conifg.tcl```
3. ```sky130A_sky130_fd_sc_hd_config.tcl```

* Floorplan envrionment variables or switches:

1. ```FP_CORE_UTIL``` - floorplan core utilisation
2. ```FP_ASPECT_RATIO``` - floorplan aspect ratio
3. ```FP_CORE_MARGIN``` - Core to die margin area
4. ```FP_IO_MODE``` - defines pin configurations (1 = equidistant/0 = not equidistant)
5. ```FP_CORE_VMETAL``` - vertical metal layer
6. ```FP_CORE_HMETAL``` - horizontal metal layer

***Note: Usually, vertical metal layer and horizontal metal layer values will be 1 more than that specified in the files***
 
 To run the picorv32a floorplan in openLANE:
 ```
 run_floorplan
 
 ```
 ![openlane - placement - run_floorplan](https://user-images.githubusercontent.com/83152452/185788662-a73dca29-98a9-4b78-9297-61f8f9bc7a47.png)
 

Post the floorplan run, a .def file will have been created within the ```results/floorplan``` directory. 
We may review floorplan files by checking the ```floorplan.tcl```. 
The system defaults will have been overriden by switches set in ```conifg.tcl``` and further overriden by switches set in ```sky130A_sky130_fd_sc_hd_config.tcl```.
 
To view the floorplan, Magic is invoked after moving to the ```results/floorplan``` directory:
```
magic -T /home/shubham/.volare/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read picorv32.def &
```


![Screenshot from 2023-09-16 06-59-08](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/c7a581e4-8abf-4d90-8041-d1cb7ca03fc0)
![Screenshot from 2023-09-16 07-00-03](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/d21f1ff0-6d90-4fc2-8461-cf47090c5347)

</details>

<details>
<summary>Placement </summary>

#### Placement Optimization

The next step in the OpenLANE ASIC flow is placement. The synthesized netlist is to be placed on the floorplan. Placement is perfomed in 2 stages:

1. Global Placement: It finds optimal position for all cells which may not be legal and cells may overlap. Optimization is done through reduction of half parameter wire length.
2. Detailed Placement: It alters the position of cells post global placement so as to legalise them.

Legalisation of cells is important from timing point of view. 

#### Placement run on OpenLANE & view in Magic

Command:

```
run_placement
```
![Screenshot from 2023-09-16 10-36-26](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/20bd0d6e-8b96-4e8d-aa91-6702d6ba6d18)

![openlane - placement - run_placement-1](https://user-images.githubusercontent.com/83152452/185789104-a2284118-7e56-439e-abac-bb1ba669e72b.png)

The objective of placement is the convergence of overflow value. If overflow value progressively reduces during the placement run it implies that the design will converge and placement will be successful. Post placement, the design can be viewed on magic within ```results/placement``` directory:

```
/OpenLane/designs/picorv32a/runs/RUN_2023.09.16_05.04.39/results/placement$ magic -T /home/shubham/.volare/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read picorv32.def &
```
![Screenshot from 2023-09-16 11-06-45](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/c3c27587-ef21-4d5c-abeb-84c277004e77)
![Screenshot from 2023-09-16 11-07-07](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/5f94d9b6-23f1-46cb-a2dc-32a3e8ba9e79)

### Libraries

![Screenshot from 2023-09-16 10-15-45](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/b861ff42-b315-42ed-bb47-95275cba6a7e)
![Screenshot from 2023-09-16 10-16-16](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/a4e7caa1-f8a6-4411-8311-37d1e0145f6a)
![Screenshot from 2023-09-16 10-16-38](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/88f8129b-3ca4-4470-bd22-a1d68cfca096)
![Screenshot from 2023-09-16 10-19-07](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/fc6939de-b4eb-4a06-b474-bee6af74f298)
![Screenshot from 2023-09-16 10-19-17](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/3332db55-9fc8-4bf9-8cdb-e5f3897b9b07)
![Screenshot from 2023-09-16 10-20-10](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/77de2da8-b0fc-495e-b789-a354a4b94e17)


</details>

<details>
<summary> CELL DESIGN AND CHARACETRIZATION FLOWS</summary>

Library is a place where we get information about every cell. It has differents cells with different size, functionality,threshold voltages. There is a typical cell design flow steps.
1. Inputs : PDKS(process design kit) : DRC & LVS, SPICE Models, library & user-defined specs.
2. Design Steps :Circuit design, Layout design (Art of layout Euler's path and stick diagram), Extraction of parasitics, Characterization (timing, noise, power).
3. Outputs: CDL (circuit description language), LEF, GDSII, extracted SPICE netlist (.cir), timing, noise and power .lib files

![Screenshot from 2023-09-16 11-12-42](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/d7848c1b-1f40-4dd7-b95e-c77cd7b04a96)


### Standard Cell Characterization Flow

A typical standard cell characterization flow that is followed in the industry includes the following steps:

1. Read in the models and tech files
2. Read extracted spice Netlist
3. Recognise behavior of the cells
4. Read the subcircuits
5. Attach power sources
6. Apply stimulus to characterization setup
7. Provide neccesary output capacitance loads
8. Provide neccesary simulation commands

Now all these 8 steps are fed in together as a configuration file to a characterization software called GUNA. This software generates timing, noise, power models.
These .libs are classified as Timing characterization, power characterization and noise characterization.


### TIMING CHARACTERIZATION

In standard cell characterisation, One of the classification of libs is timing characterisation.

#### Timing threshold definitions 
Timing defintion |	Value
-------------- | --------------
slew_low_rise_thr	| 20% value
slew_high_rise_thr | 80% value
slew_low_fall_thr |	20% value
slew_high_fall_thr |	80% value
in_rise_thr	| 50% value
in_fall_thr |	50% value
out_rise_thr |	50% value
out_fall_thr | 50% value

#### Propagation Delay and Transition Time 

**Propagation Delay** 
The time difference between when the transitional input reaches 50% of its final value and when the output reaches 50% of its final value. Poor choice of threshold values lead to negative delay values. Even thought you have taken good threshold values, sometimes depending upon how good or bad the slew, the dealy might be still +ve or -ve.

```
Propagation delay = time(out_thr) - time(in_thr)
```
**Transition Time**

The time it takes the signal to move between states is the transition time , where the time is measured between 10% and 90% or 20% to 80% of the signal levels.

```
Rise transition time = time(slew_high_rise_thr) - time (slew_low_rise_thr)

Low transition time = time(slew_high_fall_thr) - time (slew_low_fall_thr)
```
</details>

## DAY 3 : Design Library Cell using magic and ngspice


<details>
<summary> Inverter</summary>
A **CMOS inverter**, short for Complementary Metal-Oxide-Semiconductor inverter, is a fundamental digital electronic circuit that performs the basic logical operation of inversion. In other words, it takes an input signal and produces an output signal that is the logical complement of the input. If the input is high (logic 1), the output will be low (logic 0), and vice versa.  

The input signal is applied to the gate terminals of both the NMOS and PMOS transistors. The output is taken from the connection point (the drain of NMOS and the source of PMOS) between these two transistors.  

**NMOS Operation**: When the input is logic high (1), the NMOS transistor turns on because a positive voltage is applied to its gate. This establishes a low-resistance path between the output and ground, causing the output to be pulled to logic low (0).  

**PMOS Operation**: Conversely, when the input is logic low (0), the PMOS transistor turns on because a low voltage is applied to its gate. This establishes a low-resistance path between the output and the power supply voltage (VDD), causing the output to be pulled to logic high (1).


Below shown the inverter diagram : 

*In This section I have used my colleuge (Prutvi) github repo for referance.*  

![inverter](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/c0f143c3-3806-4d74-a701-2d38e8475298)

Below shows nodes:   

![2inverternodes](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/08d0db99-e46f-447c-80b5-7227ad9fadbc)

Here is spice deck simulation shown:  

![3spicedeck](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/db0c1c30-0a35-417d-80b8-d48ec66ec503)

Here is a cmos.cir file: 

![image](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/013b3083-47b6-42c4-a65b-d52916dee748)

Below is the model file : 

```
* SPICE 3f5 Level 8, Star-HSPICE Level 49, UTMOST Level 8

.lib cmos_models 
* DATE: Feb 23/01
* LOT: T0BM                  WAF: 07
* Temperature_parameters=Default
.MODEL nmos  NMOS (                                LEVEL   = 49
+VERSION = 3.1            TNOM    = 27             TOX     = 5.8E-9
+XJ      = 1E-7           NCH     = 2.3549E17      VTH0    = 0.3907535
+K1      = 0.4376003      K2      = 8.265151E-3    K3      = 4.214601E-3
+K3B     = -3.7220937     W0      = 2.517345E-6    NLX     = 2.310668E-7
+DVT0W   = 0              DVT1W   = 0              DVT2W   = 0
+DVT0    = 0.2411602      DVT1    = 0.3707226      DVT2    = -0.5
+U0      = 316.5922683    UA      = -9.89493E-10   UB      = 2.154013E-18
+UC      = 2.474632E-11   VSAT    = 1.254499E5     A0      = 1.2735648
+AGS     = 0.2428704      B0      = 2.579719E-8    B1      = -1E-7
+KETA    = 4.87168E-4     A1      = 0              A2      = 0.5196633
+RDSW    = 120            PRWG    = 0.5            PRWB    = -0.2
+WR      = 1              WINT    = 2.357855E-8    LINT    = 1.210018E-9
+DWG     = 2.292632E-9
+DWB     = -9.94921E-10   VOFF    = -0.1039771     NFACTOR = 1.3905578
+CIT     = 0              CDSC    = 2.4E-4         CDSCD   = 0
+CDSCB   = 0              ETA0    = 3.894977E-3    ETAB    = 7.800632E-4
+DSUB    = 0.0307944      PCLM    = 1.7312397      PDIBLC1 = 0.999135
+PDIBLC2 = 4.850036E-3    PDIBLCB = -0.0866866     DROUT   = 0.8612131
+PSCBE1  = 7.995844E10    PSCBE2  = 1.457011E-8    PVAG    = 0.0099984
+DELTA   = 0.01           RSH     = 5              MOBMOD  = 1
+PRT     = 0              UTE     = -1.5           KT1     = -0.11
+KT1L    = 0              KT2     = 0.022          UA1     = 4.31E-9
+UB1     = -7.61E-18      UC1     = -5.6E-11       AT      = 3.3E4
+WL      = 0              WLN     = 1              WW      = -1.22182E-16
+WWN     = 1.2127         WWL     = 0              LL      = 0
+LLN     = 1              LW      = 0              LWN     = 1
+LWL     = 0              CAPMOD  = 2              XPART   = 0.4
+CGDO    = 3.11E-10       CGSO    = 3.11E-10       CGBO    = 1E-12
+CJ      = 1.741905E-3    PB      = 0.9876681      MJ      = 0.4679558
+CJSW    = 3.653429E-10   PBSW    = 0.99           MJSW    = 0.2943558
+CF      = 0              PVTH0   = -0.01          PRDSW   = 0
+PK2     = 2.589681E-3    WKETA   = -1.866069E-3   LKETA   = -0.0166961      )
*
.MODEL pmos  PMOS (                                LEVEL   = 49
+VERSION = 3.1            TNOM    = 27             TOX     = 5.8E-9
+XJ      = 1E-7           NCH     = 4.1589E17      VTH0    = -0.583228
+K1      = 0.5999865      K2      = 6.150203E-3    K3      = 0
+K3B     = 3.6314079      W0      = 1E-6           NLX     = 1E-9
+DVT0W   = 0              DVT1W   = 0              DVT2W   = 0
+DVT0    = 2.8749516      DVT1    = 0.7488605      DVT2    = -0.0917408
+U0      = 136.076212     UA      = 2.023988E-9    UB      = 1E-21
+UC      = -9.26638E-11   VSAT    = 2E5            A0      = 0.951197
+AGS     = 0.20963        B0      = 1.345599E-6    B1      = 5E-6
+KETA    = 0.0114727      A1      = 3.851541E-4    A2      = 0.614676
+RDSW    = 1.496983E3     PRWG    = -0.0440632     PRWB    = -0.2945454
+WR      = 1              WINT    = 7.879211E-9    LINT    = 2.894523E-8
+DWG     = -1.112097E-8
+DWB     = 9.815716E-9    VOFF    = -0.1204623     NFACTOR = 1.2259401
+CIT     = 0              CDSC    = 2.4E-4         CDSCD   = 0
+CDSCB   = 0              ETA0    = 0.3325261      ETAB    = -0.0623452
+DSUB    = 0.9206875      PCLM    = 0.833903       PDIBLC1 = 9.948506E-4
+PDIBLC2 = 0.0191187      PDIBLCB = -1E-3          DROUT   = 0.9938581
+PSCBE1  = 2.887413E10    PSCBE2  = 8.325891E-9    PVAG    = 0.8478443
+DELTA   = 0.01           RSH     = 3.6            MOBMOD  = 1
+PRT     = 0              UTE     = -1.5           KT1     = -0.11
+KT1L    = 0              KT2     = 0.022          UA1     = 4.31E-9
+UB1     = -7.61E-18      UC1     = -5.6E-11       AT      = 3.3E4
+WL      = 0              WLN     = 1              WW      = 0
+WWN     = 1              WWL     = 0              LL      = 0
+LLN     = 1              LW      = 0              LWN     = 1
+LWL     = 0              CAPMOD  = 2              XPART   = 0.4
+CGDO    = 2.68E-10       CGSO    = 2.68E-10       CGBO    = 1E-12
+CJ      = 1.864957E-3    PB      = 0.976468       MJ      = 0.4614408
+CJSW    = 3.118281E-10   PBSW    = 0.6870843      MJSW    = 0.3021929
+CF      = 0              PVTH0   = 6.397941E-3    PRDSW   = 30.410214
+PK2     = 2.100359E-3    WKETA   = 5.428923E-3    LKETA   = -0.0111599      )
*
.endl
```

![1ngspice](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/7beb4d5c-48ab-428d-a04a-0dfa258192b2)


to run it open ngspice and in command give :  

```
source cmos.cir
```

![2ngspice](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/328c0c48-2b89-46b3-8073-b10ff8f2b56d)


To execute it : 

`
run
`
Then using `setplot` you can set the dc plot.  
Also you can see by usign `display`  

Now using `plot out vs in` Below graph will be seen:  

![plot](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/a7d70792-0e1b-4832-97ec-3ac5af4fd2b6)

Now if we increase the width of pmos which is `0.9375` below graph is seen and you can compare it with the above graph :  

![shiftplot](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/8772dcb3-772b-4da2-bebe-c1cb717cbb7d)

Below shown switching threshold representation where `Wp/Lp and xWn/Ln` relation and calculation shown: 

![4switchthreshold](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/be436190-b7b5-49d1-8cb7-98dd18e6324d)


Below is the modified cmos file:  

![image](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/271af951-0287-4ea5-83b3-1b700bace805)

Here is the graph of `out vs time` 

![ckt2OutvsTime](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/d17a1a4e-8c0d-49a1-a6dd-9d5ec2876083)

Now here is the graph of `out vs time in`

![ckt2OutvsTimeIn](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/b63e5e15-85bc-4570-8ab7-0931995cf250)

To find the difference between two graph points just drag with mouse and it will zoom and then you can just click on the graph.  
It will show the points in ngspice terminal:  

![2diffckt2OutvsTimeIn](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/176fb2ef-fb76-4d81-9687-84075023721e)

![diffckt2OutvsTimeIn](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/85cf86c9-defc-494e-a6a8-80c77c60aa77)

</details>

<details>
<summary>CMOS Fabrication Process </summary> 


The 16-mask CMOS fabrication process is explained below :  


1. **Substrate Selection**: Selecting a substrate is a important step in semiconductor manufacturing and integrated circuit (IC) fabrication. The substrate is the foundational material upon which the entire IC will be built. Typically, silicon (Si) is the most commonly used substrate material for most modern ICs due to its favorable properties, but other materials like gallium arsenide (GaAs) or silicon carbide (SiC) are also used in specific applications.
2. **Creating active region for transistors**: Creating active regions for transistors involves isolating specific areas on a silicon substrate where transistors will be located. This isolation is achieved by depositing layers of silicon dioxide (SiO2) and silicon nitride (Si3N4) on the substrate. Then, using photolithography and etching techniques, patterns are defined on the silicon nitride layer. These patterns determine where the active regions for transistors will be. The remaining SiO2 and exposed silicon regions become the isolated pockets where transistor components will be fabricated. This isolation is crucial to prevent unwanted electrical interactions between transistors and ensure their proper functioning.
3. **N-well and P-well formation**: The formation of N-well and P-well regions in CMOS technology involves ion implantation using specific dopants. Boron is utilized for P-well formation, while Phosphorus is employed for N-well creation. These dopants are implanted into the silicon substrate to define the N-well and P-well regions, which are essential components for building complementary NMOS and PMOS transistors, respectively.

4. **Formation of gate terminal**: The gate terminals for NMOS (N-channel Metal-Oxide-Semiconductor) and PMOS (P-channel Metal-Oxide-Semiconductor) transistors are created through photolithography techniques. In this process, precise patterns are defined on the semiconductor substrate using masks and light exposure. These patterns correspond to the gate electrodes of the transistors, and they play a fundamental role in controlling the transistor's conductivity and operation. By carefully implementing photolithography, the gate terminals for both NMOS and PMOS transistors are formed with high precision, enabling the subsequent steps in transistor fabrication.  
5. **LDD (lightly doped drain) formation:** In the LDD process, additional ion implantation steps are introduced after the formation of the main source and drain regions of the transistor. The key idea is to create lightly doped regions adjacent to the main source and drain regions. These lightly doped regions serve as a buffer between the channel and the heavily doped source and drain regions. The purpose of the LDD regions is to reduce the strength of the electric field near the drain, particularly in the region where the channel meets the drain. This helps to prevent the acceleration of electrons to high energies, which can lead to the hot electron effect. The hot electron effect can cause damage to the gate oxide and result in long-term reliability issues for the transistor.
6. **Source & drain formation:** The formation of the source and drain regions in a semiconductor device is a critical step in the fabrication process. To ensure proper performance and avoid issues like channeling during ion implantation, several techniques are employed, including the use of a screen oxide layer, arsenic implantation, and annealing.  **Screen Oxide**: Before performing the source and drain ion implantation, a thin layer of screen oxide is deposited or grown on the semiconductor wafer's surface. The screen oxide serves as a protective layer during the implantation process. It helps to disperse and slow down the implanted ions, reducing the likelihood of channeling.  **Arsenic Implantation**: Arsenic (As) ions are implanted into the regions of the silicon substrate where the source and drain are to be formed. Arsenic is a common dopant used for N-type (electron-conducting) regions in CMOS technology. The implantation process introduces a controlled amount of arsenic atoms into the silicon lattice, creating N-type doping in the source and drain regions.  **Annealing**: After the arsenic implantation, the wafer is subjected to an annealing process. Annealing involves heating the wafer to high temperatures for a specified duration. During annealing, the implanted arsenic ions are activated, and any damage to the silicon crystal lattice caused by the implantation process is repaired. Annealing helps to ensure that the source and drain regions have the desired electrical properties.
7. **Local interconnect formation:** Local interconnect formation is a important step in semiconductor device fabrication, enabling the creation of electrical connections between different components on a chip.  **Screen Oxide Removal (HF Etching):** After various processing steps, including source and drain formation, a screen oxide layer is typically deposited or grown on the semiconductor wafer's surface. This screen oxide layer serves as a protective barrier during ion implantation. However, it needs to be removed to allow for the formation of local interconnects.
**HF Etching:** Hydrofluoric acid (HF) is commonly used to selectively etch away the screen oxide. HF is highly effective at removing silicon dioxide (SiO2) while leaving other materials like silicon (Si) and metal layers unaffected.  **Deposition of Ti (Titanium):** Once the screen oxide is removed, the next step involves depositing a layer of titanium (Ti) onto the wafer's surface. Titanium is chosen for its excellent adhesion properties and low electrical resistance.
**Low-Resistance Contacts:** Titanium serves as the base layer for creating low-resistance electrical contacts or interconnects. It acts as an adhesion layer for subsequent metal layers (typically aluminum or copper) that will be deposited to form the actual interconnects.
8.**Higher level metal formation:** The higher-level metal formation in semiconductor device fabrication involves creating additional layers of metal interconnects to connect various components and ensure proper functionality.  **Chemical-Mechanical Polishing** (CMP) for Planarization: After the initial layers of metal interconnects and insulating layers have been deposited and patterned, the surface of the wafer can become uneven due to the topography of the underlying structures. To ensure a flat and planar surface, CMP is employed.
**CMP**: Chemical-Mechanical Polishing is a process that uses a combination of chemical etching and mechanical abrasion to remove excess material and achieve a smooth, flat surface. It is important for ensuring uniform layer thickness in subsequent metal layers.  **Top SiN (Silicon Nitride) Layer for Chip Protection:** To protect the completed chip from environmental factors, moisture, and physical damage, a top layer of silicon nitride (SiN) is deposited. Silicon nitride is an excellent insulator and provides a robust protective barrier.
**Chip Protection:** This top SiN layer acts as a passivation layer, shielding the underlying components from external influences. It also helps prevent contamination and ensures the long-term reliability of the integrated circuit.

Below is the final representation:  

![image](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/719e565b-fa42-4d56-b82f-8550730bd940)



### VSDSTDCelldesign Lab 

First git clone the vsdstdcelldesign repository using below command :  

``
git clone https://github.com/nickson-jose/vsdstdcelldesign
``  

There will be `sky130_inv.mag` file 

Use the sky130A.tech file from the `libs` folder when you are running the magic.

To run the layout design in magic use the below command :  

```
magic -T sky130A.tech sky130_inv.mag &
```

![1](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/faddcf47-e351-49f6-a9f4-2f3536f30cde)

Now if you click on any component and type `what` in terminal it will tell about the component:  

![2whatnmos](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/16f547de-44bd-45dc-9e11-b2fb1f0e7f9a)

Here shown the output terminal :  

![3whaty](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/a897de8c-e624-4887-ba49-70a71affac5f)

Now to understand this stick diagram below is representation where pdiff, ndiff, poly etc are shown:  

![4cmosinfo](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/571d6378-d4b8-4fd4-b393-fc039331f4ed)

**Pdiff (p-diffusion)**: Pdiff represents the P-diffusion layer in a CMOS process. This layer is used to create the N-channel MOSFETs (NMOS) in the CMOS technology. It typically represents regions where the silicon substrate is doped with a P-type dopant, creating the source and drain regions of NMOS transistors.  

**Ndiff (n-diffusion**): Ndiff represents the N-diffusion layer in a CMOS process. This layer is used to create the P-channel MOSFETs (PMOS) in the CMOS technology. It typically represents regions where the silicon substrate is doped with an N-type dopant, creating the source and drain regions of PMOS transistors.  

**Poly (polysilicon)**: The Poly layer represents the polysilicon layer, which is used to form the gates of both NMOS and PMOS transistors. The gates control the flow of current between the source and drain regions in these transistors.  

**Pdcontact (p-diffusion contact)**: Pdcontact represents the contact points or vias used to make electrical connections to the P-diffusion layer. These contacts are used to connect metal layers to the P-diffusion regions.  

**Ndcontact (n-diffusion contact**): Ndcontact represents the contact points or vias used to make electrical connections to the N-diffusion layer. These contacts are used to connect metal layers to the N-diffusion regions.

**extthresh**: Using this command it is used to extract parasitic capacitance  

Now to extract it to spice use below command in magic terminal :  

``
ext2spice
``

It will create `sky130_inv.spice` file : 

![5spicefile](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/ab97542a-7543-49e5-80ea-b624ccb6ad46)

Now include the `pshort.lib` and `nshort.lib` libraries and modified the models. 

Then pulse is given in which format is below:  

`PULSE(V1 V2 Tdelay Trise Tfall Ton Tperiod Ncycles)`  

 Below is the modified spice file:  

 ![6modifiedinv](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/7a3685dc-e894-4a35-b42c-8a6bc829298e)

Now run it in ngspice :  

![7ngspiceshow](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/4d16af1e-a825-4b66-b517-a73286f63e36)

Now give below command :  

```
plot y vs time a
```

It will give below graph :  

![8ngplot](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/78931dd9-56d1-448f-93c3-8f79e0d40cd9)


Now if zoom the graph to find rise time difference here is the representation:  

![9-zoomgraph](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/5b29d1b2-1021-4b40-bc00-aed62ba86e00)

And then click on the graph it will show :  

![10value](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/dc2c5cb1-8e72-4d6d-a9fd-009e75637b2f)
</details>

<details>
<summary>DRC</summary>

You can read Magic user guide from : `http://opencircuitdesign.com/magic/` 

![image](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/56354c85-f2f1-4dd8-a305-567ad241c664)

To use the lab contents below is the command:  

```
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
```

To extract the .tgz use below command:  

```
tar xfz drc_tests.tgz
```

Now in that extracted folder there are files like this :  

![drctest](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/a1f1c8b8-82ef-4a51-ae2d-5930579cd82c)

Now open the terminal to open the file give below commands:  

```
magic -d XR met3.mag
```
Below is the periphery rules for m3: (which can be found on `https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html#m3`)   

![image](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/7d65c0c6-8bef-450e-8420-810376d593e4)


Below is the representation:  

![2meg3](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/045954d6-a9e0-4f74-b9ba-6c2c578d37fc)

Then with the mouse select the box and type the command it will show error of overlap, width spacing etc:  

``
drc why
``

![3errordrcwhy](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/79d04b91-b800-4379-9a1e-49d8f0041671)

Now create a box and hover over the metal3 on the sidepar and press 'p' on the keyboard (p for paint) , You can also undo by pressing the 'u' key :  

![image](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/eabab38d-bc25-43cd-b952-42b4e04be58e)

You can also make a box on elements to see metalcuts.  

Then type below command (to see metalcuts) in magic terminal :  

``
cif see VIA2
``

![4cifsee](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/f58eb19b-73b5-4aae-b1cc-0e191f58326f)

### Fix poly.9 error in sky.tech file

To load the poly.mag :  

``
load poly.mag
``

It will open layout of the file in magic:  

![5polymag](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/1252e6bb-00c5-4e46-9893-4b63f7c457fe)

You can see what are the elements by selecting it and type `what` in the terminal.  

Now edit the sky.tech file. Open it in the text editor using below command:  

`gedit sky130A.tech`

Now search for the `poly.9`there will be 2-3 results here is one of them :  

```
spacing npres *nsd 480 touching_illegal \
	"poly.resistor spacing to N-tap < %d (poly.9)"
```

Modify it to this :  

```
spacing npres allpolynonres 480 touching_illegal \
	"poly.resistor spacing to N-tap < %d (poly.9)"
```

Now it should be like this :  

![image](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/29b5038f-2a70-432b-b15a-5dfff7f2daf5)

Now update the other one :  

```
spacing xhrpoly,uhrpoly,xpc alldiff 480 touching_illegal \

	"xhrpoly/uhrpoly resistor spacing to diffusion < %d (poly.9)"
```

Modify it to this:  

```
spacing xhrpoly,uhrpoly,xpc allpolynonres 480 touching_illegal \

	"xhrpoly/uhrpoly resistor spacing to diffusion < %d (poly.9)"
```
It should be look like this:   

![image](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/eed19458-b52a-4cac-8a58-cf9a0d2a92b6)

Now save it. You dont have to close the magic after the modifying the sky.tech file. Type the below command in magic's terminal:  

``
tech load sky130A.tech
``
In the warning click on yes.  
Then type below command:  

``
drc check
``

Below is the modified layout:  

![8copy](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/04d36d05-1814-4411-a29a-42454e82c433)

### Implement poly resistor spacing

Copy the three resistors by selecting (Edit > select area) and then press 'c' on keyboard.  
You can move the copied resistors by pressing 'm' key and arrow key.  

Now create n diffusion and p diffusion by creating box and selecting the `ndiff` and `pdiff` from the sidebar(by clicking the middle button of the mouse).  

Then modify the sky130A.tech file by just modifying the `*nsd` to `alldiff`.  
Now type below command in magic's terminal and all the rules will correctly identify :  

``
drc check
``

Below is the representation:  

![9npdiffusion](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/ca855626-edb1-464a-a4d8-e7ab3d302ac1)

### Challenge exercise to describe DRC error

Open sky130A.tech file and search `cifmaxwidth` below is the representation:  

![1](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/50f56848-6d90-4215-98f2-bfad07fc6f03)

Below is the rule shown for nwell (which can be access by skywater sky130 pdk website) :  

![3nwellrule](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/7d980717-2225-4bc5-8dcb-b2e3a7f0791b)

Also refer this dnwell rules:  

![2rule](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/407196cf-9a32-47fa-a29c-f0bc53812fb7)

Refer this style drc in sky130A.tech file :  

![4styledrc](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/daf3b70f-b1d3-44ab-9909-98a82ca63695)

First create a box around cell nwell6 , Now in tkcon terminal type below commands:  

![5nwell](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/b50367af-1061-496b-8e54-7687b3d035c7)

![6errornwell](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/6d8f0d4a-b97a-48c5-ae4b-3a39db94e552)


```
cif ostyle drc
cif see dnwell_shrink
feed clear
cif see nwell_missing
feed clear
```

Here shown the representations:  

![7shrink](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/b0292e43-8551-4a7b-8c70-36153cd1e5b9)


![8feedclear](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/1762a25e-92f7-44b3-80c5-2d1f70060d74)

![9final](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/4e0e24a9-a56a-49d3-92e5-6b7bfc6bd7b9)

### Lab challenge to find missing or incorrect rules (creating magic DRC rule)

Modify the sky130A.tech file according to below:  

change the cifmaxwidth code to this :  

```
cifmaxwidth nwell_untapped 0 bend_illegal \

	"Nwell missing tap (nwell.4)"
```

![ciffmax](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/cf237e7b-9017-4800-bc69-6de5204b55d9)

Then modify the nwell according to this :  

![nwellmodified](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/7a9dece1-35a2-4dcf-87eb-08d83daa8b39)

Below is the variant modification:  

![variants](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/05c855b0-7942-460b-b956-e8ae9f7f2a15)

After modification give commands shown below in terminal :  

``
tech load sky130A.tech
drc check
drc style drc(full)
drc check
``
See below there is no error for now :  

![4noerror](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/99f53718-f357-4351-9cea-6fdf37aec5b1)

![5drccheck](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/8b637453-83f2-42b5-a010-6282dbdb19d4)

Now tapp using nsubstratencontact shown below it will change DRC:  

![6final](https://github.com/Pruthvi-Parate/Advanced_Physical_Design_Using_OpenLANE/assets/72121158/760490cf-3bb4-4af9-b58f-3ea1288a77af)
</details>



## Day 4: Pre-Layout Timing Analysis

<details>
<summary> Standard Cell LEF generation </summary>

During Placement, entire mag information is not necessary. Only the PR boundary, I/O ports, Power and ground rails of the cell is required. This information is defined in LEF file.
The main objective is to extract lef from the mag file and plug into our design flow.

### Grid into Track info

 **Track** :A path or a line on which metal layers are drawn for routing. Track is used to define the height of the standard cell. 

To implement our own stdcell, few guidelines must be followed 
 - I/O ports must lie on the intersection on Horizontal and vertical tracks
 - Width and Height of standard cell are odd mutliples of Horizontal track pitch and Vertical track pitch

This information is defined in ``tracks.info``. 

```
li1 X 0.23 0.46 
li1 Y 0.17 0.34
```

To ensure that ports lie on the intersection point, the grid spacing in Magic (tkcon) must be changed to the li1 X and li1 Y values. After providing the command, we have following:

```
grid 0.46um 0.34um 0.23um 0.17um

```

### Create Port Definition: 

However, certain properties and definitions need to be set to the pins of the cell. For LEF files, a cell that contains ports is written as a macro cell, and the ports are the declared as PINs of the macro.

The way to define a port is through Magic console and following are the steps:
- In Magic Layout window, first source the .mag file for the design (here inverter). Then Edit >> Text which opens up a dialogue box.
- When you double press S at the I/O lables, the text automatically takes the string name and size. Ensure the Port enable checkbox is checked and default checkbox is unchecked as shown in the figure:

![Screenshot from 2023-09-11 00-40-05](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/cdaa8ce9-b9d9-4448-bff9-a3f0830492d0)

- In the above figure, The number in the textarea near enable checkbox defines the order in which the ports will be written in LEF file (0 being the first).

-  For power and ground layers, the definition could be same or different than the signal layer. Here, ground and power connectivity are taken from metal1

### Set port class and port use attributes for layout 

After defining ports, the next step is setting port class and port use attributes.

Select port A in magic:
```
port class input
port use signal
```
Select Y area
```
port class output
port use signal
```
Select VPWR area
```
port class inout
port use power
```
Select VGND area
```
port class inout
port use ground

```
## Custom cell naming and lef extraction.

Name the custom cell through tkcon window as ```sky130_vsdinv.mag```.

We generate lef file by command:

```
lef write

```
This generates sky130_vsdinv.lef file.

### Steps to include custom cell in ASIC design

We have created a custom standard cell in previous steps of an inverter. Copy lef file, sky130_fd_sc_hd_typical.lib, sky130_fd_sc_hd_slow.lib & sky130_fd_sc_hd_fast.lib to src folder of picorv32a from libs folder vsdstdcelldesign. Then modify the config.tcl as follows.

```

# Design
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) "$::env(DESIGN_DIR)/src/picorv32a.v"

set ::env(CLOCK_PORT) "clk"
set ::env(CLOCK_NET) $::env(CLOCK_PORT)

set ::env(GLB_RESIZER_TIMING_OPTIMIZATIONS) {1}

set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

set filename $::env(DESIGN_DIR)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1} {
	source $filename
}

```

To integrate standard cell in openlane flow after `` make mount `` , perform following commands:

```
prep -design picorv32a -tag RUN_2023.09.09_20.37.18 -overwrite 
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis

```
synthesis report :

![Screenshot from 2023-09-11 09-15-46](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/2b9b8757-17cc-41f5-b6a8-52a5285698f5)

sta report:

![Screenshot from 2023-09-11 09-14-38](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/e63e74da-2c40-47e7-90d0-0c17f61758e7)

![Screenshot from 2023-09-17 17-25-42](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/388132db-f7e7-4de7-8fbe-12b694260212)
![Screenshot from 2023-09-17 17-40-42](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/e815eaa2-144a-4039-8151-414589001fee)

</details>
<details>
<summary> Delay Tables</summary>

Basically, Delay is a parameter that has huge impact on our cells in the design. Delay decides each and every other factor in timing. 
For a cell with different size, threshold voltages, delay model table is created where we can it as timing table.
```Delay of a cell depends on input transition and out load```. 
Lets say two scenarios, 
we have long wire and the cell(X1) is sitting at the end of the wire : the delay of this cell will be different because of the bad transition that caused due to the resistance and capcitances on the long wire.
we have the same cell sitting at the end of the short wire: the delay of this will be different since the tarn is not that bad comapred to the earlier scenario.
Eventhough both are same cells, depending upon the input tran, the delay got chaned. Same goes with o/p load also.

VLSI engineers have identified specific constraints when inserting buffers to preserve signal integrity. They've noticed that each buffer level must maintain consistent sizing, but their delays can vary depending on the load they drive. To address this, they introduced the concept of "delay tables," which essentially consist of 2D arrays containing values for input slew and load capacitance, each associated with different buffer sizes. These tables serve as timing models for the design.

When the algorithm works with these delay tables, it utilizes the provided input slew and load capacitance values to compute the corresponding delay values for the buffers. In cases where the precise delay data is not readily available, the algorithm employs a technique of interpolation to determine the closest available data points and extrapolates from them to estimate the required delay values.

![Screenshot 2023-09-11 at 9 56 27 AM](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/7f23c990-215b-4fd3-a581-448967c046ae)

### Openlane steps with custom standard cell

We perform synthesis and found that it has positive slack and met timing constraints.

During Floorplan,``` 504 endcaps, 6731 tapcells ``` got placed. Design has 275 original rows

Now ``` run_placement```

After placement, we check for legality &To check the layout invoke magic from the results/placement directory:

```

magic -T /home/shubham/.volare/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read picorv32.def

```
![Screenshot from 2023-09-17 22-03-56](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/061a4bdd-1de2-41d9-a37e-ea8e7a75eeec)
![Screenshot from 2023-09-17 22-04-18](https://github.com/ShubhamGitHub528/ASIC/assets/140998623/459fc044-d4a1-4cb8-a015-6ef8fdaeea46)
![Screenshot from 2023-09-18 16-27-13](https://github.com/ShubhamGitHub528/Advanced-Physical-Design-Using-OpenLane-Sky130/assets/140998623/bfad157f-6834-4eaa-9fb3-bfd668f8dcab)
</details>
<details>
<summary> Post-synthesis timing analysis Using OpenSTA </summary>

Timing analysis is carried out outside the openLANE flow using OpenSTA tool. For this, ```pre_sta.conf``` is required to carry out the STA analysis. Invoke OpenSTA outside the openLANE flow as follows:
 
```
sta pre_sta.conf
```

sdc file for OpenSTA is modified like this:

base.sdc is located in vsdstdcelldesigns/extras directory.
So, I copied it into our design folder using

``` cp my_base.sdc /home/parallels/OpenLane/designs/picorv32a/src/ ```


Since I have no Violations I skipped this, but have hands on experience on timing analysis using OpenSTA.

Since clock is propagated only once we do CTS, In placement stage, clock is considered to be ideal. So only setup slack is taken into consideration before CTS.

``` 
Setup time: minimum time required for the data to be stable before the active edge of the clock to get properly captured.

Setup slack : data required time - data arrival time 

```
clock is generated from PLL which has inbuilt circuit which cells and some logic. There might variations in the clock generation depending upon the ckt. These variations are collectivity known as clock uncertainity. In that jitter is one of the parameter. It is uncertain that clock might come at that exact time withought any deviation. That is why it is called clock_uncertainity
Skew, Jitter and Margin comes into clock_uncertainity

```  Clock Jitter : deviation of clock edge from its original position. ```

From the timing report, we can improve slack by upsizing the cells i.e., by replacing the cells with high drive strength and we can see significant changes in the slack.


</details>
<details>
<summary>Clock Tree Synthesis using Tritoncts</summary>
	
Clock tree synthesis (CTS) can be implemented in various ways, and the choice of the specific technique depends on the design requirements, constraints, and goals. Here are some different types or approaches to clock tree synthesis:

Balanced Tree CTS:
In a balanced tree CTS, the clock signal is distributed in a balanced manner, often resembling a binary tree structure.
This approach aims to provide roughly equal path lengths to all clock sinks (flip-flops) to minimize clock skew.
It's relatively straightforward to implement and analyze but may not be the most power-efficient solution.

H-tree CTS:
An H-tree CTS uses a hierarchical tree structure, resembling the letter "H."
It is particularly effective for distributing clock signals across large chip areas.
The hierarchical structure can help reduce clock skew and optimize power consumption.

Star CTS:
In a star CTS, the clock signal is distributed from a single central point (like a star) to all the flip-flops.
This approach simplifies clock distribution and minimizes clock skew but may require a higher number of buffers near the source.

Global-Local CTS:
Global-Local CTS is a hybrid approach that combines elements of both star and tree topologies.
The global clock tree distributes the clock signal to major clock domains, while local trees within each domain further distribute the clock.
This approach balances between global and local optimization, addressing both chip-wide and domain-specific clocking requirements.

Mesh CTS:
In a mesh CTS, clock wires are arranged in a mesh-like grid pattern, and each flip-flop is connected to the nearest available clock wire.
It is often used in highly regular and structured designs, such as memory arrays.
Mesh CTS can offer a balance between simplicity and skew minimization.

Adaptive CTS:
Adaptive CTS techniques adjust the clock tree structure dynamically based on the timing and congestion constraints of the design.
This approach allows for greater flexibility and adaptability in meeting design goals but may be more complex to implement.

### crosstalk in VLSI:
Impact: Crosstalk is a significant concern in VLSI design due to the high integration density of components on a chip. Uncontrolled crosstalk can lead to data corruption, timing violations, and increased power consumption.
Mitigation: VLSI designers employ various techniques to mitigate crosstalk, such as optimizing layout and routing, using appropriate shielding, implementing proper clock distribution strategies, and utilizing clock gating to reduce dynamic power consumption when logic is idle

### Clock Net Shielding in VLSI:
Purpose: In VLSI circuits, the clock distribution network is crucial for synchronous operation. Clock signals must reach all parts of the chip while minimizing skew and maintaining signal integrity.
Shielding Techniques: VLSI designers may use shielding techniques to isolate the clock network from other signals, reducing the risk of interference. This can include dedicated clock routing layers, clock tree synthesis algorithms, and buffer insertion to manage clock distribution more effectively.
Clock Domain Isolation: VLSI designs often have multiple clock domains. Shielding and proper clock gating help ensure that clock signals do not propagate between domains, avoiding metastability issues and maintaining synchronization.

### lab:

In this stage clock is propagated and make sure that clock reaches each and every clock pin from clock source with mininimum skew and insertion delay. Inorder to do this, we implement H-tree using mid point strategy. For balancing the skews, we use clock invteres or bufferes in the clock path. 
Before attempting to run CTS in TritonCTS tool, if the slack was attempted to be reduced in previous run, the netlist may have gotten modified by cell replacement techniques. Therefore, the verilog file needs to be modified using the ```write_verilog``` command. Then, the synthesis, floorplan and placement is run again. To run CTS use the below command:

```
run_cts
```
After CTS run, my slack values are
``` setup:12.97,Hold:0.23 ```

here my both values are not voilating 

Since, clock is propagated, from this stage, we do timing analysis with real clocks. From now post cts analysis is performed by operoad within the openlane flow 

```
openroad
read_lef <path of merge.nom.lef>
read_def <path of def>
write_db pico_cts.db
read_db pico_cts.db
read_verilog /home/parallels/OpenLane/designs/picorv32a/runs/RUN_09-09_11-20/results/synthesis/picorv32a.v
link_design picorv32a
read_liberty $::env(LIB_SYNTH_COMPLETE)
read_sdc /home/parallels/OpenLane/designs/picorv32a/src/my_base.sdc
set_propagated_clock (all_clocks)
report_checks -path_delay min_max -format full_clock_expanded -digits 4
``` 

Hold slack:

![Screenshot from 2023-09-11 14-59-57](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/7a0875ab-5d58-491c-a026-846e327d5abf)


setup slack:

![Screenshot from 2023-09-11 14-59-04](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/59a9cdcd-3176-4cef-852d-8359d3f4b3d2)

### test:
type this in openlane
```
echo $::env(CTS_CLK_BUFFER_LIST)
set $::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0]
echo $::env(CTS_CLK_BUFFER_LIST)
```
After changing the files, load the placement stage def file and run cts again. 
Now, again run OpenROAD and create another db and everything else is same.
Report after post_cts is

``` Setup slack - 2.2379 , Hold slack - 0.1869 ```

</details>

## Day5: Final steps for RTL2GDS using tritonRoute and OpenSTA.

</details>
<details>
<summary> Routing and Design Rule Check </summary>

__Maze Routing__

Routing is the process of creating physical connections based on logical connectivity. Signal pins are connected by routing metal interconnects. Routed metal paths must meet timing, clock skew, max trans/cap requirements and also physical DRC requirements.

In grid based routing system each metal layer has its own tracks and preferred routing direction which are defined in a unified cell in the standard cell library.

There are four steps of routing operations:

1. Global routing
2. Track assignment
3. Detail routing
4. Search and repair

The Maze Routing algorithm, such as the Lee algorithm, is one approach for solving routing problems. In this method, a grid similar to the one created during cell customization is utilized for routing purposes. The Lee algorithm starts with two designated points, the source and target, and leverages the routing grid to identify the shortest or optimal route between them.
The algorithm assigns labels to neighboring grid cells around the source, incrementing them from 1 until it reaches the target (for instance, from 1 to 7). Various paths may emerge during this process, including L-shaped and zigzag-shaped routes. The Lee algorithm prioritizes selecting the best path, typically favoring L-shaped routes over zigzags. If no L-shaped paths are available, it may resort to zigzag routes. This approach is particularly valuable for global routing tasks.
However, the Lee algorithm has limitations. It essentially constructs a maze and then numbers its cells from the source to the target. While effective for routing between two pins, it can be time-consuming when dealing with millions of pins. There are alternative algorithms that address similar routing challenges.

![image](https://github.com/Sushma-Ravindra/Advanced_Physical_Design_using-_OpenLane/assets/141133883/160f5d7c-9ed1-4252-a548-4670a0cb9e6f)



__DRC__

Design Rule Checking (DRC) verifies as to whether a specific design meets the constraints imposed by the process technology to be used for its manufacturing. DRC checking is an essential part of the physical design flow and ensures the design meets manufacturing requirements and will not result in a chip failure. The process technology rules are provided by process engineers and/or fabrication facility.Each process technology will have its own set of rules. The number of DRC rules and complexity of rules increases as the manufacturing technology shrinks at advanced nodes
DRC verifies whether a design meets the predefined process technology rules given by the foundry for its manufacturing. DRC checking is an essential part of the physical design flow and ensures the design meets manufacturing requirements and will not result in a chip failure. It defines the Quality of chip. They are so many DRCs, let us see few of them
Design rules for physical wires
Minimum width of the wire Minimum spacing between the wires Minimum pitch of the wire To solve signal short violation, we take the metal layer and put it on to upper metal layer. we check via rules Via width via spacing



</details>
<details>
<summary> Power Distribution Network and Summary</summary>

__Build Power Distribution Network__

PDN must be generated after CTS and post-CTS STA analyses.

```
gen_pdn

```

__Standard cell power__

The power distribution network has to take the design_cts.def as the input def file.
Power rings,strapes and rails are created by PDN.
From VDD and VSS pads, power is drawn to power rings.
Next, the horizontal and vertical strapes connected to rings draw the power from strapes.
Stapes are connected to rings and these rings are connected to std cells. So, standard cells get power from rails.
The standard cells are designed such that it's height is multiples of the vertical tracks /track pitch.Here, the pitch is 2.72. Only if the above conditions are adhered it is possible to power the standard cells.
There are definitions for the straps and the rails. In this design, straps are at metal layer 4 and 5 and the standard cell rails are at the metal layer 1. Vias connect accross the layers as required.

![image](https://github.com/Sushma-Ravindra/Advanced_Physical_Design_using-_OpenLane/assets/141133883/f011c3f7-7839-4ad6-b14f-ae7655034934)


__Routing__
The Detailed Routing (drt) module in OpenROAD is based on the open-source detailed router, TritonRoute. TritonRoute consists of several main building blocks, including pin access analysis, track assignment, initial detailed routing, search and repair, and a DRC engine. The initial development of the router is inspired by the ISPD-2018 initial detailed routing contest. However, the current framework differs and is built from scratch, aiming for an industrial-oriented scalable and flexible flow.
TritonRoute provides industry-standard LEF/DEF interface with support of ISPD-2018 and ISPD-2019 contest-compatible route guide format.
Global routing is basically an estimation of routes required. Detail routing is the actual wire routing that happens and that can be manufactured. Global routing will talk in terms of Number of Routing Resources available and Number of routing resources required. It will split the entire floorplan into equally sized logical elements known as Buckets. Then, it will try to find out how many resources available on each metal level and how many required ( based on No. of pins within the bucket and within its close vicinity). Then calculate a ratio to identify whether there is overflow. If there is overflow for many buckets within a particular area, it determines that the particular area is congested.

Detail routing is actually the proper wire routing. The tool creates the wire and explicitely connects it with pins associated to each other. If the tool cannot route avoiding DRCs/ density of routes in pne particular area is very high , then the terms that the particular area as congested. 

![image](https://github.com/Sushma-Ravindra/Advanced_Physical_Design_using-_OpenLane/assets/141133883/53489ccd-c24b-4409-b421-e9993e5c85ac)


__Layout in MAGIC post routing__



![image](https://github.com/Sushma-Ravindra/Advanced_Physical_Design_using-_OpenLane/assets/141133883/cd165881-a09e-4afb-898b-6c0fe02f771b)



![image](https://github.com/Sushma-Ravindra/Advanced_Physical_Design_using-_OpenLane/assets/141133883/472dfcee-6b81-4283-95e7-632d8bdc7952)

</details>
<details>
<summary>Triton Route Features</summary>

__Preprocessed route guides__

Performs initial detailed route. Honours preprocessed route guides (obtained after global/fast route).
Adherence to Pre-Processed Route Guides: TritonRoute places significant emphasis on following pre-processed route guides. This involves several actions:
Initial Route Guide Analysis: TritonRoute analyzes the directions specified in the preferred route guides. If any non-directional routing guides are identified, it breaks them down into unit widths. Guide Splitting: In cases where non-directional routing guides are encountered, TritonRoute divides them into unit widths to facilitate routing. Guide Merging: TritonRoute merges guides that are orthogonal (touching guides) to the preferred guides, streamlining the routing process. Guide Bridging: When it encounters guides that run parallel to the preferred routing guides, TritonRoute employs an additional layer to bridge them, ensuring efficient routing within the preprocessed guides.
Route guides are followed to satisfy inter- guide connectivity.
Requirements of preprocessed route guides: Must have unit width and must be in the predefined direction.
Directions of metal ensures minimum capacitances.

![image](https://github.com/Sushma-Ravindra/Advanced_Physical_Design_using-_OpenLane/assets/141133883/7e426872-d971-49c7-ae39-3c01b93ed3b7)



__Inter guide connectivity and intra-inter layer routing__

Two guides are connected if 
They are on the same metal layer with touching edges or they are on neighbouring metal layers with a non zero vertically overlapped area.

Each unconnected terminal should have its pin shape overlapped by a route guide.



![image](https://github.com/Sushma-Ravindra/Advanced_Physical_Design_using-_OpenLane/assets/141133883/6bfec130-00ad-4b11-bb56-74d984405445)



__Method to Handle Connectivity__

The inputs to triton detailed route are lef file, def file, preprocessed route guides.
THe outputs are detailed routing solutions with optimized wire length and via coun.
Constraint files: Route guide honoring, connectivity constraints and design  rules.


Access Point: An on grid metal poiny on the route guide, used to connect to lower layer segments, upperlayer pins or io ports.

Access Point Cluster: A collection of all access points derived from lower layer segments upper layer guide a pin or an io port.

![image](https://github.com/Sushma-Ravindra/Advanced_Physical_Design_using-_OpenLane/assets/141133883/194a64ca-d273-4ea1-9ea1-10280b0518f8)


__Topology Algorithm__

First, the cost has to be estimated and then the minimal and most optimal point between 2 APCs.

![image](https://github.com/Sushma-Ravindra/Advanced_Physical_Design_using-_OpenLane/assets/141133883/f343682f-078a-4a36-88fe-e4854d400f4c)

 
</details>

## Word of Thanks
I sciencerly thank **Mr. Kunal Ghosh**(Founder/**VSD**) for helping me out to complete this flow smoothly.

## Acknowledgement
- Kunal Ghosh, VSD Corp. Pvt. Ltd.
- Chatgpt
- Devipriya
- Alwin,Colleague,IIIT B
- Pruthvi Parate,Colleague,IIIT B
- Sushma,Colleague,IIIT B
  
## Reference 
- https://www.vsdiat.com
- https://www.vsdiat.com/course_content?uniqueid=20220802001034











