Algorithm Workbench 문제 풀이

# 문제 1  
**Q : Write a sequence of MOV instructions that will exchange the upper and lower words in a doubleword variable named three.**  
**A :** mov 명령어를 이용하여 DWORD 크기의 변수, **three**의 상위 워드와 하위 워드를 바꾸는 시퀀스를 작성한다.  
```asm
mov ax, WORD PTR three
mov bx, WORD PTR three+2
mov WORD PTR three, bx
mov WORD PTR three+2, ax
```

---
# 문제 2  
**Q : Using the XCHG instruction no more than three times, reorder the values in four 8-bit registers from the order A,B,C,D to B,C,D,A.**  
**A :** XCHG 명령어를 최대 3번만 이용하여 8비트 레지스터 네 개에 저장된 값의 순서를 A, B, C, D => B, C, D, A 로 재배열 한다.   
```asm
; 초기값 => A B C D
XCHG A, B     ; A=B, B=A => B A C D
XCHG B, C     ; A=C, C=A => B C A D 
XCHG C, D     ; A=D, D=A => B C D A
; 여기서 A, B, C, D는 모두 같은 크기의 레지스터 이름이라고 가정한다. XCHG 명령어의 두 오퍼랜드는 모두 메모리 주소일 수 없다.
```

---
# 문제 3  
**Q : Transmitted messages often include a parity bit whose value is combined with a data byte to produce an even number of 1 bits. Suppose a message byte in the AL register contains 01110101. Show how you could use the Parity flag combined with an arithmetic instruction to determine if this message byte has even or odd parity.**  
**A :**  
```asm
mov al, 01110101b
add al, 0       ; PF = 0
```

---
# 문제 4  
**Q : Write code using byte operands that adds two negative integers and causes the Overflow flag to be set.**  
**A :** 두 개의 음수를 더해 OF가 설정되도록 코드를 작성한다.  
```asm
mov al, -110
add al, -80        ; OF = 1
```

---
# 문제 5  
**Q : Write a sequence of two instructions that use addition to set the Zero and Carry flags at the same time.**  
**A :** 두 개의 명령어로 ZF와 CF를 동시에 설정하는 시퀀스를 작성한다.  
```asm
mov al, 0FFh    ; al = FFh
add al, 1       ; al = (1)00h => ZF = 1, CF = 1
```

---
# 문제 6  
**Q : Write a sequence of two instructions that set the Carry flag using subtraction.**  
**A :**  
```asm
mov al, 5      ; al = 5
sub al, 10     ; al = -5 => CF = 1
```

---
# 문제 7  
**Q : Implement the following arithmetic expression in assembly language: EAX = –val2 + 7 - val3 + val1. Assume that val1, val2, and val3 are 32-bit integer variables.**  
**A :** 어셈블리로 산술표현식 EAX = -val2 + 7 - val3 + val1 을 계산하도록 한다.  
```asm
mov eax, val2
neg eax
add eax, 7
sub eax, val3
add eax, val1
```

---
# 문제 8  
**Q : Write a loop that iterates through a doubleword array and calculates the sum of its elements using a scale factor with indexed addressing.**  
**A :**  
```asm
.data
array DWORD 10, 20, 30, 40, 50
.code
mov esi, OFFSET array
mov eax, 0
mov ecx, LENGTHOF array
mov ebx, 0

sum_loop:
  add eax, [esi + ebx*4]
  inc ebx
  loop sum_loop
```

---
# 문제 9  
**Q : Implement the following expression in assembly language: AX = (val2 + BX) –val4. Assume that val2 and val4 are 16-bit integer variables.**  
**A :** 산술 표현식 AX = (val2 + BX) -val4 를 어셈블리어로 구현한다.  
```asm
mov ax, val2
add ax, bx
sub ax, val4
```

---
# 문제 10  
**Q : Write a sequence of two instructions that set both the Carry and Overflow flags at the same time.**  
**A :** 두 개의 명령어를 사용하여 CF와 OF가 동시에 세팅되도록 한다.  
```asm
mov ax, 8000h
add ax, 8000h
```

---
# 문제 11  
**Q : Write a sequence of instructions showing how the Zero flag could be used to indicate unsigned overflow after executing INC and DEC instructions.**  
**A :** INC와 DEC 명령어를 실행하고 ZF를 사용하여 부호없는 오버플로우를 감지하는 명령어 시퀀스를 작성한다.  
```asm
.data
val WORD 0FFFFh
val2 WORD 0001h
.code
mov ax, val
inc ax
jz overflow      ; jz는 ZF = 1 일 때 점프한다.
mov ax, val2
dec ax
jz zero_reached
```

---
# Use the following data definitions for Questions 12–18:
```asm
.data
myBytes  BYTE 10h,20h,30h,40h
myWords  WORD 3 DUP(?),2000h
myString BYTE "ABCDE"
```
## 문제 12  
**Q : Insert a directive in the given data that aligns myBytes to an even-numbered address.**  
**A :**   
```asm
.data
ALIGN 2
myBytes BYTE 10h, 20h, 30h, 40h
```

---
## 문제 13  
**Q : What will be the value of EAX after each of the following instructions execute?**  
```asm
mov  eax,TYPE myBytes        ; a.
mov  eax,LENGTHOF myBytes    ; b.
mov  eax,SIZEOF myBytes      ; c.
mov  eax,TYPE myWords        ; d.
mov  eax,LENGTHOF myWords    ; e.
mov  eax,SIZEOF myWords      ; f.
mov  eax,SIZEOF myString     ; g.
```
**A :**  
a. 1  
b. 4  
c. 4  
d. 2  
e. 4  
f. 8  
g. 5  

---
## 문제 14  
**Q : Write a single instruction that moves the first two bytes in myBytes to the DX register. The resulting value will be 2010h.**  
**A :**  
```asm
mov dx, WORD PTR myBytes
```

---
## 문제 15  
**Q : Write an instruction that moves the second byte in myWords to the AL register.**  
**A :**  
```asm
mov al, BYTE PTR myWords+1
```

---
## 문제 16  
**Q : Write an instruction that moves all four bytes in myBytes to the EAX register.**  
**A :**  
```asm
mov eax, DWORD PTR myBytes
```

---
## 문제 17  
**Q : Insert a LABEL directive in the given data that permits myWords to be moved directly to a 32-bit register.**  
**A :**  
```asm
.data
myDwords LABEL DWORD
myWords WORD 2 DUP(?), 2000h
.code
mov eax, myDwords
```

---
## 문제 18  
**Q : Insert a LABEL directive in the given data that permits myBytes to be moved directly to a 16-bit register.**  
**A :**  
```asm
.data
myWords2 LABEL WORD
myBytes BYTE 10h, 20h, 30h, 40h
.code
mov ax, myWords2
mov bx, myWords2+2
```

---
