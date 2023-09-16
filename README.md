# Advanced-Physical-Design-Using-OpenLane-Sky130


This project is done in the course ["Advanced Physical Design using OpenLANE/Sky130"](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/) by VLSI System Design Corporation. In this project a complete RTL to GDSII flow for PicoRV32a SoC is executed with Openlane using Skywater130nm PDK. Custom designed standard cells with Sky130 PDK are also used in the flow. Timing Optimisations are carried out. Slack violations are removed. DRC is verified.


## Table of Contents
1. [Overview](#overview)
2. [Inception of Opensource EDA](#inception-of-opensource-eda)
   - [How to talk to computers](howto-talk-to-computers)
   - [SoC Design & OpenLANE](SoC Design-&-OpenLANE)
     + [OpenLANE ASIC Flow](OpenLANE-ASIC-Flow)






## Overview
OpenLANE is an opensource tool or flow used for opensource tape-outs. The OpenLANE flow comprises a variety of tools such as Yosys, ABC, OpenSTA, Fault, OpenROAD app, Netgen and Magic which are used to harden chips and macros, i.e. generate final GDSII from the design RTL. The primary goal of OpenLANE is to produce clean GDSII with no human intervention. OpenLANE has been tuned to function for the Google-Skywater130 Opensource Process Design Kit.

## Inception of Opensource EDA

### How to talk to computers?

The RISC-V Instruction Set Architecture (ISA) is a language used to talk to computers whose hardware is based on RISC-V core. If a user wishes to run a certain application software on a computer, its corresponding C/C++/Java program must be converted into instructions by the compliler. The ouput of the compiler is hardware dependent. These instructions go as inputs to the assembler which outputs binary language that the hardware logic in the chip layout can make sense of. According to the bits received, the digital logic consisting of gates performs the function required by the user of the application software.

### SoC Design & OpenLANE

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

### Opensource EDA tools

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

## Floorplanning & Placement and library cells

### Floorplanning considerations

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


















## Day2:

Chip floor planning and consideration.
1) Define Width and height of Core and Die:
Netlist : Defines connectivity between all the components.

2) Define Location of Preplaced cells:
   eg. Memory, Clock-gating cell, Comparator, Mux.

3) De-Coupling Capacitors:
   Whenever their is switching DeCoupling capacitor will supply charge. hence, voltage remains constant.

#Fig2

4) Power Planning.


5) Pin Placement.
   









To see layout after floorplan use the command:
```
OpenLane/designs/picorv32a/runs/RUN_2023.09.11_08.17.54/results/floorplan$ magic -T /home/shubham/.volare/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read picorv32.def &
```
magi -T (Tech file location) lef read (merged file which is two level up) def read (def file) & (to free RAM)



