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
  asm(
	"or x30, x30, %0\n\t"
	:"=r"(buzzer_reg));
	
  while (1) {
    //read();
    asm(
	"andi %0, x30, 1\n\t" 
	:"=r"(switch_val));
  if(switch_val){
  //pulse_count();
  //int trigger_reg;
  trigger = 1; 
  trigger_reg = trigger*8;
  asm(
	"or x30, x30, %0\n\t" 
	:"=r"(switch_val));
  //delay_10us();

for ( i=0; i<10; i++);

  trigger =0;
  trigger_reg = trigger*8;
  asm(
	"or x30, x30, %0\n\t" 
	:"=r"(switch_val));
   asm(
	"andi %0, x30, 2\n\t" 
	:"=r"(echo));
  while(echo){pulse_counter = pulse_counter + 1 ; }
  //distance_calc();
  //float distance;
  //int buzzer_reg;
  distance= pulse_counter* 172;
  if (distance >10)
   { buzzer=1;
     buzzer_reg = buzzer*4;
     asm(
	"or x30, x30, %0\n\t" 
	:"=r"(buzzer_reg));
   }
  else{
    buzzer = 0;
    buzzer_reg = buzzer*4;
     asm(
	"or x30, x30, %0\n\t" 
	:"=r"(buzzer_reg));
    }
  pulse_counter=0;
  //delay_100ms();
  
for (i=0; i< 100000; i++);


  }
    }
    return 0;
}

```
# Assembly Code
```bash

out:     file format elf32-littleriscv


Disassembly of section .text:

out:     file format elf32-littleriscv


Disassembly of section .text:

00010054 <main>:
   10054:	fc010113          	addi	sp,sp,-64
   10058:	02812e23          	sw	s0,60(sp)
   1005c:	04010413          	addi	s0,sp,64
   10060:	fe042223          	sw	zero,-28(s0)
   10064:	fe042423          	sw	zero,-24(s0)
   10068:	fe442783          	lw	a5,-28(s0)
   1006c:	00279793          	slli	a5,a5,0x2
   10070:	fef42023          	sw	a5,-32(s0)
   10074:	00ff6f33          	or	t5,t5,a5
   10078:	fef42023          	sw	a5,-32(s0)
   1007c:	001f7793          	andi	a5,t5,1
   10080:	fcf42e23          	sw	a5,-36(s0)
   10084:	fdc42783          	lw	a5,-36(s0)
   10088:	fe078ae3          	beqz	a5,1007c <main+0x28>
   1008c:	00100793          	li	a5,1
   10090:	fcf42c23          	sw	a5,-40(s0)
   10094:	fd842783          	lw	a5,-40(s0)
   10098:	00379793          	slli	a5,a5,0x3
   1009c:	fcf42a23          	sw	a5,-44(s0)
   100a0:	00ff6f33          	or	t5,t5,a5
   100a4:	fcf42e23          	sw	a5,-36(s0)
   100a8:	fe042623          	sw	zero,-20(s0)
   100ac:	0100006f          	j	100bc <main+0x68>
   100b0:	fec42783          	lw	a5,-20(s0)
   100b4:	00178793          	addi	a5,a5,1
   100b8:	fef42623          	sw	a5,-20(s0)
   100bc:	fec42703          	lw	a4,-20(s0)
   100c0:	00900793          	li	a5,9
   100c4:	fee7d6e3          	bge	a5,a4,100b0 <main+0x5c>
   100c8:	fc042c23          	sw	zero,-40(s0)
   100cc:	fd842783          	lw	a5,-40(s0)
   100d0:	00379793          	slli	a5,a5,0x3
   100d4:	fcf42a23          	sw	a5,-44(s0)
   100d8:	00ff6f33          	or	t5,t5,a5
   100dc:	fcf42e23          	sw	a5,-36(s0)
   100e0:	002f7793          	andi	a5,t5,2
   100e4:	fcf42823          	sw	a5,-48(s0)
   100e8:	0100006f          	j	100f8 <main+0xa4>
   100ec:	fe842783          	lw	a5,-24(s0)
   100f0:	00178793          	addi	a5,a5,1
   100f4:	fef42423          	sw	a5,-24(s0)
   100f8:	fd042783          	lw	a5,-48(s0)
   100fc:	fe0798e3          	bnez	a5,100ec <main+0x98>
   10100:	fe842703          	lw	a4,-24(s0)
   10104:	00070793          	mv	a5,a4
   10108:	00179793          	slli	a5,a5,0x1
   1010c:	00e787b3          	add	a5,a5,a4
   10110:	00279793          	slli	a5,a5,0x2
   10114:	40e787b3          	sub	a5,a5,a4
   10118:	00279793          	slli	a5,a5,0x2
   1011c:	40e787b3          	sub	a5,a5,a4
   10120:	00279793          	slli	a5,a5,0x2
   10124:	fcf42623          	sw	a5,-52(s0)
   10128:	fcc42703          	lw	a4,-52(s0)
   1012c:	00a00793          	li	a5,10
   10130:	02e7d263          	bge	a5,a4,10154 <main+0x100>
   10134:	00100793          	li	a5,1
   10138:	fef42223          	sw	a5,-28(s0)
   1013c:	fe442783          	lw	a5,-28(s0)
   10140:	00279793          	slli	a5,a5,0x2
   10144:	fef42023          	sw	a5,-32(s0)
   10148:	00ff6f33          	or	t5,t5,a5
   1014c:	fef42023          	sw	a5,-32(s0)
   10150:	01c0006f          	j	1016c <main+0x118>
   10154:	fe042223          	sw	zero,-28(s0)
   10158:	fe442783          	lw	a5,-28(s0)
   1015c:	00279793          	slli	a5,a5,0x2
   10160:	fef42023          	sw	a5,-32(s0)
   10164:	00ff6f33          	or	t5,t5,a5
   10168:	fef42023          	sw	a5,-32(s0)
   1016c:	fe042423          	sw	zero,-24(s0)
   10170:	fe042623          	sw	zero,-20(s0)
   10174:	0100006f          	j	10184 <main+0x130>
   10178:	fec42783          	lw	a5,-20(s0)
   1017c:	00178793          	addi	a5,a5,1
   10180:	fef42623          	sw	a5,-20(s0)
   10184:	fec42703          	lw	a4,-20(s0)
   10188:	000187b7          	lui	a5,0x18
   1018c:	69f78793          	addi	a5,a5,1695 # 1869f <__global_pointer$+0x6d07>
   10190:	fee7d4e3          	bge	a5,a4,10178 <main+0x124>
   10194:	ee9ff06f          	j	1007c <main+0x28>

```

# Unique Instructions
```bash
Number of different instructions: 15
List of unique instructions:
lw
add
bge
andi
mv
li
addi
or
beqz
sub
slli
sw
lui
bnez
j



```

