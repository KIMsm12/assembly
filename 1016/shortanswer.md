# Short Answer 문제 풀이

## 문제 1  
**Q : What will be the value in EDX after each of the lines marked (a) and (b) execute?**  
```asm
.data
 one WORD 8002h
 two WORD 4321h
 .code
 mov   edx,21348041h
 movsx edx,one             ; (a)
 movsx edx,two             ; (b)
```
**A :** 2진수에서 one의 MSB는 1이 되므로 F로 채우고, two의 MSB는 0이 되므로 0으로 채운다.  
(a) EDX = FFFF8002h  
(b) EDX = 00004321h  

---
## 문제 2  
**Q : What will be the value in EAX after the following lines execute?**  
```asm
mov  eax,1002FFFFh
inc ax
```
**A :** 1을 더하는 명령어. FFFF에 1을 더하면 10000이 되고 오버플로우 된다. 해당 비트는 무시한다.  
EAX = 10020000h  

---
## 문제 3  
**Q : What will be the value in EAX after the following lines execute?**  
```asm
mov  eax,30020000h
dec  ax
```
**A :** 1을 빼는 명령어. 0000에 1을 빼면 FFFF로 언더플로우 된다.   
EAX = 3002FFFFh  

---
## 문제 4  
**Q : What will be the value in EAX after the following lines execute?**  
```asm
mov  eax,1002FFFFh
neg ax
```
**A :** 2의 보수로 부호를 바꾸는 명령어. FFFF(1111 1111 1111 1111)를 2의 보수 취하면 0001(0000 0000 0000 0001)이 된다.  
EAX = 0001h   

---
## 문제 5    
**Q : What will be the value of the Parity flag after the following lines execute?**  
```asm
mov  al,1
add  al,3
```
**A :** AL = 0000 0100    
PF = 0  

---
## 문제 6  
**Q : What will be the value of EAX and the Sign flag after the following lines execute?**  
```asm
mov  eax,5
sub  eax,6
```
**A :** 5-6 = -1, -1 = FFFFFFFFh  
EAX = FFFFFFFFh

---
## 문제 7  
**Q : In the following code, the value in AL is intended to be a signed byte. Explain how the Overflow flag helps, or does not help you, to determine whether the final value in AL falls within a valid signed range.**  
```asm
mov al,-1
add  al,130
```
**A :** -1 = 11111111b, 130 = 10000010b, -1 + 130 = 1 1000 0001 => OF = 1, AL = 1000 0001 = -127  
부호있는 byte로 해석하면 값은 -127이고 OF가 세팅되면서 해당 값이 정상적이지 않을 수 있음을 나타냄  

---
## 문제 8  
**Q : What value will RAX contain after the following instruction executes?**  
```asm
mov  rax,44445555h
```
**A :** RAX = 0000000044445555h  

---
## 문제 9  
**Q : What value will RAX contain after the following instructions execute?**  
```asm
.data
dwordVal DWORD 84326732h
.code
mov  rax,0FFFFFFFF00000000h
mov  rax,dwordVal
```
**A :** RAX = 0FFFFFFFF84326732h  

---
## 문제 10  
**Q : What value will EAX contain after the following instructions execute?**  
```asm
.data
dVal DWORD 12345678h
.code
mov  ax,3
mov  WORD PTR dVal+2,ax    
mov  eax,dVal
```
**A :** dVal = [78][56][34][12], ax = [03][00] => dVal = [78][56][03][00]  
EAX = 00035678h  

---
## 문제 11  
**Q : What will EAX contain after the following instructions execute?**  
```asm
.data
.dVal DWORD ?
.code
mov  dVal,12345678h
mov  ax,WORD PTR dVal+2
add  ax,3
mov  WORD PTR dVal,ax
mov  eax,dVal
```
**A :** EAX = 12341237h    
dVal = [78][56][34][12] => ax = [34][12] => ax = [37][12]  
=> dVal = [37][12][34][12] => EAX = [37][12][34][12]  

---
## 문제 12  
**Q : (Yes/No): Is it possible to set the Overflow flag if you add a positive integer to a negative integer?**  
**A :** Yes  
7번 문제가 그렇다.  

---
## 문제 13  
**Q : (Yes/No): Will the Overflow flag be set if you add a negative integer to a negative integer and produce a positive result?**  
**A :** YES  
음수와 음수를 더하면 양수가 될 수 없다.  
양수값이 나왔다면 결과가 너무 커져 이상한 값이 나오는 것이고, OF가 세팅된다.  

---
## 문제 14  
**Q : (Yes/No): Is it possible for the NEG instruction to set the Overflow flag?**  
**A :** YES  
```asm
mov al, -128 
neg al
```
AL의 값은 +128이 되어야 하고 AL로는 이 값을 표현할 수 없으므로 OF가 세팅된다.  

---
## 문제 15  
**Q : (Yes/No): Is it possible for both the Sign and Zero flags to be set at the same time? Use the following variable definitions for Questions 16–19:**  
```asm
.data
var1 SBYTE -4,-2,3,1
var2 WORD 1000h,2000h,3000h,4000h
var3 SWORD -16,-42
var4 DWORD 1,2,3,4,5
```
**A :** No  
Sign 플래그는 MSB에 따라서 세팅된다. MSB가 1이여야 1로 세팅되는데, Zero 플래그는 모든 비트가 0이여야 세팅된다.  
즉 불가능하다.  

---
## 문제 16  
**Q : For each of the following statements, state whether or not the instruction is valid:**  
```asm
a. mov   ax,var1?
b. mov   ax,var2
c. mov   eax,var3
d. mov   var2,var3
e. movzx ax,var2
f. movzx var2,al
g, mov   ds, ax
h. mov   ds, 1000h
```
**A :**  
a = **No**, 명령어 변수명에 ? 안됨  
b = **Yes**  
c = **No**, Size 다름  
d = **No**, mem to mem 명령어는 불가능  
e = **Yes**  
f = **No**, movzx의 목적지는 레지스터여야 함  
g = **Yes**  
h = **No**, 세그먼트 레지스터에 즉시값 사용 불가능  

---
## 문제 17  
**Q : What will be the hexadecimal value of the destination operand after each of the following instructions execute in sequence?**  
```asm
mov al, var1              ; a.
mov ah, [var1+3]          ; b.
```
**A :** a. FCh, b. 01h  

---
## 문제 18  
**Q : What will be the value of the destination operand after each of the following instructions execute in sequence?**  
```asm
mov ax, var2          ; a.
mov ax, [var2+4]      ; b.
mov ax, var3          ; c.
mov ax, [var3-2]      ; d.
```
**A :** a. 1000h, b. 3000h, c. FFF0h, d. 4000h    

---
## 문제 19  
**Q : What will be the value of the destination operand after each of the following instructions execute in sequence?**  
```asm
mov   edx, var4        ; a.
movzx edx, var2        ; b.
mov   edx, [var4+4]    ; c.
movsx edx, var1        ; d.
```
**A :** a. 00000001h, b. 00001000h, c. 00000002h, d. FFFFFFFCh    

---
