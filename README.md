# asic_project_blind-stick
This github repository summarizes the progress made in the ASIC class for the riscv_project.

# Aim of the project
The aim of the prohect is to design a blind stick utilizing a specialized RISCV processor for pothole detction, with the objective that a blind person will be able to walk a bit more safely

# Working
A transmitter and an ultrasonic sensor is employed at the bottom of the stick to dtect the obstacle/pothole. Upon activating the transmitter an unltrasonic wave will transmit. Upon detection, the buzzer would go on indicating that there is something nearby.

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
  asm(
	"or x30, x30, %0\n\t"
	:"=r"(buzzer_reg));
	
  while (1) {
    read();
    }
    return 0;
}

void read()
{
  asm(
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
   asm(
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
  asm(
	"or x30, x30, %0\n\t" 
	:"=r"(switch_val));
  delay_10us();
  trigger =0;
  trigger_reg = trigger*8;
  asm(
	"or x30, x30, %0\n\t" 
	:"=r"(switch_val));
}

void distance_calc()
{
  float distance;
  int buzzer_reg;
  distance= pulse_counter* 0.0344 *0.5;
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

bs.o:     file format elf32-littleriscv


Disassembly of section .text:

00010074 <register_fini>:
   10074:	ffff0797          	auipc	a5,0xffff0
   10078:	f8c78793          	addi	a5,a5,-116 # 0 <register_fini-0x10074>
   1007c:	00078863          	beqz	a5,1008c <register_fini+0x18>
   10080:	00001517          	auipc	a0,0x1
   10084:	ba450513          	addi	a0,a0,-1116 # 10c24 <__libc_fini_array>
   10088:	3550006f          	j	10bdc <atexit>
   1008c:	00008067          	ret

00010090 <_start>:
   10090:	00003197          	auipc	gp,0x3
   10094:	8b818193          	addi	gp,gp,-1864 # 12948 <__global_pointer$>
   10098:	c4818513          	addi	a0,gp,-952 # 12590 <_edata>
   1009c:	c7818613          	addi	a2,gp,-904 # 125c0 <__BSS_END__>
   100a0:	40a60633          	sub	a2,a2,a0
   100a4:	00000593          	li	a1,0
   100a8:	471000ef          	jal	ra,10d18 <memset>
   100ac:	00001517          	auipc	a0,0x1
   100b0:	b7850513          	addi	a0,a0,-1160 # 10c24 <__libc_fini_array>
   100b4:	329000ef          	jal	ra,10bdc <atexit>
   100b8:	3cd000ef          	jal	ra,10c84 <__libc_init_array>
   100bc:	00012503          	lw	a0,0(sp)
   100c0:	00410593          	addi	a1,sp,4
   100c4:	00000613          	li	a2,0
   100c8:	12c000ef          	jal	ra,101f4 <main>
   100cc:	3250006f          	j	10bf0 <exit>

000100d0 <__do_global_dtors_aux>:
   100d0:	c501c783          	lbu	a5,-944(gp) # 12598 <completed.5434>
   100d4:	04079463          	bnez	a5,1011c <__do_global_dtors_aux+0x4c>
   100d8:	ffff0797          	auipc	a5,0xffff0
   100dc:	f2878793          	addi	a5,a5,-216 # 0 <register_fini-0x10074>
   100e0:	02078863          	beqz	a5,10110 <__do_global_dtors_aux+0x40>
   100e4:	ff010113          	addi	sp,sp,-16
   100e8:	00002517          	auipc	a0,0x2
   100ec:	05050513          	addi	a0,a0,80 # 12138 <__FRAME_END__>
   100f0:	00112623          	sw	ra,12(sp)
   100f4:	00000097          	auipc	ra,0x0
   100f8:	000000e7          	jalr	zero # 0 <register_fini-0x10074>
   100fc:	00c12083          	lw	ra,12(sp)
   10100:	00100793          	li	a5,1
   10104:	c4f18823          	sb	a5,-944(gp) # 12598 <completed.5434>
   10108:	01010113          	addi	sp,sp,16
   1010c:	00008067          	ret
   10110:	00100793          	li	a5,1
   10114:	c4f18823          	sb	a5,-944(gp) # 12598 <completed.5434>
   10118:	00008067          	ret
   1011c:	00008067          	ret

00010120 <frame_dummy>:
   10120:	ffff0797          	auipc	a5,0xffff0
   10124:	ee078793          	addi	a5,a5,-288 # 0 <register_fini-0x10074>
   10128:	00078c63          	beqz	a5,10140 <frame_dummy+0x20>
   1012c:	c5418593          	addi	a1,gp,-940 # 1259c <object.5439>
   10130:	00002517          	auipc	a0,0x2
   10134:	00850513          	addi	a0,a0,8 # 12138 <__FRAME_END__>
   10138:	00000317          	auipc	t1,0x0
   1013c:	00000067          	jr	zero # 0 <register_fini-0x10074>
   10140:	00008067          	ret

00010144 <trigger_send>:
   10144:	00ff6f33          	or	t5,t5,a5
   10148:	c6f1aa23          	sw	a5,-908(gp) # 125bc <switch_val>
   1014c:	c601a823          	sw	zero,-912(gp) # 125b8 <trigger>
   10150:	00008067          	ret

00010154 <distance_calc>:
   10154:	ff010113          	addi	sp,sp,-16
   10158:	00112623          	sw	ra,12(sp)
   1015c:	c481a503          	lw	a0,-952(gp) # 12590 <_edata>
   10160:	72c000ef          	jal	ra,1088c <__floatsidf>
   10164:	c281a603          	lw	a2,-984(gp) # 12570 <__SDATA_BEGIN__>
   10168:	c2c1a683          	lw	a3,-980(gp) # 12574 <__SDATA_BEGIN__+0x4>
   1016c:	0b0000ef          	jal	ra,1021c <__muldf3>
   10170:	c301a603          	lw	a2,-976(gp) # 12578 <__SDATA_BEGIN__+0x8>
   10174:	c341a683          	lw	a3,-972(gp) # 1257c <__SDATA_BEGIN__+0xc>
   10178:	0a4000ef          	jal	ra,1021c <__muldf3>
   1017c:	071000ef          	jal	ra,109ec <__truncdfsf2>
   10180:	c381a583          	lw	a1,-968(gp) # 12580 <__SDATA_BEGIN__+0x10>
   10184:	7b4000ef          	jal	ra,10938 <__gesf2>
   10188:	00a02533          	sgtz	a0,a0
   1018c:	c4a1a623          	sw	a0,-948(gp) # 12594 <buzzer>
   10190:	00c12083          	lw	ra,12(sp)
   10194:	01010113          	addi	sp,sp,16
   10198:	00008067          	ret

0001019c <pulse_count>:
   1019c:	ff010113          	addi	sp,sp,-16
   101a0:	00112623          	sw	ra,12(sp)
   101a4:	fa1ff0ef          	jal	ra,10144 <trigger_send>
   101a8:	002f7793          	andi	a5,t5,2
   101ac:	c6f1a623          	sw	a5,-916(gp) # 125b4 <echo>
   101b0:	00078463          	beqz	a5,101b8 <pulse_count+0x1c>
   101b4:	0000006f          	j	101b4 <pulse_count+0x18>
   101b8:	f9dff0ef          	jal	ra,10154 <distance_calc>
   101bc:	00c12083          	lw	ra,12(sp)
   101c0:	01010113          	addi	sp,sp,16
   101c4:	00008067          	ret

000101c8 <read>:
   101c8:	001f7793          	andi	a5,t5,1
   101cc:	c6f1aa23          	sw	a5,-908(gp) # 125bc <switch_val>
   101d0:	00079463          	bnez	a5,101d8 <read+0x10>
   101d4:	00008067          	ret
   101d8:	ff010113          	addi	sp,sp,-16
   101dc:	00112623          	sw	ra,12(sp)
   101e0:	fbdff0ef          	jal	ra,1019c <pulse_count>
   101e4:	c401a423          	sw	zero,-952(gp) # 12590 <_edata>
   101e8:	00c12083          	lw	ra,12(sp)
   101ec:	01010113          	addi	sp,sp,16
   101f0:	00008067          	ret

000101f4 <main>:
   101f4:	ff010113          	addi	sp,sp,-16
   101f8:	00112623          	sw	ra,12(sp)
   101fc:	fcdff0ef          	jal	ra,101c8 <read>
   10200:	ffdff06f          	j	101fc <main+0x8>

00010204 <delay_10us>:
   10204:	00008067          	ret

00010208 <delay_100ms>:
   10208:	000187b7          	lui	a5,0x18
   1020c:	6a078793          	addi	a5,a5,1696 # 186a0 <__global_pointer$+0x5d58>
   10210:	fff78793          	addi	a5,a5,-1
   10214:	fe079ee3          	bnez	a5,10210 <delay_100ms+0x8>
   10218:	00008067          	ret

0001021c <__muldf3>:
   1021c:	fd010113          	addi	sp,sp,-48
   10220:	01312e23          	sw	s3,28(sp)
   10224:	0145d993          	srli	s3,a1,0x14
   10228:	02812423          	sw	s0,40(sp)
   1022c:	02912223          	sw	s1,36(sp)
   10230:	01412c23          	sw	s4,24(sp)
   10234:	01512a23          	sw	s5,20(sp)
   10238:	01612823          	sw	s6,16(sp)
   1023c:	00c59493          	slli	s1,a1,0xc
   10240:	02112623          	sw	ra,44(sp)
   10244:	03212023          	sw	s2,32(sp)
   10248:	01712623          	sw	s7,12(sp)
   1024c:	7ff9f993          	andi	s3,s3,2047
   10250:	00050413          	mv	s0,a0
   10254:	00060b13          	mv	s6,a2
   10258:	00068a93          	mv	s5,a3
   1025c:	00c4d493          	srli	s1,s1,0xc
   10260:	01f5da13          	srli	s4,a1,0x1f
   10264:	0a098463          	beqz	s3,1030c <__muldf3+0xf0>
   10268:	7ff00793          	li	a5,2047
   1026c:	10f98263          	beq	s3,a5,10370 <__muldf3+0x154>
   10270:	01d55793          	srli	a5,a0,0x1d
   10274:	00349493          	slli	s1,s1,0x3
   10278:	0097e4b3          	or	s1,a5,s1
   1027c:	008007b7          	lui	a5,0x800
   10280:	00f4e4b3          	or	s1,s1,a5
   10284:	00351913          	slli	s2,a0,0x3
   10288:	c0198993          	addi	s3,s3,-1023
   1028c:	00000b93          	li	s7,0
   10290:	014ad513          	srli	a0,s5,0x14
   10294:	00ca9413          	slli	s0,s5,0xc
   10298:	7ff57513          	andi	a0,a0,2047
   1029c:	00c45413          	srli	s0,s0,0xc
   102a0:	01fada93          	srli	s5,s5,0x1f
   102a4:	10050263          	beqz	a0,103a8 <__muldf3+0x18c>
   102a8:	7ff00793          	li	a5,2047
   102ac:	16f50263          	beq	a0,a5,10410 <__muldf3+0x1f4>
   102b0:	01db5793          	srli	a5,s6,0x1d
   102b4:	00341413          	slli	s0,s0,0x3
   102b8:	0087e433          	or	s0,a5,s0
   102bc:	008007b7          	lui	a5,0x800
   102c0:	00f46433          	or	s0,s0,a5
   102c4:	c0150513          	addi	a0,a0,-1023
   102c8:	003b1793          	slli	a5,s6,0x3
   102cc:	00000713          	li	a4,0
   102d0:	002b9693          	slli	a3,s7,0x2
   102d4:	00e6e6b3          	or	a3,a3,a4
   102d8:	00a989b3          	add	s3,s3,a0
   102dc:	fff68693          	addi	a3,a3,-1
   102e0:	00e00613          	li	a2,14
   102e4:	015a4833          	xor	a6,s4,s5
   102e8:	00198893          	addi	a7,s3,1
   102ec:	14d66e63          	bltu	a2,a3,10448 <__muldf3+0x22c>
   102f0:	00001617          	auipc	a2,0x1
   102f4:	d0c60613          	addi	a2,a2,-756 # 10ffc <__errno+0xc>
   102f8:	00269693          	slli	a3,a3,0x2
   102fc:	00c686b3          	add	a3,a3,a2
   10300:	0006a683          	lw	a3,0(a3)
   10304:	00c686b3          	add	a3,a3,a2
   10308:	00068067          	jr	a3
   1030c:	00a4e933          	or	s2,s1,a0
   10310:	06090c63          	beqz	s2,10388 <__muldf3+0x16c>
   10314:	04048063          	beqz	s1,10354 <__muldf3+0x138>
   10318:	00048513          	mv	a0,s1
   1031c:	075000ef          	jal	ra,10b90 <__clzsi2>
   10320:	ff550713          	addi	a4,a0,-11
   10324:	01c00793          	li	a5,28
   10328:	02e7cc63          	blt	a5,a4,10360 <__muldf3+0x144>
   1032c:	01d00793          	li	a5,29
   10330:	ff850913          	addi	s2,a0,-8
   10334:	40e787b3          	sub	a5,a5,a4
   10338:	012494b3          	sll	s1,s1,s2
   1033c:	00f457b3          	srl	a5,s0,a5
   10340:	0097e4b3          	or	s1,a5,s1
   10344:	01241933          	sll	s2,s0,s2
   10348:	c0d00993          	li	s3,-1011
   1034c:	40a989b3          	sub	s3,s3,a0
   10350:	f3dff06f          	j	1028c <__muldf3+0x70>
   10354:	03d000ef          	jal	ra,10b90 <__clzsi2>
   10358:	02050513          	addi	a0,a0,32
   1035c:	fc5ff06f          	j	10320 <__muldf3+0x104>
   10360:	fd850493          	addi	s1,a0,-40
   10364:	009414b3          	sll	s1,s0,s1
   10368:	00000913          	li	s2,0
   1036c:	fddff06f          	j	10348 <__muldf3+0x12c>
   10370:	00a4e933          	or	s2,s1,a0
   10374:	02090263          	beqz	s2,10398 <__muldf3+0x17c>
   10378:	00050913          	mv	s2,a0
   1037c:	7ff00993          	li	s3,2047
   10380:	00300b93          	li	s7,3
   10384:	f0dff06f          	j	10290 <__muldf3+0x74>
   10388:	00000493          	li	s1,0
   1038c:	00000993          	li	s3,0
   10390:	00100b93          	li	s7,1
   10394:	efdff06f          	j	10290 <__muldf3+0x74>
   10398:	00000493          	li	s1,0
   1039c:	7ff00993          	li	s3,2047
   103a0:	00200b93          	li	s7,2
   103a4:	eedff06f          	j	10290 <__muldf3+0x74>
   103a8:	016467b3          	or	a5,s0,s6
   103ac:	06078e63          	beqz	a5,10428 <__muldf3+0x20c>
   103b0:	04040063          	beqz	s0,103f0 <__muldf3+0x1d4>
   103b4:	00040513          	mv	a0,s0
   103b8:	7d8000ef          	jal	ra,10b90 <__clzsi2>
   103bc:	ff550693          	addi	a3,a0,-11
   103c0:	01c00793          	li	a5,28
   103c4:	02d7ce63          	blt	a5,a3,10400 <__muldf3+0x1e4>
   103c8:	01d00713          	li	a4,29
   103cc:	ff850793          	addi	a5,a0,-8
   103d0:	40d70733          	sub	a4,a4,a3
   103d4:	00f41433          	sll	s0,s0,a5
   103d8:	00eb5733          	srl	a4,s6,a4
   103dc:	00876433          	or	s0,a4,s0
   103e0:	00fb17b3          	sll	a5,s6,a5
   103e4:	c0d00713          	li	a4,-1011
   103e8:	40a70533          	sub	a0,a4,a0
   103ec:	ee1ff06f          	j	102cc <__muldf3+0xb0>
   103f0:	000b0513          	mv	a0,s6
   103f4:	79c000ef          	jal	ra,10b90 <__clzsi2>
   103f8:	02050513          	addi	a0,a0,32
   103fc:	fc1ff06f          	j	103bc <__muldf3+0x1a0>
   10400:	fd850413          	addi	s0,a0,-40
   10404:	008b1433          	sll	s0,s6,s0
   10408:	00000793          	li	a5,0
   1040c:	fd9ff06f          	j	103e4 <__muldf3+0x1c8>
   10410:	016467b3          	or	a5,s0,s6
   10414:	02078263          	beqz	a5,10438 <__muldf3+0x21c>
   10418:	000b0793          	mv	a5,s6
   1041c:	7ff00513          	li	a0,2047
   10420:	00300713          	li	a4,3
   10424:	eadff06f          	j	102d0 <__muldf3+0xb4>
   10428:	00000413          	li	s0,0
   1042c:	00000513          	li	a0,0
   10430:	00100713          	li	a4,1
   10434:	e9dff06f          	j	102d0 <__muldf3+0xb4>
   10438:	00000413          	li	s0,0
   1043c:	7ff00513          	li	a0,2047
   10440:	00200713          	li	a4,2
   10444:	e8dff06f          	j	102d0 <__muldf3+0xb4>
   10448:	000102b7          	lui	t0,0x10
   1044c:	fff28313          	addi	t1,t0,-1 # ffff <register_fini-0x75>
   10450:	01095f13          	srli	t5,s2,0x10
   10454:	0107df93          	srli	t6,a5,0x10
   10458:	00697933          	and	s2,s2,t1
   1045c:	0067f7b3          	and	a5,a5,t1
   10460:	00090513          	mv	a0,s2
   10464:	00078593          	mv	a1,a5
   10468:	704000ef          	jal	ra,10b6c <__mulsi3>
   1046c:	00050e93          	mv	t4,a0
   10470:	000f8593          	mv	a1,t6
   10474:	00090513          	mv	a0,s2
   10478:	6f4000ef          	jal	ra,10b6c <__mulsi3>
   1047c:	00050e13          	mv	t3,a0
   10480:	00078593          	mv	a1,a5
   10484:	000f0513          	mv	a0,t5
   10488:	6e4000ef          	jal	ra,10b6c <__mulsi3>
   1048c:	00050a13          	mv	s4,a0
   10490:	000f8593          	mv	a1,t6
   10494:	000f0513          	mv	a0,t5
   10498:	6d4000ef          	jal	ra,10b6c <__mulsi3>
   1049c:	010ed713          	srli	a4,t4,0x10
   104a0:	014e0e33          	add	t3,t3,s4
   104a4:	01c70733          	add	a4,a4,t3
   104a8:	00050393          	mv	t2,a0
   104ac:	01477463          	bgeu	a4,s4,104b4 <__muldf3+0x298>
   104b0:	005503b3          	add	t2,a0,t0
   104b4:	00677e33          	and	t3,a4,t1
   104b8:	006efeb3          	and	t4,t4,t1
   104bc:	01045a13          	srli	s4,s0,0x10
   104c0:	010e1e13          	slli	t3,t3,0x10
   104c4:	00647433          	and	s0,s0,t1
   104c8:	01075293          	srli	t0,a4,0x10
   104cc:	01de0e33          	add	t3,t3,t4
   104d0:	00090513          	mv	a0,s2
   104d4:	00040593          	mv	a1,s0
   104d8:	694000ef          	jal	ra,10b6c <__mulsi3>
   104dc:	00050e93          	mv	t4,a0
   104e0:	000a0593          	mv	a1,s4
   104e4:	00090513          	mv	a0,s2
   104e8:	684000ef          	jal	ra,10b6c <__mulsi3>
   104ec:	00050713          	mv	a4,a0
   104f0:	00040593          	mv	a1,s0
   104f4:	000f0513          	mv	a0,t5
   104f8:	674000ef          	jal	ra,10b6c <__mulsi3>
   104fc:	00050313          	mv	t1,a0
   10500:	000a0593          	mv	a1,s4
   10504:	000f0513          	mv	a0,t5
   10508:	664000ef          	jal	ra,10b6c <__mulsi3>
   1050c:	010ed693          	srli	a3,t4,0x10
   10510:	00670733          	add	a4,a4,t1
   10514:	00e686b3          	add	a3,a3,a4
   10518:	0066f663          	bgeu	a3,t1,10524 <__muldf3+0x308>
   1051c:	00010737          	lui	a4,0x10
   10520:	00e50533          	add	a0,a0,a4
   10524:	00010ab7          	lui	s5,0x10
   10528:	fffa8613          	addi	a2,s5,-1 # ffff <register_fini-0x75>
   1052c:	0106d713          	srli	a4,a3,0x10
   10530:	00c6f6b3          	and	a3,a3,a2
   10534:	01069693          	slli	a3,a3,0x10
   10538:	00cefeb3          	and	t4,t4,a2
   1053c:	00a70f33          	add	t5,a4,a0
   10540:	01d68eb3          	add	t4,a3,t4
   10544:	0104d713          	srli	a4,s1,0x10
   10548:	00c4f4b3          	and	s1,s1,a2
   1054c:	01d282b3          	add	t0,t0,t4
   10550:	00048513          	mv	a0,s1
   10554:	00078593          	mv	a1,a5
   10558:	614000ef          	jal	ra,10b6c <__mulsi3>
   1055c:	00050913          	mv	s2,a0
   10560:	000f8593          	mv	a1,t6
   10564:	00048513          	mv	a0,s1
   10568:	604000ef          	jal	ra,10b6c <__mulsi3>
   1056c:	00050313          	mv	t1,a0
   10570:	00078593          	mv	a1,a5
   10574:	00070513          	mv	a0,a4
   10578:	5f4000ef          	jal	ra,10b6c <__mulsi3>
   1057c:	00050b13          	mv	s6,a0
   10580:	000f8593          	mv	a1,t6
   10584:	00070513          	mv	a0,a4
   10588:	5e4000ef          	jal	ra,10b6c <__mulsi3>
   1058c:	01095793          	srli	a5,s2,0x10
   10590:	01630333          	add	t1,t1,s6
   10594:	006787b3          	add	a5,a5,t1
   10598:	0167f463          	bgeu	a5,s6,105a0 <__muldf3+0x384>
   1059c:	01550533          	add	a0,a0,s5
   105a0:	00010ab7          	lui	s5,0x10
   105a4:	fffa8693          	addi	a3,s5,-1 # ffff <register_fini-0x75>
   105a8:	00d7f333          	and	t1,a5,a3
   105ac:	0107d613          	srli	a2,a5,0x10
   105b0:	00d97933          	and	s2,s2,a3
   105b4:	01031313          	slli	t1,t1,0x10
   105b8:	00a60fb3          	add	t6,a2,a0
   105bc:	01230333          	add	t1,t1,s2
   105c0:	00048513          	mv	a0,s1
   105c4:	00040593          	mv	a1,s0
   105c8:	5a4000ef          	jal	ra,10b6c <__mulsi3>
   105cc:	00050793          	mv	a5,a0
   105d0:	000a0593          	mv	a1,s4
   105d4:	00048513          	mv	a0,s1
   105d8:	594000ef          	jal	ra,10b6c <__mulsi3>
   105dc:	00050493          	mv	s1,a0
   105e0:	00040593          	mv	a1,s0
   105e4:	00070513          	mv	a0,a4
   105e8:	584000ef          	jal	ra,10b6c <__mulsi3>
   105ec:	00050913          	mv	s2,a0
   105f0:	000a0593          	mv	a1,s4
   105f4:	00070513          	mv	a0,a4
   105f8:	574000ef          	jal	ra,10b6c <__mulsi3>
   105fc:	0107d693          	srli	a3,a5,0x10
   10600:	012484b3          	add	s1,s1,s2
   10604:	009686b3          	add	a3,a3,s1
   10608:	0126f463          	bgeu	a3,s2,10610 <__muldf3+0x3f4>
   1060c:	01550533          	add	a0,a0,s5
   10610:	00010637          	lui	a2,0x10
   10614:	fff60613          	addi	a2,a2,-1 # ffff <register_fini-0x75>
   10618:	00c6f733          	and	a4,a3,a2
   1061c:	00c7f7b3          	and	a5,a5,a2
   10620:	01071713          	slli	a4,a4,0x10
   10624:	007282b3          	add	t0,t0,t2
   10628:	00f70733          	add	a4,a4,a5
   1062c:	01d2beb3          	sltu	t4,t0,t4
   10630:	01e70733          	add	a4,a4,t5
   10634:	01d70433          	add	s0,a4,t4
   10638:	006282b3          	add	t0,t0,t1
   1063c:	01f40633          	add	a2,s0,t6
   10640:	0062b333          	sltu	t1,t0,t1
   10644:	006605b3          	add	a1,a2,t1
   10648:	01e73733          	sltu	a4,a4,t5
   1064c:	01d43433          	sltu	s0,s0,t4
   10650:	00876433          	or	s0,a4,s0
   10654:	0106d693          	srli	a3,a3,0x10
   10658:	01f63633          	sltu	a2,a2,t6
   1065c:	0065b333          	sltu	t1,a1,t1
   10660:	00d40433          	add	s0,s0,a3
   10664:	00666333          	or	t1,a2,t1
   10668:	00640433          	add	s0,s0,t1
   1066c:	00929793          	slli	a5,t0,0x9
   10670:	00a40433          	add	s0,s0,a0
   10674:	01c7e7b3          	or	a5,a5,t3
   10678:	00941413          	slli	s0,s0,0x9
   1067c:	0175d513          	srli	a0,a1,0x17
   10680:	00f037b3          	snez	a5,a5
   10684:	0172de13          	srli	t3,t0,0x17
   10688:	00959713          	slli	a4,a1,0x9
   1068c:	00a46433          	or	s0,s0,a0
   10690:	01c7e7b3          	or	a5,a5,t3
   10694:	00e7e7b3          	or	a5,a5,a4
   10698:	00741713          	slli	a4,s0,0x7
   1069c:	10075263          	bgez	a4,107a0 <__muldf3+0x584>
   106a0:	0017d713          	srli	a4,a5,0x1
   106a4:	0017f793          	andi	a5,a5,1
   106a8:	00f767b3          	or	a5,a4,a5
   106ac:	01f41713          	slli	a4,s0,0x1f
   106b0:	00e7e7b3          	or	a5,a5,a4
   106b4:	00145413          	srli	s0,s0,0x1
   106b8:	3ff88693          	addi	a3,a7,1023
   106bc:	0ed05663          	blez	a3,107a8 <__muldf3+0x58c>
   106c0:	0077f713          	andi	a4,a5,7
   106c4:	02070063          	beqz	a4,106e4 <__muldf3+0x4c8>
   106c8:	00f7f713          	andi	a4,a5,15
   106cc:	00400613          	li	a2,4
   106d0:	00c70a63          	beq	a4,a2,106e4 <__muldf3+0x4c8>
   106d4:	00478713          	addi	a4,a5,4 # 800004 <__global_pointer$+0x7ed6bc>
   106d8:	00f737b3          	sltu	a5,a4,a5
   106dc:	00f40433          	add	s0,s0,a5
   106e0:	00070793          	mv	a5,a4
   106e4:	00741713          	slli	a4,s0,0x7
   106e8:	00075a63          	bgez	a4,106fc <__muldf3+0x4e0>
   106ec:	ff000737          	lui	a4,0xff000
   106f0:	fff70713          	addi	a4,a4,-1 # feffffff <__global_pointer$+0xfefed6b7>
   106f4:	00e47433          	and	s0,s0,a4
   106f8:	40088693          	addi	a3,a7,1024
   106fc:	7fe00713          	li	a4,2046
   10700:	16d74663          	blt	a4,a3,1086c <__muldf3+0x650>
   10704:	0037d713          	srli	a4,a5,0x3
   10708:	01d41793          	slli	a5,s0,0x1d
   1070c:	00e7e7b3          	or	a5,a5,a4
   10710:	00345413          	srli	s0,s0,0x3
   10714:	00c41413          	slli	s0,s0,0xc
   10718:	7ff6f713          	andi	a4,a3,2047
   1071c:	01471713          	slli	a4,a4,0x14
   10720:	00c45413          	srli	s0,s0,0xc
   10724:	00e46433          	or	s0,s0,a4
   10728:	01f81813          	slli	a6,a6,0x1f
   1072c:	01046733          	or	a4,s0,a6
   10730:	02c12083          	lw	ra,44(sp)
   10734:	02812403          	lw	s0,40(sp)
   10738:	02412483          	lw	s1,36(sp)
   1073c:	02012903          	lw	s2,32(sp)
   10740:	01c12983          	lw	s3,28(sp)
   10744:	01812a03          	lw	s4,24(sp)
   10748:	01412a83          	lw	s5,20(sp)
   1074c:	01012b03          	lw	s6,16(sp)
   10750:	00c12b83          	lw	s7,12(sp)
   10754:	00078513          	mv	a0,a5
   10758:	00070593          	mv	a1,a4
   1075c:	03010113          	addi	sp,sp,48
   10760:	00008067          	ret
   10764:	000a0813          	mv	a6,s4
   10768:	00048413          	mv	s0,s1
   1076c:	00090793          	mv	a5,s2
   10770:	000b8713          	mv	a4,s7
   10774:	00200693          	li	a3,2
   10778:	0ed70a63          	beq	a4,a3,1086c <__muldf3+0x650>
   1077c:	00300693          	li	a3,3
   10780:	0cd70c63          	beq	a4,a3,10858 <__muldf3+0x63c>
   10784:	00100693          	li	a3,1
   10788:	f2d718e3          	bne	a4,a3,106b8 <__muldf3+0x49c>
   1078c:	00000413          	li	s0,0
   10790:	00000793          	li	a5,0
   10794:	0880006f          	j	1081c <__muldf3+0x600>
   10798:	000a8813          	mv	a6,s5
   1079c:	fd9ff06f          	j	10774 <__muldf3+0x558>
   107a0:	00098893          	mv	a7,s3
   107a4:	f15ff06f          	j	106b8 <__muldf3+0x49c>
   107a8:	00100613          	li	a2,1
   107ac:	40d60633          	sub	a2,a2,a3
   107b0:	03800713          	li	a4,56
   107b4:	fcc74ce3          	blt	a4,a2,1078c <__muldf3+0x570>
   107b8:	01f00713          	li	a4,31
   107bc:	06c74463          	blt	a4,a2,10824 <__muldf3+0x608>
   107c0:	41e88893          	addi	a7,a7,1054
   107c4:	01141733          	sll	a4,s0,a7
   107c8:	00c7d6b3          	srl	a3,a5,a2
   107cc:	011797b3          	sll	a5,a5,a7
   107d0:	00d76733          	or	a4,a4,a3
   107d4:	00f037b3          	snez	a5,a5
   107d8:	00f767b3          	or	a5,a4,a5
   107dc:	00c45433          	srl	s0,s0,a2
   107e0:	0077f713          	andi	a4,a5,7
   107e4:	02070063          	beqz	a4,10804 <__muldf3+0x5e8>
   107e8:	00f7f713          	andi	a4,a5,15
   107ec:	00400693          	li	a3,4
   107f0:	00d70a63          	beq	a4,a3,10804 <__muldf3+0x5e8>
   107f4:	00478713          	addi	a4,a5,4
   107f8:	00f737b3          	sltu	a5,a4,a5
   107fc:	00f40433          	add	s0,s0,a5
   10800:	00070793          	mv	a5,a4
   10804:	00841713          	slli	a4,s0,0x8
   10808:	06074a63          	bltz	a4,1087c <__muldf3+0x660>
   1080c:	01d41713          	slli	a4,s0,0x1d
   10810:	0037d793          	srli	a5,a5,0x3
   10814:	00f767b3          	or	a5,a4,a5
   10818:	00345413          	srli	s0,s0,0x3
   1081c:	00000693          	li	a3,0
   10820:	ef5ff06f          	j	10714 <__muldf3+0x4f8>
   10824:	fe100713          	li	a4,-31
   10828:	40d70733          	sub	a4,a4,a3
   1082c:	02000593          	li	a1,32
   10830:	00e45733          	srl	a4,s0,a4
   10834:	00000693          	li	a3,0
   10838:	00b60663          	beq	a2,a1,10844 <__muldf3+0x628>
   1083c:	43e88893          	addi	a7,a7,1086
   10840:	011416b3          	sll	a3,s0,a7
   10844:	00f6e7b3          	or	a5,a3,a5
   10848:	00f037b3          	snez	a5,a5
   1084c:	00f767b3          	or	a5,a4,a5
   10850:	00000413          	li	s0,0
   10854:	f8dff06f          	j	107e0 <__muldf3+0x5c4>
   10858:	00080437          	lui	s0,0x80
   1085c:	00000793          	li	a5,0
   10860:	7ff00693          	li	a3,2047
   10864:	00000813          	li	a6,0
   10868:	eadff06f          	j	10714 <__muldf3+0x4f8>
   1086c:	00000413          	li	s0,0
   10870:	00000793          	li	a5,0
   10874:	7ff00693          	li	a3,2047
   10878:	e9dff06f          	j	10714 <__muldf3+0x4f8>
   1087c:	00000413          	li	s0,0
   10880:	00000793          	li	a5,0
   10884:	00100693          	li	a3,1
   10888:	e8dff06f          	j	10714 <__muldf3+0x4f8>

0001088c <__floatsidf>:
   1088c:	ff010113          	addi	sp,sp,-16
   10890:	00112623          	sw	ra,12(sp)
   10894:	00812423          	sw	s0,8(sp)
   10898:	00912223          	sw	s1,4(sp)
   1089c:	08050863          	beqz	a0,1092c <__floatsidf+0xa0>
   108a0:	41f55793          	srai	a5,a0,0x1f
   108a4:	00a7c433          	xor	s0,a5,a0
   108a8:	40f40433          	sub	s0,s0,a5
   108ac:	01f55493          	srli	s1,a0,0x1f
   108b0:	00040513          	mv	a0,s0
   108b4:	2dc000ef          	jal	ra,10b90 <__clzsi2>
   108b8:	41e00713          	li	a4,1054
   108bc:	00a00793          	li	a5,10
   108c0:	40a70733          	sub	a4,a4,a0
   108c4:	04a7ca63          	blt	a5,a0,10918 <__floatsidf+0x8c>
   108c8:	00b00793          	li	a5,11
   108cc:	40a787b3          	sub	a5,a5,a0
   108d0:	01550513          	addi	a0,a0,21
   108d4:	00f457b3          	srl	a5,s0,a5
   108d8:	00a41433          	sll	s0,s0,a0
   108dc:	00048513          	mv	a0,s1
   108e0:	00c79793          	slli	a5,a5,0xc
   108e4:	7ff77713          	andi	a4,a4,2047
   108e8:	01471713          	slli	a4,a4,0x14
   108ec:	00c7d793          	srli	a5,a5,0xc
   108f0:	01f51513          	slli	a0,a0,0x1f
   108f4:	00e7e7b3          	or	a5,a5,a4
   108f8:	00a7e733          	or	a4,a5,a0
   108fc:	00c12083          	lw	ra,12(sp)
   10900:	00040513          	mv	a0,s0
   10904:	00812403          	lw	s0,8(sp)
   10908:	00412483          	lw	s1,4(sp)
   1090c:	00070593          	mv	a1,a4
   10910:	01010113          	addi	sp,sp,16
   10914:	00008067          	ret
   10918:	ff550513          	addi	a0,a0,-11
   1091c:	00a417b3          	sll	a5,s0,a0
   10920:	00048513          	mv	a0,s1
   10924:	00000413          	li	s0,0
   10928:	fb9ff06f          	j	108e0 <__floatsidf+0x54>
   1092c:	00000713          	li	a4,0
   10930:	00000793          	li	a5,0
   10934:	ff1ff06f          	j	10924 <__floatsidf+0x98>

00010938 <__gesf2>:
   10938:	01755693          	srli	a3,a0,0x17
   1093c:	008007b7          	lui	a5,0x800
   10940:	fff78793          	addi	a5,a5,-1 # 7fffff <__global_pointer$+0x7ed6b7>
   10944:	0175d713          	srli	a4,a1,0x17
   10948:	0ff6f693          	andi	a3,a3,255
   1094c:	0ff00813          	li	a6,255
   10950:	00a7f633          	and	a2,a5,a0
   10954:	0ff77713          	andi	a4,a4,255
   10958:	00b7f7b3          	and	a5,a5,a1
   1095c:	01f55513          	srli	a0,a0,0x1f
   10960:	01f5d593          	srli	a1,a1,0x1f
   10964:	01069863          	bne	a3,a6,10974 <__gesf2+0x3c>
   10968:	06060a63          	beqz	a2,109dc <__gesf2+0xa4>
   1096c:	ffe00513          	li	a0,-2
   10970:	00008067          	ret
   10974:	01071463          	bne	a4,a6,1097c <__gesf2+0x44>
   10978:	fe079ae3          	bnez	a5,1096c <__gesf2+0x34>
   1097c:	06069263          	bnez	a3,109e0 <__gesf2+0xa8>
   10980:	00071463          	bnez	a4,10988 <__gesf2+0x50>
   10984:	04078863          	beqz	a5,109d4 <__gesf2+0x9c>
   10988:	00060c63          	beqz	a2,109a0 <__gesf2+0x68>
   1098c:	00b51463          	bne	a0,a1,10994 <__gesf2+0x5c>
   10990:	02d75063          	bge	a4,a3,109b0 <__gesf2+0x78>
   10994:	02050c63          	beqz	a0,109cc <__gesf2+0x94>
   10998:	fff00513          	li	a0,-1
   1099c:	00008067          	ret
   109a0:	fff00513          	li	a0,-1
   109a4:	00058a63          	beqz	a1,109b8 <__gesf2+0x80>
   109a8:	00058513          	mv	a0,a1
   109ac:	00008067          	ret
   109b0:	00e6d663          	bge	a3,a4,109bc <__gesf2+0x84>
   109b4:	fe0502e3          	beqz	a0,10998 <__gesf2+0x60>
   109b8:	00008067          	ret
   109bc:	fcc7ece3          	bltu	a5,a2,10994 <__gesf2+0x5c>
   109c0:	fef66ae3          	bltu	a2,a5,109b4 <__gesf2+0x7c>
   109c4:	00000513          	li	a0,0
   109c8:	00008067          	ret
   109cc:	00100513          	li	a0,1
   109d0:	00008067          	ret
   109d4:	fe0608e3          	beqz	a2,109c4 <__gesf2+0x8c>
   109d8:	fbdff06f          	j	10994 <__gesf2+0x5c>
   109dc:	f8d70ee3          	beq	a4,a3,10978 <__gesf2+0x40>
   109e0:	fa0716e3          	bnez	a4,1098c <__gesf2+0x54>
   109e4:	fa0788e3          	beqz	a5,10994 <__gesf2+0x5c>
   109e8:	fa5ff06f          	j	1098c <__gesf2+0x54>

000109ec <__truncdfsf2>:
   109ec:	00c59613          	slli	a2,a1,0xc
   109f0:	00c65613          	srli	a2,a2,0xc
   109f4:	0145d813          	srli	a6,a1,0x14
   109f8:	00361793          	slli	a5,a2,0x3
   109fc:	7ff87813          	andi	a6,a6,2047
   10a00:	01d55613          	srli	a2,a0,0x1d
   10a04:	00f66633          	or	a2,a2,a5
   10a08:	00180793          	addi	a5,a6,1
   10a0c:	7fe7f793          	andi	a5,a5,2046
   10a10:	01f5d593          	srli	a1,a1,0x1f
   10a14:	00351713          	slli	a4,a0,0x3
   10a18:	0a078663          	beqz	a5,10ac4 <__truncdfsf2+0xd8>
   10a1c:	c8080693          	addi	a3,a6,-896
   10a20:	0fe00793          	li	a5,254
   10a24:	0cd7c263          	blt	a5,a3,10ae8 <__truncdfsf2+0xfc>
   10a28:	08d04063          	bgtz	a3,10aa8 <__truncdfsf2+0xbc>
   10a2c:	fe900793          	li	a5,-23
   10a30:	10f6cc63          	blt	a3,a5,10b48 <__truncdfsf2+0x15c>
   10a34:	008007b7          	lui	a5,0x800
   10a38:	01e00513          	li	a0,30
   10a3c:	00f66633          	or	a2,a2,a5
   10a40:	40d50533          	sub	a0,a0,a3
   10a44:	01f00793          	li	a5,31
   10a48:	02a7c863          	blt	a5,a0,10a78 <__truncdfsf2+0x8c>
   10a4c:	c8280813          	addi	a6,a6,-894
   10a50:	010717b3          	sll	a5,a4,a6
   10a54:	00f037b3          	snez	a5,a5
   10a58:	01061633          	sll	a2,a2,a6
   10a5c:	00a75533          	srl	a0,a4,a0
   10a60:	00c7e7b3          	or	a5,a5,a2
   10a64:	00f567b3          	or	a5,a0,a5
   10a68:	00000693          	li	a3,0
   10a6c:	0077f713          	andi	a4,a5,7
   10a70:	08070063          	beqz	a4,10af0 <__truncdfsf2+0x104>
   10a74:	0dc0006f          	j	10b50 <__truncdfsf2+0x164>
   10a78:	ffe00793          	li	a5,-2
   10a7c:	40d786b3          	sub	a3,a5,a3
   10a80:	02000793          	li	a5,32
   10a84:	00d656b3          	srl	a3,a2,a3
   10a88:	00000893          	li	a7,0
   10a8c:	00f50663          	beq	a0,a5,10a98 <__truncdfsf2+0xac>
   10a90:	ca280813          	addi	a6,a6,-862
   10a94:	010618b3          	sll	a7,a2,a6
   10a98:	00e8e7b3          	or	a5,a7,a4
   10a9c:	00f037b3          	snez	a5,a5
   10aa0:	00f6e7b3          	or	a5,a3,a5
   10aa4:	fc5ff06f          	j	10a68 <__truncdfsf2+0x7c>
   10aa8:	00651513          	slli	a0,a0,0x6
   10aac:	00a03533          	snez	a0,a0
   10ab0:	00361613          	slli	a2,a2,0x3
   10ab4:	01d75793          	srli	a5,a4,0x1d
   10ab8:	00c56633          	or	a2,a0,a2
   10abc:	00f667b3          	or	a5,a2,a5
   10ac0:	fadff06f          	j	10a6c <__truncdfsf2+0x80>
   10ac4:	00e667b3          	or	a5,a2,a4
   10ac8:	00081663          	bnez	a6,10ad4 <__truncdfsf2+0xe8>
   10acc:	00f037b3          	snez	a5,a5
   10ad0:	f99ff06f          	j	10a68 <__truncdfsf2+0x7c>
   10ad4:	0ff00693          	li	a3,255
   10ad8:	00078c63          	beqz	a5,10af0 <__truncdfsf2+0x104>
   10adc:	00361613          	slli	a2,a2,0x3
   10ae0:	020007b7          	lui	a5,0x2000
   10ae4:	fd9ff06f          	j	10abc <__truncdfsf2+0xd0>
   10ae8:	00000793          	li	a5,0
   10aec:	0ff00693          	li	a3,255
   10af0:	00579713          	slli	a4,a5,0x5
   10af4:	00075e63          	bgez	a4,10b10 <__truncdfsf2+0x124>
   10af8:	00168693          	addi	a3,a3,1
   10afc:	0ff00713          	li	a4,255
   10b00:	06e68263          	beq	a3,a4,10b64 <__truncdfsf2+0x178>
   10b04:	fc000737          	lui	a4,0xfc000
   10b08:	fff70713          	addi	a4,a4,-1 # fbffffff <__global_pointer$+0xfbfed6b7>
   10b0c:	00e7f7b3          	and	a5,a5,a4
   10b10:	0ff00713          	li	a4,255
   10b14:	0037d793          	srli	a5,a5,0x3
   10b18:	00e69863          	bne	a3,a4,10b28 <__truncdfsf2+0x13c>
   10b1c:	00078663          	beqz	a5,10b28 <__truncdfsf2+0x13c>
   10b20:	004007b7          	lui	a5,0x400
   10b24:	00000593          	li	a1,0
   10b28:	00979793          	slli	a5,a5,0x9
   10b2c:	0ff6f693          	andi	a3,a3,255
   10b30:	01769693          	slli	a3,a3,0x17
   10b34:	0097d793          	srli	a5,a5,0x9
   10b38:	01f59593          	slli	a1,a1,0x1f
   10b3c:	00d7e7b3          	or	a5,a5,a3
   10b40:	00b7e533          	or	a0,a5,a1
   10b44:	00008067          	ret
   10b48:	00100793          	li	a5,1
   10b4c:	00000693          	li	a3,0
   10b50:	00f7f713          	andi	a4,a5,15
   10b54:	00400613          	li	a2,4
   10b58:	f8c70ce3          	beq	a4,a2,10af0 <__truncdfsf2+0x104>
   10b5c:	00478793          	addi	a5,a5,4 # 400004 <__global_pointer$+0x3ed6bc>
   10b60:	f91ff06f          	j	10af0 <__truncdfsf2+0x104>
   10b64:	00000793          	li	a5,0
   10b68:	fa9ff06f          	j	10b10 <__truncdfsf2+0x124>

00010b6c <__mulsi3>:
   10b6c:	00050613          	mv	a2,a0
   10b70:	00000513          	li	a0,0
   10b74:	0015f693          	andi	a3,a1,1
   10b78:	00068463          	beqz	a3,10b80 <__mulsi3+0x14>
   10b7c:	00c50533          	add	a0,a0,a2
   10b80:	0015d593          	srli	a1,a1,0x1
   10b84:	00161613          	slli	a2,a2,0x1
   10b88:	fe0596e3          	bnez	a1,10b74 <__mulsi3+0x8>
   10b8c:	00008067          	ret

00010b90 <__clzsi2>:
   10b90:	000107b7          	lui	a5,0x10
   10b94:	02f57a63          	bgeu	a0,a5,10bc8 <__clzsi2+0x38>
   10b98:	0ff00793          	li	a5,255
   10b9c:	00a7b7b3          	sltu	a5,a5,a0
   10ba0:	00379793          	slli	a5,a5,0x3
   10ba4:	02000713          	li	a4,32
   10ba8:	40f70733          	sub	a4,a4,a5
   10bac:	00f557b3          	srl	a5,a0,a5
   10bb0:	00000517          	auipc	a0,0x0
   10bb4:	48850513          	addi	a0,a0,1160 # 11038 <__clz_tab>
   10bb8:	00f507b3          	add	a5,a0,a5
   10bbc:	0007c503          	lbu	a0,0(a5) # 10000 <register_fini-0x74>
   10bc0:	40a70533          	sub	a0,a4,a0
   10bc4:	00008067          	ret
   10bc8:	01000737          	lui	a4,0x1000
   10bcc:	01000793          	li	a5,16
   10bd0:	fce56ae3          	bltu	a0,a4,10ba4 <__clzsi2+0x14>
   10bd4:	01800793          	li	a5,24
   10bd8:	fcdff06f          	j	10ba4 <__clzsi2+0x14>

00010bdc <atexit>:
   10bdc:	00050593          	mv	a1,a0
   10be0:	00000693          	li	a3,0
   10be4:	00000613          	li	a2,0
   10be8:	00000513          	li	a0,0
   10bec:	2080006f          	j	10df4 <__register_exitproc>

00010bf0 <exit>:
   10bf0:	ff010113          	addi	sp,sp,-16
   10bf4:	00000593          	li	a1,0
   10bf8:	00812423          	sw	s0,8(sp)
   10bfc:	00112623          	sw	ra,12(sp)
   10c00:	00050413          	mv	s0,a0
   10c04:	28c000ef          	jal	ra,10e90 <__call_exitprocs>
   10c08:	c3c18793          	addi	a5,gp,-964 # 12584 <_global_impure_ptr>
   10c0c:	0007a503          	lw	a0,0(a5)
   10c10:	03c52783          	lw	a5,60(a0)
   10c14:	00078463          	beqz	a5,10c1c <exit+0x2c>
   10c18:	000780e7          	jalr	a5
   10c1c:	00040513          	mv	a0,s0
   10c20:	38c000ef          	jal	ra,10fac <_exit>

00010c24 <__libc_fini_array>:
   10c24:	ff010113          	addi	sp,sp,-16
   10c28:	00812423          	sw	s0,8(sp)
   10c2c:	00001797          	auipc	a5,0x1
   10c30:	51878793          	addi	a5,a5,1304 # 12144 <__init_array_end>
   10c34:	00001417          	auipc	s0,0x1
   10c38:	51440413          	addi	s0,s0,1300 # 12148 <__fini_array_end>
   10c3c:	40f40433          	sub	s0,s0,a5
   10c40:	00112623          	sw	ra,12(sp)
   10c44:	00912223          	sw	s1,4(sp)
   10c48:	40245413          	srai	s0,s0,0x2
   10c4c:	02040263          	beqz	s0,10c70 <__libc_fini_array+0x4c>
   10c50:	00241493          	slli	s1,s0,0x2
   10c54:	ffc48493          	addi	s1,s1,-4
   10c58:	00f484b3          	add	s1,s1,a5
   10c5c:	0004a783          	lw	a5,0(s1)
   10c60:	fff40413          	addi	s0,s0,-1
   10c64:	ffc48493          	addi	s1,s1,-4
   10c68:	000780e7          	jalr	a5
   10c6c:	fe0418e3          	bnez	s0,10c5c <__libc_fini_array+0x38>
   10c70:	00c12083          	lw	ra,12(sp)
   10c74:	00812403          	lw	s0,8(sp)
   10c78:	00412483          	lw	s1,4(sp)
   10c7c:	01010113          	addi	sp,sp,16
   10c80:	00008067          	ret

00010c84 <__libc_init_array>:
   10c84:	ff010113          	addi	sp,sp,-16
   10c88:	00812423          	sw	s0,8(sp)
   10c8c:	01212023          	sw	s2,0(sp)
   10c90:	00001417          	auipc	s0,0x1
   10c94:	4ac40413          	addi	s0,s0,1196 # 1213c <__init_array_start>
   10c98:	00001917          	auipc	s2,0x1
   10c9c:	4a490913          	addi	s2,s2,1188 # 1213c <__init_array_start>
   10ca0:	40890933          	sub	s2,s2,s0
   10ca4:	00112623          	sw	ra,12(sp)
   10ca8:	00912223          	sw	s1,4(sp)
   10cac:	40295913          	srai	s2,s2,0x2
   10cb0:	00090e63          	beqz	s2,10ccc <__libc_init_array+0x48>
   10cb4:	00000493          	li	s1,0
   10cb8:	00042783          	lw	a5,0(s0)
   10cbc:	00148493          	addi	s1,s1,1
   10cc0:	00440413          	addi	s0,s0,4
   10cc4:	000780e7          	jalr	a5
   10cc8:	fe9918e3          	bne	s2,s1,10cb8 <__libc_init_array+0x34>
   10ccc:	00001417          	auipc	s0,0x1
   10cd0:	47040413          	addi	s0,s0,1136 # 1213c <__init_array_start>
   10cd4:	00001917          	auipc	s2,0x1
   10cd8:	47090913          	addi	s2,s2,1136 # 12144 <__init_array_end>
   10cdc:	40890933          	sub	s2,s2,s0
   10ce0:	40295913          	srai	s2,s2,0x2
   10ce4:	00090e63          	beqz	s2,10d00 <__libc_init_array+0x7c>
   10ce8:	00000493          	li	s1,0
   10cec:	00042783          	lw	a5,0(s0)
   10cf0:	00148493          	addi	s1,s1,1
   10cf4:	00440413          	addi	s0,s0,4
   10cf8:	000780e7          	jalr	a5
   10cfc:	fe9918e3          	bne	s2,s1,10cec <__libc_init_array+0x68>
   10d00:	00c12083          	lw	ra,12(sp)
   10d04:	00812403          	lw	s0,8(sp)
   10d08:	00412483          	lw	s1,4(sp)
   10d0c:	00012903          	lw	s2,0(sp)
   10d10:	01010113          	addi	sp,sp,16
   10d14:	00008067          	ret

00010d18 <memset>:
   10d18:	00f00313          	li	t1,15
   10d1c:	00050713          	mv	a4,a0
   10d20:	02c37e63          	bgeu	t1,a2,10d5c <memset+0x44>
   10d24:	00f77793          	andi	a5,a4,15
   10d28:	0a079063          	bnez	a5,10dc8 <memset+0xb0>
   10d2c:	08059263          	bnez	a1,10db0 <memset+0x98>
   10d30:	ff067693          	andi	a3,a2,-16
   10d34:	00f67613          	andi	a2,a2,15
   10d38:	00e686b3          	add	a3,a3,a4
   10d3c:	00b72023          	sw	a1,0(a4) # 1000000 <__global_pointer$+0xfed6b8>
   10d40:	00b72223          	sw	a1,4(a4)
   10d44:	00b72423          	sw	a1,8(a4)
   10d48:	00b72623          	sw	a1,12(a4)
   10d4c:	01070713          	addi	a4,a4,16
   10d50:	fed766e3          	bltu	a4,a3,10d3c <memset+0x24>
   10d54:	00061463          	bnez	a2,10d5c <memset+0x44>
   10d58:	00008067          	ret
   10d5c:	40c306b3          	sub	a3,t1,a2
   10d60:	00269693          	slli	a3,a3,0x2
   10d64:	00000297          	auipc	t0,0x0
   10d68:	005686b3          	add	a3,a3,t0
   10d6c:	00c68067          	jr	12(a3)
   10d70:	00b70723          	sb	a1,14(a4)
   10d74:	00b706a3          	sb	a1,13(a4)
   10d78:	00b70623          	sb	a1,12(a4)
   10d7c:	00b705a3          	sb	a1,11(a4)
   10d80:	00b70523          	sb	a1,10(a4)
   10d84:	00b704a3          	sb	a1,9(a4)
   10d88:	00b70423          	sb	a1,8(a4)
   10d8c:	00b703a3          	sb	a1,7(a4)
   10d90:	00b70323          	sb	a1,6(a4)
   10d94:	00b702a3          	sb	a1,5(a4)
   10d98:	00b70223          	sb	a1,4(a4)
   10d9c:	00b701a3          	sb	a1,3(a4)
   10da0:	00b70123          	sb	a1,2(a4)
   10da4:	00b700a3          	sb	a1,1(a4)
   10da8:	00b70023          	sb	a1,0(a4)
   10dac:	00008067          	ret
   10db0:	0ff5f593          	andi	a1,a1,255
   10db4:	00859693          	slli	a3,a1,0x8
   10db8:	00d5e5b3          	or	a1,a1,a3
   10dbc:	01059693          	slli	a3,a1,0x10
   10dc0:	00d5e5b3          	or	a1,a1,a3
   10dc4:	f6dff06f          	j	10d30 <memset+0x18>
   10dc8:	00279693          	slli	a3,a5,0x2
   10dcc:	00000297          	auipc	t0,0x0
   10dd0:	005686b3          	add	a3,a3,t0
   10dd4:	00008293          	mv	t0,ra
   10dd8:	fa0680e7          	jalr	-96(a3)
   10ddc:	00028093          	mv	ra,t0
   10de0:	ff078793          	addi	a5,a5,-16
   10de4:	40f70733          	sub	a4,a4,a5
   10de8:	00f60633          	add	a2,a2,a5
   10dec:	f6c378e3          	bgeu	t1,a2,10d5c <memset+0x44>
   10df0:	f3dff06f          	j	10d2c <memset+0x14>

00010df4 <__register_exitproc>:
   10df4:	c3c18793          	addi	a5,gp,-964 # 12584 <_global_impure_ptr>
   10df8:	0007a703          	lw	a4,0(a5)
   10dfc:	14872783          	lw	a5,328(a4)
   10e00:	04078c63          	beqz	a5,10e58 <__register_exitproc+0x64>
   10e04:	0047a703          	lw	a4,4(a5)
   10e08:	01f00813          	li	a6,31
   10e0c:	06e84e63          	blt	a6,a4,10e88 <__register_exitproc+0x94>
   10e10:	00271813          	slli	a6,a4,0x2
   10e14:	02050663          	beqz	a0,10e40 <__register_exitproc+0x4c>
   10e18:	01078333          	add	t1,a5,a6
   10e1c:	08c32423          	sw	a2,136(t1) # 101c0 <pulse_count+0x24>
   10e20:	1887a883          	lw	a7,392(a5)
   10e24:	00100613          	li	a2,1
   10e28:	00e61633          	sll	a2,a2,a4
   10e2c:	00c8e8b3          	or	a7,a7,a2
   10e30:	1917a423          	sw	a7,392(a5)
   10e34:	10d32423          	sw	a3,264(t1)
   10e38:	00200693          	li	a3,2
   10e3c:	02d50463          	beq	a0,a3,10e64 <__register_exitproc+0x70>
   10e40:	00170713          	addi	a4,a4,1
   10e44:	00e7a223          	sw	a4,4(a5)
   10e48:	010787b3          	add	a5,a5,a6
   10e4c:	00b7a423          	sw	a1,8(a5)
   10e50:	00000513          	li	a0,0
   10e54:	00008067          	ret
   10e58:	14c70793          	addi	a5,a4,332
   10e5c:	14f72423          	sw	a5,328(a4)
   10e60:	fa5ff06f          	j	10e04 <__register_exitproc+0x10>
   10e64:	18c7a683          	lw	a3,396(a5)
   10e68:	00170713          	addi	a4,a4,1
   10e6c:	00e7a223          	sw	a4,4(a5)
   10e70:	00c6e633          	or	a2,a3,a2
   10e74:	18c7a623          	sw	a2,396(a5)
   10e78:	010787b3          	add	a5,a5,a6
   10e7c:	00b7a423          	sw	a1,8(a5)
   10e80:	00000513          	li	a0,0
   10e84:	00008067          	ret
   10e88:	fff00513          	li	a0,-1
   10e8c:	00008067          	ret

00010e90 <__call_exitprocs>:
   10e90:	fd010113          	addi	sp,sp,-48
   10e94:	c3c18793          	addi	a5,gp,-964 # 12584 <_global_impure_ptr>
   10e98:	01812423          	sw	s8,8(sp)
   10e9c:	0007ac03          	lw	s8,0(a5)
   10ea0:	01312e23          	sw	s3,28(sp)
   10ea4:	01412c23          	sw	s4,24(sp)
   10ea8:	01512a23          	sw	s5,20(sp)
   10eac:	01612823          	sw	s6,16(sp)
   10eb0:	02112623          	sw	ra,44(sp)
   10eb4:	02812423          	sw	s0,40(sp)
   10eb8:	02912223          	sw	s1,36(sp)
   10ebc:	03212023          	sw	s2,32(sp)
   10ec0:	01712623          	sw	s7,12(sp)
   10ec4:	00050a93          	mv	s5,a0
   10ec8:	00058b13          	mv	s6,a1
   10ecc:	00100a13          	li	s4,1
   10ed0:	fff00993          	li	s3,-1
   10ed4:	148c2903          	lw	s2,328(s8)
   10ed8:	02090863          	beqz	s2,10f08 <__call_exitprocs+0x78>
   10edc:	00492483          	lw	s1,4(s2)
   10ee0:	fff48413          	addi	s0,s1,-1
   10ee4:	02044263          	bltz	s0,10f08 <__call_exitprocs+0x78>
   10ee8:	00249493          	slli	s1,s1,0x2
   10eec:	009904b3          	add	s1,s2,s1
   10ef0:	040b0463          	beqz	s6,10f38 <__call_exitprocs+0xa8>
   10ef4:	1044a783          	lw	a5,260(s1)
   10ef8:	05678063          	beq	a5,s6,10f38 <__call_exitprocs+0xa8>
   10efc:	fff40413          	addi	s0,s0,-1
   10f00:	ffc48493          	addi	s1,s1,-4
   10f04:	ff3416e3          	bne	s0,s3,10ef0 <__call_exitprocs+0x60>
   10f08:	02c12083          	lw	ra,44(sp)
   10f0c:	02812403          	lw	s0,40(sp)
   10f10:	02412483          	lw	s1,36(sp)
   10f14:	02012903          	lw	s2,32(sp)
   10f18:	01c12983          	lw	s3,28(sp)
   10f1c:	01812a03          	lw	s4,24(sp)
   10f20:	01412a83          	lw	s5,20(sp)
   10f24:	01012b03          	lw	s6,16(sp)
   10f28:	00c12b83          	lw	s7,12(sp)
   10f2c:	00812c03          	lw	s8,8(sp)
   10f30:	03010113          	addi	sp,sp,48
   10f34:	00008067          	ret
   10f38:	00492783          	lw	a5,4(s2)
   10f3c:	0044a683          	lw	a3,4(s1)
   10f40:	fff78793          	addi	a5,a5,-1
   10f44:	04878a63          	beq	a5,s0,10f98 <__call_exitprocs+0x108>
   10f48:	0004a223          	sw	zero,4(s1)
   10f4c:	fa0688e3          	beqz	a3,10efc <__call_exitprocs+0x6c>
   10f50:	18892783          	lw	a5,392(s2)
   10f54:	008a1733          	sll	a4,s4,s0
   10f58:	00492b83          	lw	s7,4(s2)
   10f5c:	00f777b3          	and	a5,a4,a5
   10f60:	00079e63          	bnez	a5,10f7c <__call_exitprocs+0xec>
   10f64:	000680e7          	jalr	a3
   10f68:	00492783          	lw	a5,4(s2)
   10f6c:	f77794e3          	bne	a5,s7,10ed4 <__call_exitprocs+0x44>
   10f70:	148c2783          	lw	a5,328(s8)
   10f74:	f92784e3          	beq	a5,s2,10efc <__call_exitprocs+0x6c>
   10f78:	f5dff06f          	j	10ed4 <__call_exitprocs+0x44>
   10f7c:	18c92783          	lw	a5,396(s2)
   10f80:	0844a583          	lw	a1,132(s1)
   10f84:	00f77733          	and	a4,a4,a5
   10f88:	00071c63          	bnez	a4,10fa0 <__call_exitprocs+0x110>
   10f8c:	000a8513          	mv	a0,s5
   10f90:	000680e7          	jalr	a3
   10f94:	fd5ff06f          	j	10f68 <__call_exitprocs+0xd8>
   10f98:	00892223          	sw	s0,4(s2)
   10f9c:	fb1ff06f          	j	10f4c <__call_exitprocs+0xbc>
   10fa0:	00058513          	mv	a0,a1
   10fa4:	000680e7          	jalr	a3
   10fa8:	fc1ff06f          	j	10f68 <__call_exitprocs+0xd8>

00010fac <_exit>:
   10fac:	00000593          	li	a1,0
   10fb0:	00000613          	li	a2,0
   10fb4:	00000693          	li	a3,0
   10fb8:	00000713          	li	a4,0
   10fbc:	00000793          	li	a5,0
   10fc0:	05d00893          	li	a7,93
   10fc4:	00000073          	ecall
   10fc8:	00054463          	bltz	a0,10fd0 <_exit+0x24>
   10fcc:	0000006f          	j	10fcc <_exit+0x20>
   10fd0:	ff010113          	addi	sp,sp,-16
   10fd4:	00812423          	sw	s0,8(sp)
   10fd8:	00050413          	mv	s0,a0
   10fdc:	00112623          	sw	ra,12(sp)
   10fe0:	40800433          	neg	s0,s0
   10fe4:	00c000ef          	jal	ra,10ff0 <__errno>
   10fe8:	00852023          	sw	s0,0(a0)
   10fec:	0000006f          	j	10fec <_exit+0x40>

00010ff0 <__errno>:
   10ff0:	c4418793          	addi	a5,gp,-956 # 1258c <_impure_ptr>
   10ff4:	0007a503          	lw	a0,0(a5)
   10ff8:	00008067          	ret

```

# Unique Instructions
```bash
Number of different instructions: 42
List of unique instructions:
xor
bltz
srl
add
or
bgeu
andi
sb
sltu
srli
ecall
bgez
blt
bgtz
lw
jr
sw
mv
jal
ret
auipc
bnez
jalr
sll
bne
beq
srai
bge
bltu
lbu
j
slli
blez
neg
lui
li
beqz
sgtz
addi
and
sub
snez
```

