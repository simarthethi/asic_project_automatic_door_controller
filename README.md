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
int main() {
  int i;
  int buzzer_reg;
  int trigger;
  int echo;
  int buzzer=0;
  int pulse_counter=0;
  int switch_val;
  int distance;
  int trigger_reg;
  buzzer_reg = buzzer*4;
  asm volatile(
	"or x30, x30, %0\n\t"
	:"=r"(buzzer_reg));
	
	
  //trigger_send();
   asm volatile(
	"andi %0, x30, 2\n\t" 
	:"=r"(echo));
  while(echo){pulse_counter = pulse_counter + 1 ; }
  //distance_calc();
  
  trigger = 1; 
  trigger_reg = trigger*8;
  asm volatile(
	"or x30, x30, %0\n\t" 
	:"=r"(switch_val));
  for (int i=0; i<10; i++);
  trigger =0;
  trigger_reg = trigger*8;
  asm volatile(
	"or x30, x30, %0\n\t" 
	:"=r"(switch_val));
	

  distance= pulse_counter * 172;
  //distance =  3 * 2;
  if (distance >10)
   { buzzer=1;
     buzzer_reg = buzzer*4;
     asm volatile(
	"or x30, x30, %0\n\t" 
	:"=r"(buzzer_reg));
   }
  else{
    buzzer = 0;
    buzzer_reg = buzzer*4;
     asm volatile(
	"or x30, x30, %0\n\t" 
	:"=r"(buzzer_reg));
    }	
	
	
  while (1) {
  asm volatile(
	"andi %0, x30, 1\n\t" 
	:"=r"(switch_val));
  if(switch_val){
  //pulse_count();
  pulse_counter=0;
  for (int i=0; i< 100000; i++);
  }
    
    }
    return 0;
}


```
# Assembly Code
```bash

out:     file format elf32-littleriscv


Disassembly of section .text:

00010054 <main>:
   10054:	fc010113          	addi	sp,sp,-64
   10058:	02812e23          	sw	s0,60(sp)
   1005c:	04010413          	addi	s0,sp,64
   10060:	fe042023          	sw	zero,-32(s0)
   10064:	fe042623          	sw	zero,-20(s0)
   10068:	fe042783          	lw	a5,-32(s0)
   1006c:	00279793          	slli	a5,a5,0x2
   10070:	fcf42e23          	sw	a5,-36(s0)
   10074:	00ff6f33          	or	t5,t5,a5
   10078:	fcf42e23          	sw	a5,-36(s0)
   1007c:	002f7793          	andi	a5,t5,2
   10080:	fcf42c23          	sw	a5,-40(s0)
   10084:	0100006f          	j	10094 <main+0x40>
   10088:	fec42783          	lw	a5,-20(s0)
   1008c:	00178793          	addi	a5,a5,1
   10090:	fef42623          	sw	a5,-20(s0)
   10094:	fd842783          	lw	a5,-40(s0)
   10098:	fe0798e3          	bnez	a5,10088 <main+0x34>
   1009c:	00100793          	li	a5,1
   100a0:	fcf42a23          	sw	a5,-44(s0)
   100a4:	fd442783          	lw	a5,-44(s0)
   100a8:	00379793          	slli	a5,a5,0x3
   100ac:	fcf42823          	sw	a5,-48(s0)
   100b0:	00ff6f33          	or	t5,t5,a5
   100b4:	fcf42623          	sw	a5,-52(s0)
   100b8:	fe042423          	sw	zero,-24(s0)
   100bc:	0100006f          	j	100cc <main+0x78>
   100c0:	fe842783          	lw	a5,-24(s0)
   100c4:	00178793          	addi	a5,a5,1
   100c8:	fef42423          	sw	a5,-24(s0)
   100cc:	fe842703          	lw	a4,-24(s0)
   100d0:	00900793          	li	a5,9
   100d4:	fee7d6e3          	bge	a5,a4,100c0 <main+0x6c>
   100d8:	fc042a23          	sw	zero,-44(s0)
   100dc:	fd442783          	lw	a5,-44(s0)
   100e0:	00379793          	slli	a5,a5,0x3
   100e4:	fcf42823          	sw	a5,-48(s0)
   100e8:	00ff6f33          	or	t5,t5,a5
   100ec:	fcf42623          	sw	a5,-52(s0)
   100f0:	fec42703          	lw	a4,-20(s0)
   100f4:	00070793          	mv	a5,a4
   100f8:	00179793          	slli	a5,a5,0x1
   100fc:	00e787b3          	add	a5,a5,a4
   10100:	00279793          	slli	a5,a5,0x2
   10104:	40e787b3          	sub	a5,a5,a4
   10108:	00279793          	slli	a5,a5,0x2
   1010c:	40e787b3          	sub	a5,a5,a4
   10110:	00279793          	slli	a5,a5,0x2
   10114:	fcf42423          	sw	a5,-56(s0)
   10118:	fc842703          	lw	a4,-56(s0)
   1011c:	00a00793          	li	a5,10
   10120:	02e7d263          	bge	a5,a4,10144 <main+0xf0>
   10124:	00100793          	li	a5,1
   10128:	fef42023          	sw	a5,-32(s0)
   1012c:	fe042783          	lw	a5,-32(s0)
   10130:	00279793          	slli	a5,a5,0x2
   10134:	fcf42e23          	sw	a5,-36(s0)
   10138:	00ff6f33          	or	t5,t5,a5
   1013c:	fcf42e23          	sw	a5,-36(s0)
   10140:	01c0006f          	j	1015c <main+0x108>
   10144:	fe042023          	sw	zero,-32(s0)
   10148:	fe042783          	lw	a5,-32(s0)
   1014c:	00279793          	slli	a5,a5,0x2
   10150:	fcf42e23          	sw	a5,-36(s0)
   10154:	00ff6f33          	or	t5,t5,a5
   10158:	fcf42e23          	sw	a5,-36(s0)
   1015c:	001f7793          	andi	a5,t5,1
   10160:	fcf42623          	sw	a5,-52(s0)
   10164:	fcc42783          	lw	a5,-52(s0)
   10168:	fe078ae3          	beqz	a5,1015c <main+0x108>
   1016c:	fe042623          	sw	zero,-20(s0)
   10170:	fe042223          	sw	zero,-28(s0)
   10174:	0100006f          	j	10184 <main+0x130>
   10178:	fe442783          	lw	a5,-28(s0)
   1017c:	00178793          	addi	a5,a5,1
   10180:	fef42223          	sw	a5,-28(s0)
   10184:	fe442703          	lw	a4,-28(s0)
   10188:	000187b7          	lui	a5,0x18
   1018c:	69f78793          	addi	a5,a5,1695 # 1869f <__global_pointer$+0x6d07>
   10190:	fee7d4e3          	bge	a5,a4,10178 <main+0x124>
   10194:	fc9ff06f          	j	1015c <main+0x108>

```

# Unique Instructions
```bash
Number of different instructions: 15
List of unique instructions:
lui
mv
addi
bnez
add
bge
sub
or
lw
sw
andi
beqz
li
slli
j



```

