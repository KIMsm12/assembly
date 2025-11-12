# Short Answer

## 문제 1
**Q:** 다음 명령어 실행 후 BX의 값은 무엇인가?
```asm
mov  bx,0FFFFh
and  bx,6Bh
```
**A:** BX = 006Bh

## 문제 2
**Q:** 다음 명령어 실행 후 BX의 값은 무엇인가?
```asm
mov  bx,91BAh
and  bx,92h
```
**A:** BX = 0092h

## 문제 3
**Q:** 다음 명령어 실행 후 BX의 값은 무엇인가?
```asm
mov  bx,0649Bh
or   bx,3Ah
```
**A:** BX = 64BBh

## 문제 4
**Q:** 다음 명령어 실행 후 BX의 값은 무엇인가?
```asm
mov  bx,029D6h
xor  bx,8181h
```
**A:** BX = A857h

## 문제 5
**Q:** 다음 명령어 실행 후 EBX의 값은 무엇인가?
```asm
mov  ebx,0AFAF649Bh
or   ebx,3A219604
```
**A:** EBX = 0BFAFF69Fh

## 문제 6
**Q:** 다음 명령어 실행 후 RBX의 값은 무엇인가?
```asm
mov  rbx,0AFAF649Bh
xor  rbx,0FFFFFFFFh
```
**A:** RBX = 0000000050509B64h

## 문제 7
**Q:** 다음 명령어 실행 후 AL의 값을 이진수로 나타내시오.
```asm
mov  al,01101111b
and  al,00101101b        ; a.
mov  al,6Dh
and  al,4Ah              ; b.
mov  al,00001111b
or   al,61h              ; c.
mov  al,94h
xor  al,37h              ; d.
```
**A:**
- a. 00101101b
- b. 01001000b
- c. 01101111b
- d. 10100011b

## 문제 8
**Q:** 다음 명령어 실행 후 AL의 값을 16진수로 나타내시오.
```asm
mov  al,7Ah
not  al               ; a.
mov  al,3Dh
and  al,74h           ; b.
mov  al,9Bh
or   al,35h           ; c.
mov  al,72h
xor  al,0DCh          ; d.
```
**A:**
- a. 85h
- b. 34h
- c. 0BFh
- d. 0AEh

## 문제 9
**Q:** 다음 명령어 실행 후 Carry, Zero, Sign 플래그 값을 쓰시오.
```asm
mov  al,00001111b
test al,00000010b       ; a.
mov  al,00000110b
cmp  al,00000101b       ; b.
mov  al,00000101b
cmp  al,00000111b       ; c.
```
**A:**
- a. CF = 0, ZF = 0, SF = 0
- b. CF = 0, ZF = 0, SF = 0
- c. CF = 1, ZF = 0, SF = 1

## 문제 10
**Q:** ECX의 값에 따라 분기하는 조건부 점프 명령어는?
**A:** JECXZ

## 문제 11
**Q:** JA와 JNBE는 Zero와 Carry 플래그에 의해 어떻게 영향을 받는가?
**A:** Zero=0이고 Carry=0일 때 점프한다.

## 문제 12
**Q:** 다음 코드 실행 후 EDX의 최종 값은?
```asm
mov  edx,1
mov  eax,7FFFh
cmp  eax,8000h
jl   L1
mov  edx,0
L1:
```
**A:** EDX = 0000 0000 0000 0001b

## 문제 13
**Q:** 다음 코드 실행 후 EDX의 최종 값은?
```asm
mov  edx,1
mov  eax,7FFFh
cmp  eax,8000h
jb   L1
mov  edx,0
L1:
```
**A:** EDX = 0000 0000 0000 0001b

## 문제 14
**Q:** 다음 코드 실행 후 EDX의 최종 값은?
```asm
mov  edx,1
mov  eax,7FFFh
cmp  eax,0FFFF8000h
jl   L2
mov  edx,0
L2:
```
**A:** EDX = 0000 0000 0000 0000b

## 문제 15
**Q:** True/False: 다음 코드가 Target 라벨로 점프하는가?
```asm
mov  eax,-30
cmp  eax,-50
jg   Target
```
**A:** True

## 문제 16
**Q:** True/False: 다음 코드가 Target 라벨로 점프하는가?
```asm
mov  eax,-42
cmp  eax,26
ja   Target
```
**A:** True

## 문제 17
**Q:** 다음 명령어 실행 후 RBX의 값은?
```asm
mov  rbx,0FFFFFFFFFFFFFFFFh
and  rbx,80h
```
**A:** RBX = 0FFFFFFFFFFFFFF80h

## 문제 18
**Q:** 다음 명령어 실행 후 RBX의 값은?
```asm
mov  rbx,0FFFFFFFFFFFFFFFFh
and  rbx,808080h
```
**A:** RBX = 0FFFFFFFFFF808080h

## 문제 19
**Q:** 다음 명령어 실행 후 RBX의 값은?
```asm
mov  rbx,0FFFFFFFFFFFFFFFFh
and  rbx,80808080h
```
**A:** RBX = 0FFFFFFFF80808080h