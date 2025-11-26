# Algorithm Workbench 문제 풀이  
## 문제 1  
**Q : Here is a calling sequence for a procedure named AddThree that adds three doublewords (assume that the STDCALL calling convention is used):**  
```asm
push 10h
push 20h
push 30h
call AddThree
```
**Draw a picture of the procedure’s stack frame immediately after EBP has been pushed on the runtime stack.**  
**A :**  
| 스택 주소 기준 | 내용       | 설명                         |
|----------------|------------|------------------------------|
| EBP + 16       | 10h        | 세 번째 인자                 |
| EBP + 12       | 20h        | 두 번째 인자                 |
| EBP + 8        | 30h        | 첫 번째 인자                 |
| EBP + 4        | Return Addr| 리턴 주소                    |
| EBP, ESP       | EBP, ESP   | 스택 프레임 포인터            |

---
## 문제 2  
**Q : Create a procedure named AddThree that receives three integer parameters and calculates and returns their sum in the EAX register.**  
**A :** 1번을 기준으로 한다.  
```asm
;---------------------------------------
AddThree PROC
; 3개의 정수를 받아 합을 구하는 프로시저
; 입력 : A[ebp+8], B[ebp+12], C[ebp+16] 
; 출력 : EAX = A+B+C
;---------------------------------------
  enter 0, 0
  mov eax, [ebp + 8]
  add eax, [ebp + 12]
  add eax, [ebp + 16]
  leave
  ret 12
AddThree ENDP
```

---
## 문제 3  
**Q : Declare a local variable named pArray that is a pointer to an array of doublewords.**  
**A :**
```asm
LOCAL pArray:PTR DWORD
```

---
## 문제 4  
**Q : Declare a local variable named buffer that is an array of 20 bytes.**  
**A :**
```asm
LOCAL buffer[20]:BYTE
```

---
## 문제 5  
**Q : Declare a local variable named pwArray that points to a 16-bit unsigned integer.**  
**A :**
```asm
LOCAL pwArray:PTR WORD
```

---
## 문제 6  
**Q : Declare a local variable named myByte that holds an 8-bit signed integer.**  
**A :**
```asm
LOCAL myByte:SBYTE
```

---
## 문제 7  
**Q : Declare a local variable named myArray that is an array of 20 doublewords.**  
**A :**
```asm
LOCAL myArray[20]:DWORD
```

---
## 문제 8  
**Q : Create a procedure named SetColor that receives two stack parameters: forecolor and backcolor, and calls the SetTextColor procedure from the Irvine32 library.**  
**A :**
```asm
SetColor PROC forecolor:BYTE, backcolor:BYTE
  INVOKE SetTextColor, forecolor, backcolor
  ret
SetColor ENDP
```

---
## 문제 9  
**Q : Create a procedure named WriteColorChar that receives three stack parameters: char, forecolor, and backcolor. It displays a single character, using the color attributes specified in forecolor and backcolor.**  
**A :**
```asm
;------------------------------------------
WriteColorChar PROC
; 입력 : char       [ebp+16]
;       forecolor   [ebp+12]
;       backcolor   [ebp+8]
; 출력 : 전경색과 배경색을 바꾼 문자
;------------------------------------------
WriteColorChar PROC
  enter 0, 0
  mov al, [ebp+12]  ; 전경색
  mov ah, [ebp+8]   ; 배경색
  call SetTextColor ; 색 설정

  mov al, [ebp+16]  ; 문자 가져오기
  call WriteChar    ; 출력
  leave
  ret
WriteColorChar ENDP
```

---
## 문제 10  
**Q : Write a procedure named DumpMemory that encapsulates the DumpMem procedure in the Irvine32 library. Use declared parameters and the USES directive. The following is an example of how it should be called: INVOKE DumpMemory, OFFSET array, LENGTHOF array, TYPE array.**  
**A :** DumpMem을 캡슐화한 DumpMemory  
```asm
;---------------------------------------------------------------------------
DumpMemory PROC USES eax edx, ptrMem:PTR BYTE, memLen:DWORD, memType:DWORD
; DumpMem을 호출하여 지정된 메모리를 화면에 호출하는 프로시저
;---------------------------------------------------------------------------
  INVOKE DumpMem, ptrMem, memLen, memType
  ret
DumpMemory ENDP
```

---
## 문제 11  
**Q : Declare a procedure named MultArray that receives two pointers to arrays of doublewords, and a third parameter indicating the number of array elements.  Also, create a PROTO declaration for this procedure.**  
**A :**  
PROTO 선언
```asm
MultArray PROTO ptrArray1:PTR DOWRD, ptrArray2:PTR DWORD, count:DWORD
```
PROC 선언
```asm
MultArrat PROC ptrArray1:PTR DWORD, ptrArray2:PTR DWORD, count:DWORD
  .
  .
  .
  ret
MultArrayy ENDP
```

---
