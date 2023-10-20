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

00010074 <main>:
   10074:	fc010113          	add	sp,sp,-64
   10078:	02812e23          	sw	s0,60(sp)
   1007c:	04010413          	add	s0,sp,64
   10080:	fe042023          	sw	zero,-32(s0)
   10084:	fe042623          	sw	zero,-20(s0)
   10088:	fe042783          	lw	a5,-32(s0)
   1008c:	00279793          	sll	a5,a5,0x2
   10090:	fcf42e23          	sw	a5,-36(s0)
   10094:	00ff6f33          	or	t5,t5,a5
   10098:	fcf42e23          	sw	a5,-36(s0)
   1009c:	002f7793          	and	a5,t5,2
   100a0:	fcf42c23          	sw	a5,-40(s0)
   100a4:	0100006f          	j	100b4 <main+0x40>
   100a8:	fec42783          	lw	a5,-20(s0)
   100ac:	00178793          	add	a5,a5,1
   100b0:	fef42623          	sw	a5,-20(s0)
   100b4:	fd842783          	lw	a5,-40(s0)
   100b8:	fe0798e3          	bnez	a5,100a8 <main+0x34>
   100bc:	00100793          	li	a5,1
   100c0:	fcf42a23          	sw	a5,-44(s0)
   100c4:	fd442783          	lw	a5,-44(s0)
   100c8:	00379793          	sll	a5,a5,0x3
   100cc:	fcf42823          	sw	a5,-48(s0)
   100d0:	00ff6f33          	or	t5,t5,a5
   100d4:	fcf42623          	sw	a5,-52(s0)
   100d8:	fe042423          	sw	zero,-24(s0)
   100dc:	0100006f          	j	100ec <main+0x78>
   100e0:	fe842783          	lw	a5,-24(s0)
   100e4:	00178793          	add	a5,a5,1
   100e8:	fef42423          	sw	a5,-24(s0)
   100ec:	fe842703          	lw	a4,-24(s0)
   100f0:	00900793          	li	a5,9
   100f4:	fee7d6e3          	bge	a5,a4,100e0 <main+0x6c>
   100f8:	fc042a23          	sw	zero,-44(s0)
   100fc:	fd442783          	lw	a5,-44(s0)
   10100:	00379793          	sll	a5,a5,0x3
   10104:	fcf42823          	sw	a5,-48(s0)
   10108:	00ff6f33          	or	t5,t5,a5
   1010c:	fcf42623          	sw	a5,-52(s0)
   10110:	fec42703          	lw	a4,-20(s0)
   10114:	00070793          	mv	a5,a4
   10118:	00179793          	sll	a5,a5,0x1
   1011c:	00e787b3          	add	a5,a5,a4
   10120:	00279793          	sll	a5,a5,0x2
   10124:	40e787b3          	sub	a5,a5,a4
   10128:	00279793          	sll	a5,a5,0x2
   1012c:	40e787b3          	sub	a5,a5,a4
   10130:	00279793          	sll	a5,a5,0x2
   10134:	fcf42423          	sw	a5,-56(s0)
   10138:	fc842703          	lw	a4,-56(s0)
   1013c:	00a00793          	li	a5,10
   10140:	02e7d263          	bge	a5,a4,10164 <main+0xf0>
   10144:	00100793          	li	a5,1
   10148:	fef42023          	sw	a5,-32(s0)
   1014c:	fe042783          	lw	a5,-32(s0)
   10150:	00279793          	sll	a5,a5,0x2
   10154:	fcf42e23          	sw	a5,-36(s0)
   10158:	00ff6f33          	or	t5,t5,a5
   1015c:	fcf42e23          	sw	a5,-36(s0)
   10160:	01c0006f          	j	1017c <main+0x108>
   10164:	fe042023          	sw	zero,-32(s0)
   10168:	fe042783          	lw	a5,-32(s0)
   1016c:	00279793          	sll	a5,a5,0x2
   10170:	fcf42e23          	sw	a5,-36(s0)
   10174:	00ff6f33          	or	t5,t5,a5
   10178:	fcf42e23          	sw	a5,-36(s0)
   1017c:	001f7793          	and	a5,t5,1
   10180:	fcf42623          	sw	a5,-52(s0)
   10184:	fcc42783          	lw	a5,-52(s0)
   10188:	fe078ae3          	beqz	a5,1017c <main+0x108>
   1018c:	fe042623          	sw	zero,-20(s0)
   10190:	fe042223          	sw	zero,-28(s0)
   10194:	0100006f          	j	101a4 <main+0x130>
   10198:	fe442783          	lw	a5,-28(s0)
   1019c:	00178793          	add	a5,a5,1
   101a0:	fef42223          	sw	a5,-28(s0)
   101a4:	fe442703          	lw	a4,-28(s0)
   101a8:	000187b7          	lui	a5,0x18
   101ac:	69f78793          	add	a5,a5,1695 # 1869f <__global_pointer$+0x6ce7>
   101b0:	fee7d4e3          	bge	a5,a4,10198 <main+0x124>
   101b4:	fc9ff06f          	j	1017c <main+0x108>

```

# Unique Instructions
```bash
Number of different instructions: 14
List of unique instructions:
sw
and
beqz
li
sll
lui
add
bnez
bge
or
mv
sub
j
lw


```

