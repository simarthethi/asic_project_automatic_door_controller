# asic_project_automatic door controller
This github repository summarizes the progress made in the ASIC class for the riscv_project.

# Aim of the project
The aim of the prohect is to design a gate controller that detects when someone is in the proximity and automatically opens the door

# Working
A transmitter and an ultrasonic sensor is employed at the bottom of the stick to dtect the obstacle/pothole. Upon activating the transmitter an unltrasonic wave will transmit. Upon detection, the buzzer would go on indicating that there is something nearby.

# Circuit Diagram
The following is not the final circuit but just for reference

![Screenshot from 2023-10-05 10-29-33](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/4e51e417-326f-4e24-a5a6-34b6b9c89fc9)



# Block Diagram

![Screenshot from 2023-10-05 10-16-26](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/759cf073-f28c-4b43-9b0b-e4b9b9d3b657)

# C code
-code to be used for generating RTL processor
```bash

int main()
{

	int m;	
	int p1, p2;
	
	int d1 = 0;
	int d2 = 0;
	
	int d1_reg;
	int d2_reg;
	
	int mask = 0xFFFFFFE3; 
	
	d1_reg = d1*8;
	d2_reg = d2*16;
	
	asm volatile(
	"and x30, x30, %2\n\t"
    	"or x30, x30, %0\n\t"  
    	"or x30, x30, %1\n\t"  
    	:
    	: "r" (d1_reg), "r" (d2_reg), "r"(mask)
	: "x30"
	);
	
	while(1)
	{
		
		asm volatile(
		"andi %0, x30, 0x01\n\t"
		: "=r" (m)
		:
		:);
		
	
		asm volatile(
		"andi %0, x30, 0x02\n\t"
		: "=r" (p1)
		:
		:);
		
		
		asm volatile(
		"andi %0, x30, 0x04\n\t"
		: "=r" (p2)
		:
		:);
		
		
		if(m) //entry
		{
			if(p1 && p2)
			{
				d1=1;
				d2=1;
			}
			else if(p1 && (!p2))
			{	
				d1=0;
				d2=0;
			}
			else if((!p1)&&p2)
			{
				d1=1;
				d2=1;
			}
			else
			{
				d1=0;
				d2=0;
			}
			
		}
		else //exit
		{
			if(p1 && p2)
			{
				d1=1;
				d2=1;
			}
			else if(p1 && (!p2))
			{	
				d1=1;
				d2=1;
			}
			else if((!p1)&&p2)
			{
				d1=0;
				d2=0;
			}
			else
			{
				d1=0;
				d2=0;
			}
			
		}
			
		d1_reg = d1*8;
		d2_reg = d2*16;
	
		asm volatile(
		"and x30, x30, %2\n\t"
    		"or x30, x30, %0\n\t"  
    		"or x30, x30, %1\n\t"  
    		:
    		: "r" (d1_reg), "r" (d2_reg), "r"(mask)
		: "x30"
		);
			
		
	}
	
return 0;
}

```
# Assembly Code


```bash


out:     file format elf32-littleriscv


Disassembly of section .text:

00010054 <main>:
   10054:	fd010113          	addi	sp,sp,-48
   10058:	02812623          	sw	s0,44(sp)
   1005c:	03010413          	addi	s0,sp,48
   10060:	fe042623          	sw	zero,-20(s0)
   10064:	fe042423          	sw	zero,-24(s0)
   10068:	fe300793          	li	a5,-29
   1006c:	fef42223          	sw	a5,-28(s0)
   10070:	fec42783          	lw	a5,-20(s0)
   10074:	00379793          	slli	a5,a5,0x3
   10078:	fef42023          	sw	a5,-32(s0)
   1007c:	fe842783          	lw	a5,-24(s0)
   10080:	00479793          	slli	a5,a5,0x4
   10084:	fcf42e23          	sw	a5,-36(s0)
   10088:	fe042783          	lw	a5,-32(s0)
   1008c:	fdc42703          	lw	a4,-36(s0)
   10090:	fe442683          	lw	a3,-28(s0)
   10094:	00df7f33          	and	t5,t5,a3
   10098:	00ff6f33          	or	t5,t5,a5
   1009c:	00ef6f33          	or	t5,t5,a4
   100a0:	001f7793          	andi	a5,t5,1
   100a4:	fcf42c23          	sw	a5,-40(s0)
   100a8:	002f7793          	andi	a5,t5,2
   100ac:	fcf42a23          	sw	a5,-44(s0)
   100b0:	004f7793          	andi	a5,t5,4
   100b4:	fcf42823          	sw	a5,-48(s0)
   100b8:	fd842783          	lw	a5,-40(s0)
   100bc:	06078a63          	beqz	a5,10130 <main+0xdc>
   100c0:	fd442783          	lw	a5,-44(s0)
   100c4:	02078063          	beqz	a5,100e4 <main+0x90>
   100c8:	fd042783          	lw	a5,-48(s0)
   100cc:	00078c63          	beqz	a5,100e4 <main+0x90>
   100d0:	00100793          	li	a5,1
   100d4:	fef42623          	sw	a5,-20(s0)
   100d8:	00100793          	li	a5,1
   100dc:	fef42423          	sw	a5,-24(s0)
   100e0:	0bc0006f          	j	1019c <main+0x148>
   100e4:	fd442783          	lw	a5,-44(s0)
   100e8:	00078c63          	beqz	a5,10100 <main+0xac>
   100ec:	fd042783          	lw	a5,-48(s0)
   100f0:	00079863          	bnez	a5,10100 <main+0xac>
   100f4:	fe042623          	sw	zero,-20(s0)
   100f8:	fe042423          	sw	zero,-24(s0)
   100fc:	0a00006f          	j	1019c <main+0x148>
   10100:	fd442783          	lw	a5,-44(s0)
   10104:	02079063          	bnez	a5,10124 <main+0xd0>
   10108:	fd042783          	lw	a5,-48(s0)
   1010c:	00078c63          	beqz	a5,10124 <main+0xd0>
   10110:	00100793          	li	a5,1
   10114:	fef42623          	sw	a5,-20(s0)
   10118:	00100793          	li	a5,1
   1011c:	fef42423          	sw	a5,-24(s0)
   10120:	07c0006f          	j	1019c <main+0x148>
   10124:	fe042623          	sw	zero,-20(s0)
   10128:	fe042423          	sw	zero,-24(s0)
   1012c:	0700006f          	j	1019c <main+0x148>
   10130:	fd442783          	lw	a5,-44(s0)
   10134:	02078063          	beqz	a5,10154 <main+0x100>
   10138:	fd042783          	lw	a5,-48(s0)
   1013c:	00078c63          	beqz	a5,10154 <main+0x100>
   10140:	00100793          	li	a5,1
   10144:	fef42623          	sw	a5,-20(s0)
   10148:	00100793          	li	a5,1
   1014c:	fef42423          	sw	a5,-24(s0)
   10150:	04c0006f          	j	1019c <main+0x148>
   10154:	fd442783          	lw	a5,-44(s0)
   10158:	02078063          	beqz	a5,10178 <main+0x124>
   1015c:	fd042783          	lw	a5,-48(s0)
   10160:	00079c63          	bnez	a5,10178 <main+0x124>
   10164:	00100793          	li	a5,1
   10168:	fef42623          	sw	a5,-20(s0)
   1016c:	00100793          	li	a5,1
   10170:	fef42423          	sw	a5,-24(s0)
   10174:	0280006f          	j	1019c <main+0x148>
   10178:	fd442783          	lw	a5,-44(s0)
   1017c:	00079c63          	bnez	a5,10194 <main+0x140>
   10180:	fd042783          	lw	a5,-48(s0)
   10184:	00078863          	beqz	a5,10194 <main+0x140>
   10188:	fe042623          	sw	zero,-20(s0)
   1018c:	fe042423          	sw	zero,-24(s0)
   10190:	00c0006f          	j	1019c <main+0x148>
   10194:	fe042623          	sw	zero,-20(s0)
   10198:	fe042423          	sw	zero,-24(s0)
   1019c:	fec42783          	lw	a5,-20(s0)
   101a0:	00379793          	slli	a5,a5,0x3
   101a4:	fef42023          	sw	a5,-32(s0)
   101a8:	fe842783          	lw	a5,-24(s0)
   101ac:	00479793          	slli	a5,a5,0x4
   101b0:	fcf42e23          	sw	a5,-36(s0)
   101b4:	fe042783          	lw	a5,-32(s0)
   101b8:	fdc42703          	lw	a4,-36(s0)
   101bc:	fe442683          	lw	a3,-28(s0)
   101c0:	00df7f33          	and	t5,t5,a3
   101c4:	00ff6f33          	or	t5,t5,a5
   101c8:	00ef6f33          	or	t5,t5,a4
   101cc:	ed5ff06f          	j	100a0 <main+0x4c>

```

# Unique Instructions
```bash
Number of different instructions: 11
List of unique instructions:
andi
slli
beqz
addi
bnez
li
and
j
or
lw
sw




```
# Spike Simulation


- After this, we define the inputs and outputs using asm to link the assemply level inputs and outputs and store them over variables in C.

- Now, we spike the code to again check the functionality of the code. The test code can be found above as spike_tester.c. We have taken 4 testcases, 3 for the doors to open and one for door remains closed.
- eg: For m=1 and {p1,p2} = (0,1) the door is opened i.e {d1.d2}=(1,1)
      For m=1 and {p1,p2} = (1,1) the door is opened i.e {d1.d2}=(1,1)
      For m=0 and {p1,p2} = (1,0) the door is opened i.e {d1.d2}=(1,1)
      For m=1 and {p1,p2} = (0,1) the door is opened i.e {d1.d2}=(0,0)
- The masked output format comes as {d1,d2,p2,p1,m}, in which the 3 bits from LSB are input bits which get masked to 0 for the output, thus the masked output is displayed in the spike simulation comes as {1,1,0,0,0}.

   Masked Output - 24 (000011000) for input {p2,p1,m}=(0,1,0);(1,0,1);(1,1,1);(1,1,0)
   Masked Output - 00 (000000000) for rest of inputs 
- **code for spike simulation**
```bash
#include <stdio.h>

int main()
{

	int m;	
	int p1, p2;
	int spike_test;
	
	int d1 = 0;
	int d2 = 0;
	
	int d1_reg;
	int d2_reg;
	
	int mask = 0xFFFFFFE3; 
	
	d1_reg = d1*8;
	d2_reg = d2*16;
	
	asm volatile(
	"and x30, x30, %2\n\t"
    	"or x30, x30, %0\n\t"  
    	"or x30, x30, %1\n\t"  
    	:
    	: "r" (d1_reg), "r" (d2_reg), "r"(mask)
	: "x30"
	);
	
	for(int i=0; i<=2;i++)
	//while(1)
	{
		
		asm volatile(
		"andi %0, x30, 0x01\n\t"
		: "=r" (m)
		:
		:);
		
	
		asm volatile(
		"andi %0, x30, 0x02\n\t"
		: "=r" (p1)
		:
		:);
		
		
		asm volatile(
		"andi %0, x30, 0x04\n\t"
		: "=r" (p2)
		:
		:);
		
		m=0;
		p1=1;
		p2=0;
		
		if(m) //entry
		{
			if(p1 && p2)
			{
				d1=1;
				d2=1;
			}
			else if(p1 && (!p2))
			{	
				d1=0;
				d2=0;
			}
			else if((!p1)&&p2)
			{
				d1=1;
				d2=1;
			}
			else
			{
				d1=0;
				d2=0;
			}
			
		}
		else //exit
		{
			if(p1 && p2)
			{
				d1=1;
				d2=1;
			}
			else if(p1 && (!p2))
			{	
				d1=1;
				d2=1;
			}
			else if((!p1)&&p2)
			{
				d1=0;
				d2=0;
			}
			else
			{
				d1=0;
				d2=0;
			}
			
		}
			
		d1_reg = d1*8;
		d2_reg = d2*16;
	
		asm volatile(
		"and x30, x30, %2\n\t"
    		"or x30, x30, %0\n\t"  
    		"or x30, x30, %1\n\t"  
    		:
    		: "r" (d1_reg), "r" (d2_reg), "r"(mask)
		: "x30"
		);
		
		printf("extry/exit(1/0) - %d \n",m);		
		printf("px1 - %d \t px2 - %d \n",p1,p2);
		printf("door1 - %d \t door1 - %d\n", d1,d2);
		
		asm volatile(
    		"addi %0, x30, 0\n\t"
    		:"=r"(spike_test)
    		:
    		:"x30"
    		);
    		printf("spike_test_result = %d\n",spike_test);	
		
	}

	
return 0;
}
```
- **spike simulation**
``` I have used my friend's laptop cause spike wasn't working on mine```
![Screenshot from 2023-10-25 16-26-41](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/b2ebebbc-4b1b-4e77-8bfd-9575293ead15)
![Screenshot from 2023-10-25 16-26-38](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/7a078c52-d4f4-4e0e-9cc4-06c152b087c5)
![Screenshot from 2023-10-25 16-28-10](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/f3e60627-1f4b-4e45-9437-c4a57f51fce8)
![Screenshot from 2023-10-25 16-28-37](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/53d2c950-f194-4836-b8e8-d06dc16c2f98)

# Functional Simualation

We will perform functional simulation to test the functionality of the verilog code generated for the processor chip. We have tested the processor and its functionality for various input combinations and compare the output generated with the desired expected output. The processor and testbench code are under the file name ```processor.v``` and ```testbench.v```. The files can be found in the repository above.

Note - The inputs for the processor are ```m, p1, p2```. The inputs are taken from LSB for the x30 registor, thus, the input format is given as {p2,p1,m}. ie, the LSB bit is mapped to m and continues. Similar;y the output is generated as {av2,av1,x,c}


- input - 100, expected output -  00
![Screenshot from 2023-10-28 22-57-21](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/327bc367-c3c3-4dec-b22b-6303ad1900f7)

- input - 111, expected output - 11
![Screenshot from 2023-10-28 22-51-41](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/fc33abb1-4785-4dca-a7dd-5910dcfa0510)

- input - 101, expected output - 11
![Screenshot from 2023-10-28 23-03-01](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/d67161fd-bbf0-48f8-82bf-c05cf64c6f14)

- input - 010, expected output - 11
![Screenshot from 2023-10-28 23-08-40](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/c74714e3-5028-427a-943a-75ed7082b36f)

We have seen a few cases and verified the output. We can observe the instruction bit toggling and the input can be seen in the ```input_gpio_pins``` and ```input_write```. We can see the output has been written in the ```output_gpio_pins``` and ```output_write```. We can also observe the ```write_done``` being flagged once the output has been written. Thus we can conclude the processor code is working as expected and we can now move ahead with the synthesis and Gate level simulations.

# Instruction Verification

We will consider the link: ```https://en.wikichip.org/wiki/risc-v/registers``` for refrence and decide the signal pins. Thus, according to the reference given, ```signal43``` is zero register, ```signal45``` is the stack pointer, ```signal51``` is the s0 register and ```signal58``` is the a5 register.

Some of the instructions in the above assembly code were tested in GTKWave and was verified.

**addi sp,sp,-48**
Here we will conside the assembly code line:
```10054:	fd010113          	addi	sp,sp,-48```
We can observe default value of sp is FF after that it becomes CF which is -48 in hexadecimal.

Input instruction - 00000000

Output instruction - FB010113

![Screenshot from 2023-11-01 18-54-47](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/816e892e-c194-4692-a345-720f9eb9daaa)


**addi	s0,sp,48**
Here we will conside the assembly code line:
```1005c:	03010413          	addi	s0,sp,48```

We can see for the instruction03010413 in the register s0 which is $signal$53 will be 000000FF which is 255.

![Screenshot from 2023-11-01 18-50-51](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/b8ad2e48-49de-4469-a5ed-b4c23736c5f7)
![Screenshot from 2023-11-01 18-51-41](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/3841d574-bb7f-4cfe-b54d-74280f171e20)

# The below screenshots are of mutiple possible cases of inputs

- We see the input 011 produces the output low of 00 
![Screenshot from 2023-11-01 18-34-35](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/2c3e9a64-5294-49ac-a428-4c457fdaec7f)
When the output is becoming low the instruction is 00FF6f33 as shown above.

- We see the input 101 produces the output high of 11
![Screenshot from 2023-11-01 18-42-09](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/ed604501-0cd3-4f22-b15c-507aabce9460)
When the output is becoming low the instruction is ED5FF06F as shown above.


# Gate Level Synthesis

Here we do Synthesis of our processor on yosys using the following commands:
```bash
read_liberty -lib sky130_fd_sc_hd__tt_025C_1v80_256.lib 
read_verilog processor.v 
synth -top wrapper
dfflibmap -liberty sky130_fd_sc_hd__tt_025C_1v80_256.lib 
abc -liberty sky130_fd_sc_hd__tt_025C_1v80_256.lib
write_verilog <filename.v>
```

The following Command is used to run the synthesized netlist along with primitives.
```bash
iverilog -o test testbench.v synth_processor_test.v sky130_sram_1kbyte_1rw1r_32x256_8.v sky130_fd_sc_hd.v primitives.v
```
The following screenshot is of the wrapper module using the following command in yosys
```bash
show wrapper
```
![Screenshot from 2023-10-31 22-36-48](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/8044c186-7181-4b77-a55e-c7448ca77340)

The following waveforms are of GLS Simulation obtained using GTKWave and the same output is obtained as Functional Verification.
![Screenshot from 2023-10-31 22-57-05](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/716ab35e-b06f-412a-979b-cf4c6ff0cbf1)

![Screenshot from 2023-11-01 18-34-35](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/0adcc700-cd26-44a9-95d8-4505ce3ef902)

# Physical Design using OPENLANE

**OVERVIEW OF PHYSICAL DESIGN**

Place and Route (PnR) is the core of any ASIC implementation and Openlane flow integrates into it several key open source tools which perform each of the respective stages of PnR. Below are the stages and the respective tools that are called by openlane for the functionalities as described:
![Screenshot from 2023-11-27 00-01-52](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/8d67e55d-dddf-4444-b4f0-c4bd58d3537f)

Below are the stages and the respective tools that are called by openlane for the functionalities as described:

- Synthesis Generating gate-level netlist (yosys). Performing cell mapping (abc). Performing pre-layout STA (OpenSTA).

- Floorplanning Defining the core area for the macro as well as the cell sites and the tracks (init_fp). Placing the macro input and output ports (ioplacer). Generating the power distribution network (pdn).

- Placement Performing global placement (RePLace). Perfroming detailed placement to legalize the globally placed components (OpenDP).

- Clock Tree Synthesis (CTS) Synthesizing the clock tree (TritonCTS).

- Routing Performing global routing to generate a guide file for the detailed router (FastRoute). Performing detailed routing (TritonRoute)

- GDSII Generation Streaming out the final GDSII layout file from the routed def (Magic).

**OPENLANE

OpenLane is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, CVC, SPEF-Extractor, CU-GR, Klayout and a number of custom scripts for design exploration and optimization. The flow performs full ASIC implementation steps from RTL all the way down to GDSII.

More about Openlane at : https://github.com/The-OpenROAD-Project/OpenLane

# MAGIC

Magic is a venerable VLSI layout tool, written in the 1980's at Berkeley by John Ousterhout, now famous primarily for writing the scripting interpreter language Tcl. Due largely in part to its liberal Berkeley open-source license, magic has remained popular with universities and small companies. The open-source license has allowed VLSI engineers with a bent toward programming to implement clever ideas and help magic stay abreast of fabrication technology. However, it is the well thought-out core algorithms which lend to magic the greatest part of its popularity. Magic is widely cited as being the easiest tool to use for circuit layout, even for people who ultimately rely on commercial tools for their product design flow.

More about magic at http://opencircuitdesign.com/magic/index.html

# Preparing the Design

Preparing the design and including the lef files: The commands to prepare the design and overwite in a existing run folder the reports and results along with the command to include the lef files is given below:
```bash
sed -i's/max_transition   :0.04/max_transition   :0.75/g'*/*.lib
```

OpenLane Interactive Flow:
```bash
make mount
%./flow.tcl -interactive
% package require openlane 0.9
% prep -design project project_simar
```
<img width="866" alt="1" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/38f708af-e53b-4620-888f-42e0ee616123">

- Completion of synthesis, floorplan, placement, CTS, routing.

<img width="866" alt="2" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/defee84e-67b1-4326-9f78-baedd8c7ee81">

```bash
run_magic
run_magic_spice_export
run_magic_drc
run_antenna_check
```
<img width="865" alt="3" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/acefa0f1-7dad-42b6-bd67-fb03564dba9f">

# Reports

**SYNTHESIS**

- Logic synthesis uses the RTL netlist to perform HDL technology mapping. The synthesis process is normally performed in two major steps:
      -  GTECH Mapping – Consists of mapping the HDL netlist to generic gates what are used to perform logical optimization based on AIGERs and other topologies created from the generic mapped netlist
      -  Technology Mapping – Consists of mapping the post-optimized GTECH netlist to standard cells described in the PDK.

- To synthesize the code run the following command.

<img width="680" alt="synth_log" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/f21dcaa8-e840-48a4-a0d7-14340cc7e5f5">

FLOORPLAN

- Goal is to plan the silicon area and create a robust power distribution network (PDN) to power each of the individual components of the synthesized netlist. In addition, macro placement and blockages must be defined before placement occurs to ensure a legalized GDS file. In power planning we create the ring which is connected to the pads which brings power around the edges of the chip. We also include power straps to bring power to the middle of the chip using higher metal layers which reduces IR drop and electro-migration problem.

- Following command helps to run floorplan:

```bash
run_floorplan
```
- To view the floorplan on Magic from ```results/floorplan```
```bash
 magic -T ~/.volare/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read wrapper.def &
```

<img width="962" alt="floorplan" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/2aac5a67-6177-4838-ad07-12f297472f75">

- Core Area after floorplan

<img width="674" alt="core_area" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/aab9d32a-853f-4902-9f87-9e460df436a3">

- Die Area after floorplan

<img width="707" alt="die_area" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/722b470c-3d09-4052-a590-0688f479cef9">

**PLACEMENT**

- Place the standard cells on the floorplane rows, aligned with sites defined in the technology lef file. Placement is done in two steps: Global and Detailed. In Global placement tries to find optimal position for all cells but they may be overlapping and not aligned to rows, detailed placement takes the global placement and legalizes all of the placements trying to adhere to what the global placement wants. The next step in the OpenLANE ASIC flow is placement. The synthesized netlist is to be placed on the floorplan. Placement is perfomed in 2 stages:
      - Global Placement
      - Detailed Placement
  
- Run the following command to run the placement:  
```bash
run_placement
```
- To view the placement on Magic from results/placement
```bash
 magic -T ~/.volare/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read wrapper.def &
```
<img width="1071" alt="placement" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/d6f815ee-3093-432e-b838-c7ee74aa5d2b">

**CLOCK TREE SYNTHESIS**

Clock tree synteshsis is used to create the clock distribution network that is used to deliver the clock to all sequential elements. The main goal is to create a network with minimal skew across the chip. H-trees are a common network topology that is used to achieve this goal.

The purpose of building a clock tree is enable the clock input to reach every element and to ensure a zero clock skew. H-tree is a common methodology followed in CTS. Following command is used to run CTS.
```bash
run_cts
```
- Timimg Report

<img width="673" alt="timing" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/5f71ba58-4f42-4faf-89be-1c21128bf996">

-  Area Report

<img width="685" alt="area" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/16cbc721-03d2-493d-9141-8f580ea717a4">

-  Skew Report

<img width="605" alt="skew" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/19a18af5-cb79-4cda-a4d1-94aeff1914af">

- Power Report

<img width="667" alt="power" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/b600e8e6-6a77-4a0c-bfdb-5b8e7353b255">

**POWER NETWORK DISTRIBUTION**

- The commmand to establish power distribution network is as follows
```bash
gen_pdn
```
**ROUTING**

- Implements the interconnect system between standard cells using the remaining available metal layers after CTS and PDN generation. The routing is performed on routing grids to ensure minimal DRC errors.

- OpenLANE uses the TritonRoute tool for routing. There are 2 stages of routing:
      
    Global Routing
    Detailed Routing


- In Global Routing Routing region is divided into rectangle grids which are represented as course 3D routes (Fastroute tool).

- In Detailed Finer grids and routing guides used to implement physical wiring (TritonRoute tool).

- Run the following command to run the routing
```bash
run_routing
```
- The layout can be viewed using MAGIC in results/routing
```bash
 magic -T ~/.volare/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read wrapper.def &
```
<img width="959" alt="routing" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/7e0d719b-2606-4738-88a9-1a12b7b44182">


- Area of Design
<img width="616" alt="magic" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/ed150954-f766-4220-8de2-ac9c680dd01b">

- Post Routing Reports
- Timing
  
<img width="689" alt="rout_timing" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/9a13e90d-ceba-45d5-8a41-e065e2931bcf">

- Area
 
<img width="634" alt="rout_area" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/8d028003-e3a9-4368-8d8b-03a13ad3db8b">

- Power
 
 <img width="653" alt="rout_power" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/ef0de36d-d47f-4e32-a65d-1097b81ebad0">

- Design Rule Check (DRC)

 <img width="689" alt="drc" src="https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/b28f96f6-43db-4ce4-aa33-ce67425a1c19">

# Word of Thanks

I would take this opportunity to sciencerly thank Mr. Kunal Gosh(Founder/VSD) for helping me out to complete this flow smoothly.

Acknowledgement

- Kunal Ghosh, VSD Corp. Pvt. Ltd.
- Skywater Foundry
- OpenAI Chatgpt
- Alwin Shaju, MTech, IIITB
- Emil Jayanth Lal, MTech, IIITB
- N Sai Sampath, MTech, IIITB
- Mayank Kabra, Founder, Chipcron Pvt.Ltd.
- Sahil Singh Rana, MTech, IIITB
- Kanish R, MTech, IIITB
