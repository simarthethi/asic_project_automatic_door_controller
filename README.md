# asic_project_blind-stick
This github repository summarizes the progress made in the ASIC class for the riscv_project.

# Aim of the project
The aim of the prohect is to design a blind stick utilizing a specialized RISCV processor for pothole detction, with the objective that a blind person will be able to walk a bit more safely

# Working
A transmitter and an ultrasonic sensor is employed at the bottom of the stick to dtect the obstacle/pothole. Upon activating the transmitter an unltrasonic wave will transmit. Upon detection, the buzzer would go on indicating that there is something nearby.

# Circuit Diagram
The following is not the final circuit but just for reference

![Screenshot from 2023-10-05 10-29-33](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/4e51e417-326f-4e24-a5a6-34b6b9c89fc9)



# Block Diagram

![Screenshot from 2023-10-05 10-16-26](https://github.com/simarthethi/asic_project_blind-stick/assets/140998783/759cf073-f28c-4b43-9b0b-e4b9b9d3b657)

# C code
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

