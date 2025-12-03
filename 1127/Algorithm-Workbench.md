# Algorithm Workbench 문제 풀이  
## 문제 1  
**Q : Show an example of a base-index operand in 32-bit mode.**  
**A :** `[ebx + esi]`   

---
## 문제 2  
**Q : Show an example of a base-index-displacement operand in 32-bit mode.**  
**A :** `array[ebx + esi]`  

---
## 문제 3  
**Q : Suppose a two-dimensional array of doublewords has three logical rows and four logical columns. Write an expression using ESI and EDI that addresses the third column in the second row. (Numbering for rows and columns starts at zero.)**  
**A :**
```asm
TYPE pArray = sizeA
mov esi, 2*4
mov edi, 3
mov eax, [esi*sizeA + edi*sizeA]
```

---
## 문제 4  
**Q : Write instructions using CMPSW that compare two arrays of 16-bit values named sourcew and targetw.**  
**A :**
```asm
mov ecx, LENGTHOF sourcew
mov esi, OFFSET sourcew
mov edi, OFFSET targetw
cld
repe CMPSW
```

---
## 문제 5  
**Q : Write instructions that use SCASW to scan for the 16-bit value 0100h in an array named wordArray, and copy the offset of the matching member into the EAX register.**  
**A :**
```asm
mov eax, 100h
mov ecx, LENGTHOF wordArray
mov edi, OFFSET wordArray
cld
repne SCASW
sub edi, 2
mov eax, edi
```

---
## 문제 6  
**Q : Write a sequence of instructions that use the Str_compare procedure to determine the larger of two input strings and write it to the console window.**  
**A :**
```asm
.data
str1 BYTE "PINK", 0
str2 BYTE "GRAY", 0
.code
    INVOKE Str_compare ADDR str1, ADDR str2
    ja firstBig
    mov edx, OFFSET str2
    jmp printStr
firstBig:
    mov edx, OFFSET str1
printStr:
    call WriteString
    call Crlf
```

---
## 문제 7  
**Q : Show how to call the Str_trim procedure and remove all trailing "@" characters from a string.**  
**A :**
```asm
INVOKE Str_trim, ADDR str1, '@'
```

---
## 문제 8  
**Q : Show how to modify the Str_ucase procedure from the Irvine32 library so it changes all characters to lower case. **  
**A :**
```asm
Str_ucase PROC USES eax esi,
pString:PTR BYTE
    mov esi, pString
    cmp al, 0
    je L3
    cmp al, 'A'
    jb L2
    cmp al, 'Z'
    ja L2
    or BYTE PTR[esi], 00100000b
L2:
    inc esi
    jmp L1
L3:
    ret
Str_ucase ENDP
```

---
## 문제 9  
**Q : Create a 64-bit version of the Str_trim procedure.**  
**A :**
```asm
Str_trim PROC USES rax rcx rdi,
    pString:PTR BYTE,
    char:BYTE
    mov rdi, pString
    INVOKE Str_length, rdi
    cmp rax, 0
    je L3
    mov rcx, rax
    dec rax
    add rdi, rax
L1:
    mov al, [rdi]
    cmp al, char
    jne L2
    dec rdi
    loop L1
L2: mov BYTE PTR[rdi+1], 0
L3: ret
Str_trim ENDP
```

---
## 문제 10  
**Q : Show an example of a base-index operand in 64-bit mode.**  
**A :** `array[rsi*TYPE array]`  

---
## 문제 11  
**Q : Assuming that EBX contains a row index into a two-dimensional array of 32-bit integers named myArray and EDI contains the index of a column, write a single statement that moves the content of the given array element into the EAX register.**  
**A :** `mov eax, myArray[ebx*row_Size*4 + edi*4]`    

---
## 문제 12  
**Q : Assuming that RBX contains a row index into a two-dimensional array of 64-bit integers named myArray and RDI contains the index of a column, write a single statement that moves the content of the given array element into the RAX register.**  
**A :** `mov rax, myArray[rbx*row_Size*8 + rdi*8]`    

---
