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


int trigger;
int echo;
int buzzer=0;
int pulse_counter=0;
int switch_val;

void read();
void pulse_count();
void distance_calc();
void trigger_send();
void delay_10us();
void delay_100ms();


int main() {
  int buzzer_reg;
  buzzer_reg = buzzer*4;
  asm volatile(
	"or x30, x30, %0\n\t"
	:"=r"(buzzer_reg));
	
  while (1) {
    read();
    }
    return 0;
}

void read()
{
  asm volatile(
	"andi %0, x30, 1\n\t" 
	:"=r"(switch_val));
  if(switch_val){
  pulse_count();
  pulse_counter=0;
  delay_100ms();
  }
}

void pulse_count()
{ 
  trigger_send();
   asm volatile(
	"andi %0, x30, 2\n\t" 
	:"=r"(echo));
  while(echo){pulse_counter = pulse_counter + 1 ; }
  distance_calc();
}

void trigger_send()
{
  int trigger_reg;
  trigger = 1; 
  trigger_reg = trigger*8;
  asm volatile(
	"or x30, x30, %0\n\t" 
	:"=r"(switch_val));
  delay_10us();
  trigger =0;
  trigger_reg = trigger*8;
  asm volatile(
	"or x30, x30, %0\n\t" 
	:"=r"(switch_val));
}

void distance_calc()
{
  int distance;
  int buzzer_reg;
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
}

void delay_10us ()
{
for (int i=0; i<10; i++);
}

void delay_100ms ()
{
for (int i=0; i< 100000; i++);
}
```
# Assembly Code
```bash

out:     file format elf32-littleriscv


Disassembly of section .text:

00010094 <main>:
   10094:	fe010113          	add	sp,sp,-32
   10098:	00112e23          	sw	ra,28(sp)
   1009c:	00812c23          	sw	s0,24(sp)
   100a0:	02010413          	add	s0,sp,32
   100a4:	8081a783          	lw	a5,-2040(gp) # 112d4 <buzzer>
   100a8:	00279793          	sll	a5,a5,0x2
   100ac:	fef42623          	sw	a5,-20(s0)
   100b0:	00ff6f33          	or	t5,t5,a5
   100b4:	fef42623          	sw	a5,-20(s0)
   100b8:	008000ef          	jal	100c0 <read>
   100bc:	ffdff06f          	j	100b8 <main+0x24>

000100c0 <read>:
   100c0:	ff010113          	add	sp,sp,-16
   100c4:	00112623          	sw	ra,12(sp)
   100c8:	00812423          	sw	s0,8(sp)
   100cc:	01010413          	add	s0,sp,16
   100d0:	001f7713          	and	a4,t5,1
   100d4:	80e1a823          	sw	a4,-2032(gp) # 112dc <switch_val>
   100d8:	8101a783          	lw	a5,-2032(gp) # 112dc <switch_val>
   100dc:	00078863          	beqz	a5,100ec <read+0x2c>
   100e0:	020000ef          	jal	10100 <pulse_count>
   100e4:	8001a623          	sw	zero,-2036(gp) # 112d8 <pulse_counter>
   100e8:	1a0000ef          	jal	10288 <delay_100ms>
   100ec:	00000013          	nop
   100f0:	00c12083          	lw	ra,12(sp)
   100f4:	00812403          	lw	s0,8(sp)
   100f8:	01010113          	add	sp,sp,16
   100fc:	00008067          	ret

00010100 <pulse_count>:
   10100:	ff010113          	add	sp,sp,-16
   10104:	00112623          	sw	ra,12(sp)
   10108:	00812423          	sw	s0,8(sp)
   1010c:	01010413          	add	s0,sp,16
   10110:	044000ef          	jal	10154 <trigger_send>
   10114:	002f7713          	and	a4,t5,2
   10118:	000117b7          	lui	a5,0x11
   1011c:	2ce7a823          	sw	a4,720(a5) # 112d0 <echo>
   10120:	0100006f          	j	10130 <pulse_count+0x30>
   10124:	80c1a783          	lw	a5,-2036(gp) # 112d8 <pulse_counter>
   10128:	00178713          	add	a4,a5,1
   1012c:	80e1a623          	sw	a4,-2036(gp) # 112d8 <pulse_counter>
   10130:	000117b7          	lui	a5,0x11
   10134:	2d07a783          	lw	a5,720(a5) # 112d0 <echo>
   10138:	fe0796e3          	bnez	a5,10124 <pulse_count+0x24>
   1013c:	084000ef          	jal	101c0 <distance_calc>
   10140:	00000013          	nop
   10144:	00c12083          	lw	ra,12(sp)
   10148:	00812403          	lw	s0,8(sp)
   1014c:	01010113          	add	sp,sp,16
   10150:	00008067          	ret

00010154 <trigger_send>:
   10154:	fe010113          	add	sp,sp,-32
   10158:	00112e23          	sw	ra,28(sp)
   1015c:	00812c23          	sw	s0,24(sp)
   10160:	02010413          	add	s0,sp,32
   10164:	000117b7          	lui	a5,0x11
   10168:	00100713          	li	a4,1
   1016c:	2ce7a623          	sw	a4,716(a5) # 112cc <__DATA_BEGIN__>
   10170:	000117b7          	lui	a5,0x11
   10174:	2cc7a783          	lw	a5,716(a5) # 112cc <__DATA_BEGIN__>
   10178:	00379793          	sll	a5,a5,0x3
   1017c:	fef42623          	sw	a5,-20(s0)
   10180:	00ef6f33          	or	t5,t5,a4
   10184:	80e1a823          	sw	a4,-2032(gp) # 112dc <switch_val>
   10188:	0c0000ef          	jal	10248 <delay_10us>
   1018c:	000117b7          	lui	a5,0x11
   10190:	2c07a623          	sw	zero,716(a5) # 112cc <__DATA_BEGIN__>
   10194:	000117b7          	lui	a5,0x11
   10198:	2cc7a783          	lw	a5,716(a5) # 112cc <__DATA_BEGIN__>
   1019c:	00379793          	sll	a5,a5,0x3
   101a0:	fef42623          	sw	a5,-20(s0)
   101a4:	00ef6f33          	or	t5,t5,a4
   101a8:	80e1a823          	sw	a4,-2032(gp) # 112dc <switch_val>
   101ac:	00000013          	nop
   101b0:	01c12083          	lw	ra,28(sp)
   101b4:	01812403          	lw	s0,24(sp)
   101b8:	02010113          	add	sp,sp,32
   101bc:	00008067          	ret

000101c0 <distance_calc>:
   101c0:	fe010113          	add	sp,sp,-32
   101c4:	00812e23          	sw	s0,28(sp)
   101c8:	02010413          	add	s0,sp,32
   101cc:	80c1a703          	lw	a4,-2036(gp) # 112d8 <pulse_counter>
   101d0:	00070793          	mv	a5,a4
   101d4:	00179793          	sll	a5,a5,0x1
   101d8:	00e787b3          	add	a5,a5,a4
   101dc:	00279793          	sll	a5,a5,0x2
   101e0:	40e787b3          	sub	a5,a5,a4
   101e4:	00279793          	sll	a5,a5,0x2
   101e8:	40e787b3          	sub	a5,a5,a4
   101ec:	00279793          	sll	a5,a5,0x2
   101f0:	fef42623          	sw	a5,-20(s0)
   101f4:	fec42703          	lw	a4,-20(s0)
   101f8:	00a00793          	li	a5,10
   101fc:	02e7d263          	bge	a5,a4,10220 <distance_calc+0x60>
   10200:	00100713          	li	a4,1
   10204:	80e1a423          	sw	a4,-2040(gp) # 112d4 <buzzer>
   10208:	8081a783          	lw	a5,-2040(gp) # 112d4 <buzzer>
   1020c:	00279793          	sll	a5,a5,0x2
   10210:	fef42423          	sw	a5,-24(s0)
   10214:	00ff6f33          	or	t5,t5,a5
   10218:	fef42423          	sw	a5,-24(s0)
   1021c:	01c0006f          	j	10238 <distance_calc+0x78>
   10220:	8001a423          	sw	zero,-2040(gp) # 112d4 <buzzer>
   10224:	8081a783          	lw	a5,-2040(gp) # 112d4 <buzzer>
   10228:	00279793          	sll	a5,a5,0x2
   1022c:	fef42423          	sw	a5,-24(s0)
   10230:	00ff6f33          	or	t5,t5,a5
   10234:	fef42423          	sw	a5,-24(s0)
   10238:	00000013          	nop
   1023c:	01c12403          	lw	s0,28(sp)
   10240:	02010113          	add	sp,sp,32
   10244:	00008067          	ret

00010248 <delay_10us>:
   10248:	fe010113          	add	sp,sp,-32
   1024c:	00812e23          	sw	s0,28(sp)
   10250:	02010413          	add	s0,sp,32
   10254:	fe042623          	sw	zero,-20(s0)
   10258:	0100006f          	j	10268 <delay_10us+0x20>
   1025c:	fec42783          	lw	a5,-20(s0)
   10260:	00178793          	add	a5,a5,1
   10264:	fef42623          	sw	a5,-20(s0)
   10268:	fec42703          	lw	a4,-20(s0)
   1026c:	00900793          	li	a5,9
   10270:	fee7d6e3          	bge	a5,a4,1025c <delay_10us+0x14>
   10274:	00000013          	nop
   10278:	00000013          	nop
   1027c:	01c12403          	lw	s0,28(sp)
   10280:	02010113          	add	sp,sp,32
   10284:	00008067          	ret

00010288 <delay_100ms>:
   10288:	fe010113          	add	sp,sp,-32
   1028c:	00812e23          	sw	s0,28(sp)
   10290:	02010413          	add	s0,sp,32
   10294:	fe042623          	sw	zero,-20(s0)
   10298:	0100006f          	j	102a8 <delay_100ms+0x20>
   1029c:	fec42783          	lw	a5,-20(s0)
   102a0:	00178793          	add	a5,a5,1
   102a4:	fef42623          	sw	a5,-20(s0)
   102a8:	fec42703          	lw	a4,-20(s0)
   102ac:	000187b7          	lui	a5,0x18
   102b0:	69f78793          	add	a5,a5,1695 # 1869f <__global_pointer$+0x6bd3>
   102b4:	fee7d4e3          	bge	a5,a4,1029c <delay_100ms+0x14>
   102b8:	00000013          	nop
   102bc:	00000013          	nop
   102c0:	01c12403          	lw	s0,28(sp)
   102c4:	02010113          	add	sp,sp,32
   102c8:	00008067          	ret

```

# Unique Instructions
```bash
Number of different instructions: 17
List of unique instructions:
sll
bnez
jal
bge
nop
ret
or
mv
beqz
lui
sw
li
add
j
sub
and
lw

```

