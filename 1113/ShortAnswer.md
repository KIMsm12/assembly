# Short Answer 문제 풀이  

## 문제 1  
**Q : In the following code sequence, show the value of AL after each shift or rotate instruction has executed:**  
```asm
mov al,0D4h
shr al,1             ; a.
mov al,0D4h
sar al,1             ; b.
mov al,0D4h
sar al,4             ; c.
mov al,0D4h
rol al,1             ; d.
```
**A :** 0D4h = 11010100b  
a. 01101010b => 6Ah  
b. 11101010b => 0EAh  
c. 11111101b => 0FDh  
d. 10101001b => 0A9h  

---
## 문제 2  
**Q : In the following code sequence, show the value of AL after each shift or rotate instruction has executed:**  
```asm
mov al,0D4h
ror al,3             ; a.
mov al,0D4h
rol al,7             ; b.
stc
mov al,0D4h
rcl al,1             ; c.
stc
mov al,0D4h
rcr al,3             ; d.
```
**A :** 0D4h = 11010100b, stc에서 CF = 1    
a. 10011010b => 9Ah  
b. 01101010b => 6Ah  
c. 10101001b => 0A9h  
d. 00111010b => 3Ah  

---
## 문제 3  
**Q : What will be the contents of AX and DX after the following operation?**
```asm
mov dx,0
mov ax,222h
mov cx,100h
mul cx
```
**A :** DX : 0002h, AX : 2200h  

---
## 문제 4  
**Q : What will be the contents of AX after the following operation?**  
```asm
mov ax,63h
mov bl,10h
div bl
```
**A :** AX : 0306h  

---
## 문제 5  
**Q : What will be the contents of EAX and EDX after the following operation?**  
```asm
mov eax,123400h
mov edx,0
mov ebx,10h
div ebx
```
**A :** EDX : 00000000h, EAX : 00012340h  

---
## 문제 6  
**Q : What will be the contents of AX and DX after the following operation?**  
```asm
mov ax,4000h
mov dx,500h
mov bx,10h
div bx
```
**A :** 나눗셈 결과의 몫이 목적지인 AX에 들어갈 수 없다. 오버플로우가 발생하고 프로그램은 종료된다.  

---
## 문제 7  
**Q : What will be the contents of BX after the following instructions execute?**  
```asm
mov bx,5
stc
mov ax,60h
adc bx,ax
```
**A :** BX : 0066h  

---
## 문제 8  
**Q : Describe the output when the following code executes in 64-bit mode:**  
```asm
.data
dividend_hi  QWORD 00000108h
dividend_lo  QWORD 33300020h
divisor      QWORD 00000100h
.code
mov  rdx,dividend_hi
mov  rax,dividend_lo
div  divisor
```
**A :** 나눗셈 결과의 몫이 목적지인 RAX에 들어갈 수 없다. 오버플로우가 발생하고 프로그램은 종료된다.  

---
## 문제 9  
**Q : The following program is supposed to subtract val2 from val1. Find and correct all logic errors (CLC clears the Carry flag):**  
```asm
.data
 val1   QWORD 20403004362047A1h
 val2   QWORD 055210304A2630B2h
 result QWORD 0
.code
   mov  cx,8               ; loop counter
   mov  esi,val1           ; set index to start
   mov  edi,val2
   clc                     ; clear Carry flag
top:
   mov  al,BYTE PTR[esi]   ; get first number
   sbb  al,BYTE PTR[edi]    ; subtract second
   mov  BYTE PTR[esi],al    ; store the result
   dec  esi
   dec  edi
   loop top
```
**A :**   
1. esi와 edi가 변수의 주소를 가리키게 하고 싶을 때는 OFFSET 지시어를 사용해야 한다. 해당 명령어는 val1, val2의 하위 8바이트 값을 가지게 한다.  
2. 변수 저장방식은 '리틀 엔디안'으로 가장 작은 주소에 가장 하위 바이트의 값이 들어간다. dec를 하면 주소가 이상한 값을 가리킨다.  
3. 뺄셈의 결과 값을 return에 저장하지 않고 esi가 가리키는 주소로 덮어씌운다.  

---
## 문제 10  
**Q : What will be the hexadecimal contents of RAX after the following instructions execute in 64-bit mode?**  
```asm
.data
multiplicand QWORD 0001020304050000h
.code
imul rax,multiplicand, 4
```
**A :** RAX : 0004080C10140000h  
다른 풀이 : x4는 왼쪽으로 2번 시프트 하는 것과 같다.  
multiplicand : 0000 0000 0000 0001 0000 0010 0000 0011 0000 0100 0000 0101 0000 0000 0000 0000b  
      => RAX : 0000 0000 0000 0100 0000 1000 0000 1100 0001 0000 0001 0100 0000 0000 0000 0000b = 0004080C10140000h  

---