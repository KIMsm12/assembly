# Algorithm Workbench 문제 풀이  

## 문제 1  
**Q : Write a sequence of shift instructions that cause AX to be sign-extended into EAX. In other words, the sign bit of AX is copied into the upper 16 bits of EAX. Do not use the CWD instruction.**  
**A :** shl로 ax의 값을 통째로 상위 16바이트로 옮긴 뒤, sar로 부호비트를 복사하며 ax의 값을 원래자리로 되돌린다.  
```asm
shl eax, 16
sar eax, 16
```

---
## 문제 2  
**Q : Suppose the instruction set contained no rotate instructions. Show how you would use SHR and a conditional jump instruction to rotate the contents of the AL register 1 bit to the right.**  
**A :**
```asm
SHR AL, 1
jnc next
or al, 80h
next:
```

---
## 문제 3  
**Q : Write a logical shift instruction that multiplies the contents of EAX by 16.**  
**A :**
```asm
SHL EAX, 4
```

---
## 문제 4  
**Q : Write a logical shift instruction that divides EBX by 4.**  
**A :**
```asm
SHR EBX, 2
```

---
## 문제 5  
**Q : Write a single rotate instruction that exchanges the high and low halves of the DL register.**  
**A :** ROL을 사용해도 무관  
```asm
ROR DL, 4
```

---
## 문제 6  
**Q : Write a single SHLD instruction that shifts the highest bit of the AX register into the lowest bit position of DX and shifts DX one bit to the left.**  
**A :**
```asm
SHLD DX, AX, 1
```

---
## 문제 7  
**Q : Write a sequence of instructions that shift three memory bytes to the right by 1 bit position. Use the following test data:**  
```asm
byteArray BYTE 81h,20h,33h
```
**A :**
```asm
SHR BYTE PTR[byteArray], 1
SHR BYTE PTR[byteArray+1], 1
SHR BYTE PTR[byteArray+2], 1
```

---
## 문제 8  
**Q : Write a sequence of instructions that shift three memory words to the left by 1 bit position. Use the following test data:**  
```asm
wordArray WORD 810Dh, 0C064h,93ABh
```
**A :**
```asm
SHL WORD PTR[wordArray], 1
SHL WORD PTR[wordArray+2], 1
SHL WORD PTR[wordArray+4], 1
```

---
## 문제 9  
**Q : Write instructions that multiply -5 by 3 and store the result in a 16-bit variable val1.**  
**A :**
```asm
mov ax, 3
IMUL ax, -5
mov val1, ax
```

---
## 문제 10  
**Q : Write instructions that divide -276 by 10 and store the result in a 16-bit variable val1.**  
**A :** IDIV 전에는 CBW, CWD, CDQ 부호 확장 명령어로 부호 확장 해야함을 유의  
```asm
mov ax, -276
cwd
IDIV 10
mov val1, ax
```

---
## 문제 11  
**Q : Implement the following C++ expression in assembly language, using 32-bit unsigned operands:**  
```asm
val1 = (val2 * val3) / (val4 - 3)
```
**A :**
```asm
mov eax, val2
MUL val3
mov ebx, val4
SUB ebx, 3
DIV ebx
mov val1, eax
```

---
## 문제 12  
**Q : Implement the following C++ expression in assembly language, using 32-bit signed operands:**  
```asm
val1 = (val2 / val3) * (val1 + val2)
```
**A :**
```asm
mov eax, val2
cdq
IDIV val3
mov ebx, val1
add ebx, val2
IMUL eax, ebx
mov val1, eax
```

---
## 문제 13  
**Q : Write a procedure that displays an unsigned 8-bit binary value in decimal format. Pass the binary value in AL. The input range is limited to 0 to 99, decimal. The only procedure you can call from the book’s link library is WriteChar. The procedure should contain approximately eight instructions. Here is a sample call:**  
```asm
mov  al,65            ; range limit: 0 to 99
call showDecimal8
```
**A :** 0~99 범위 내의 숫자가 입력되므로 aam을 사용한다.  
```asm
showDecimal8:
  AAM             ; 10의 자리가 AH에, 1의 자리가 AL에 저장
  or AX, 3030h    ; 각 자리에 ASCII 문자열의 값을 씌움
  push eax
  mov al, ah      ; 10의 자리를 먼저 출력
  call WriteChar
  pop eax         ; 1의 자리를 출력
  call WriteChar
  ret
```

---
## 문제 14  
**Q : Challenge: Suppose AX contains 0072h and the Auxiliary Carry flag is set as a result of adding two unknown ASCII decimal digits. Use the Intel 64 and IA-32 Instruction Set Reference to determine what output the AAA instruction would produce. Explain your answer.**  
**A :** AAA를 실행하면 `al & 0Fh`의 결과가 9보다 큰지, 아니면 `AF = 1`인지 검사한다. 그렇다면 AH+1, AL+6을 하여 비압축 10진수 형태로 만들어 저장한다.  
AF가 셋 되었으므로 `al & 0Fh` -> `ah+1, al+6` => AX : 0108h   

---
## 문제 15  
**Q : Challenge: Using only SUB, MOV, and AND instructions, show how to calculate x = n mod y, assuming that you are given the values of n and y. You can assume that n is any 32-bit unsigned integer, and y is a power of 2.**  
**A :**
```asm
mov edx, divisor     ; 제수는 2의 거듭제곱수
sub edx, 1           ; 제수는 00011111 <-의 형태
mov eax, dividend
and eax, edx         ; 피제수와 and 하면 1000 으로 나눌 때 그보다 작은 비트만 남김
mov answer, eax      ; 나머지 저장
```

---
## 문제 16  
**Q : Challenge: Using only SAR, ADD, and XOR instructions (but no conditional jumps), write code that calculates the absolute value of the signed integer in the EAX register. Hints: A number can be negated by adding -1 to it and then forming its one’s complement. Also, if you XOR an integer with all 1s, its 1s are reversed. On the other hand, if you XOR an integer with all zeros, the integer is unchanged.**  
**A :**
```asm
mov edx, eax     ; 비트 마스크 생성
sar edx, 31      ; 양수라면 00000000h, 음수라면 0FFFFFFFFh
add eax, edx     ; 양수라면 아무 변화 없음, 음수라면 -1
xor eax, edx     ; 양수라면 아무 변화 없음, 음수라면 부호 전환(2의 보수 생성)
```

---
