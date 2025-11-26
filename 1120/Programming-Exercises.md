# Programming Exercises 문제 풀이
## ★ 1. FindLargest Procedure  
**Q : Create a procedure named FindLargest that receives two parameters: a pointer to a signed doubleword array, and a count of the array’s length. The procedure must return the value of the largest array member in EAX. Use the PROC directive with a parameter list when declaring the procedure. Preserve all registers (except EAX) that are modified by the procedure. Write a test program that calls FindLargest and passes three different arrays of different lengths. Be sure to include negative values in your arrays. Create a PROTO declaration for FindLargest.**  
**A :** 배열과 그 길이를 파라미터로 받아 배열의 최댓값을 반환하는 프로시저 작성, PROTO로 선언 및 해당 프로시저 3회 실행  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

FindLargest PROTO, 
    ptrArray:PTR SDWORD,
    arrayCount:DWORD

.data
    arr1 SDWORD 23, -45, 67, -12, 89
    arr1Size DWORD 5

    arr2 SDWORD -100, -50, 0, 50, 100, -200, 150
    arr2Size DWORD 7

    arr3 SDWORD -10, -2, -5
    arr3Size DWORD 3

    msg1 BYTE "Array 1 - Largest value: ", 0
    msg2 BYTE "Array 2 - Largest value: ", 0
    msg3 BYTE "Array 3 - Largest value: ", 0
.code
main PROC
    call Clrscr

    mov edx, OFFSET msg1
    call WriteString
    INVOKE FindLargest, OFFSET arr1, arr1Size
    call WriteInt
    call Crlf

    mov edx, OFFSET msg2
    call WriteString
    INVOKE FindLargest, OFFSET arr2, arr2Size
    call WriteInt
    call Crlf

    mov edx, OFFSET msg3
    call WriteString
    INVOKE FindLargest, OFFSET arr3, arr3Size
    call WriteInt
    call Crlf
    exit 
main ENDP
;----------------------------------------------------------
FindLargest PROC USES esi ecx edx,
    ptrArray:PTR SDWORD,
    arrayCount:DWORD
; 배열에서 가장 큰 값을 찾아 EAX에 반환하는 프로시저
; 받은 파라미터 :
;   ptrArray   = 배열의 주소
;   arrayCount = 배열의 길이
; 반환값 : EAX = 배열의 최댓값
;---------------------------------------------------------
    mov esi, ptrArray       
    mov ecx, arrayCount
    cmp ecx, 0          ; 배열 길이가 0 이하면 종료
    jle L2
    mov eax, [esi]      ; 첫 번째 원소를 최댓값으로 초기화
    dec ecx             ; ecx 1 감소 후 길이가 0이면 종료
    jz L2
    add esi, 4          ; 아니면 다음 원소로 포인터 이동
L1:
    mov edx, [esi]      ; EDX = 현재 배열 원소의 값
    cmp edx, eax        ; 현재 원소와 최댓값 비교
    jle L3              ; 작으면 L3로 점프
    mov eax, edx        ; 아니면 현재 원소를 최댓값으로 갱신
L3:
    add esi, 4          ; 다음 원소 확인
    loop L1
L2:
    ret 8
FindLargest ENDP
END main
```

---
## ★ 2. Chess Board  
**Q : Write a program that draws an 8×8 chess board, with alternating gray and white squares. You can use the SetTextColor and Gotoxy procedures from the Irvine32 library. Avoid the use of global variables, and use declared parameters in all procedures. Use short procedures that are focused on a single task.**  
**A :** 8x8 체스판을 출력한다.  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

DrawChessBoard PROTO
DrawSquare PROTO, xpos:DWORD, ypos:DWORD, color:DWORD
GetColor PROTO, row:DWORD, col:DWORD

.data
    space BYTE "▒▒", 0 
    whiteColor DWORD 15
    grayColor DWORD 8
.code
main PROC
    call Clrscr
    INVOKE DrawChessBoard
    call Crlf
    exit 
main ENDP
;--------------------------------
DrawChessBoard PROC
; 8x8 체스판을 출력하는 프로시저
;--------------------------------
    pushad
    mov ecx, 8      ; 8개의 행
    xor esi, esi    ; 0으로 초기화
rowLoop:
    mov edi, 8      ; 8개의 열
    xor ebx, ebx    ; 각 행마다 열 0으로 초기화
colLoop:
    INVOKE GetColor, esi, ebx     ; EAX = Color
    ; 화면 좌표 계산 x=col*2, y=row 문자 2개로 1칸을 표현
    mov edx, ebx
    shl edx, 1          ; *2 (문자 폭)
    INVOKE DrawSquare, edx, esi, eax

    inc ebx             ; col++
    dec edi             ; colLoop 카운트 감소
    jnz colLoop
    inc esi             ; row++
    loop rowLoop        ; rowLoop 카운트 감소
    popad
    ret
DrawChessBoard ENDP
;-----------------------------------------------------
DrawSquare PROC USES eax edx,
    xpos:DWORD,
    ypos:DWORD,
    color:DWORD
; 색상을 color로 변경하고 좌표로 이동해 체스판 칸을 표현
; 받은 파라미터 :
;   xpos(edx) = 출력할 콘솔 x좌표
;   ypos(ebx) = 출력할 콘솔 y좌표
;   color(eax) = 칸의 색상
; 반환값 : 없음 (콘솔에 한 칸 출력)
;-----------------------------------------------------
    mov eax, color
    call SetTextColor       ; 색 설정

    mov dl, BYTE PTR xpos
    mov dh, BYTE PTR ypos
    call Gotoxy             ; 커서 이동

    mov edx, OFFSET space
    call WriteString        ; 칸 출력
    ret
DrawSquare ENDP
;-------------------------------------------------------
GetColor PROC USES ebx edx,
    row:DWORD,
    col:DWORD
; (row+col)mod 2를 이용해 칸 색 결정 흰색: 짝, 회색: 홀
; 받은 파라미터 :
;   row : 현재 행 번호
;   col : 현재 열 번호
; 반환값 : EAX = 색상값(흰색 혹은 회색)
;-------------------------------------------------------
    mov eax, row
    add eax, col            ; row+col

    and eax, 1              ; mod 2
    cmp eax, 0              ; 0이면(짝수) 흰색
    jne graySquare          ; 아니면(홀수) 회색

    mov eax, whiteColor
    jmp done
graySquare:
    mov eax, grayColor
done:
    ret
GetColor ENDP
END main
```

---
## ★★★ 3. Chess Board with Alternating Colors  
**Q : This exercise extends Exercise 2. Every 500 milliseconds, change the color of the colored squares and redisplay the board. Continue until you have shown the board 16 times, using all possible 4-bit background colors. (The white squares remain white throughout.)**  
**A :** 2번의 확장, 흰색 칸은 냅두고 회색 칸을 500ms마다 색 변경하며 출력하는 프로시저. 아무튼 됨  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

DrawChessBoard PROTO
DrawSquare PROTO, xpos:DWORD, ypos:DWORD, color:DWORD
GetColor PROTO, row:DWORD, col:DWORD

.data
    space BYTE "▒▒", 0 
    whiteColor DWORD 15
    ChangeColor DWORD 0
.code
main PROC
    call Clrscr
    mov ecx, 16
    xor ebx, ebx
colorLoop:
    call Clrscr
    INVOKE DrawChessBoard
    INVOKE Sleep, 500
    inc ChangeColor
    loop colorLoop
    exit 
main ENDP
;--------------------------------
DrawChessBoard PROC
; 8x8 체스판을 출력하는 프로시저
;--------------------------------
    pushad
    mov ecx, 8      ; 8개의 행
    xor esi, esi    ; 0으로 초기화
rowLoop:
    push ecx
    mov ecx, 8      ; 8개의 열
    xor edi, edi    ; 각 행마다 열 0으로 초기화
colLoop:
    INVOKE GetColor, esi, edi     ; EAX = Color
    ; 화면 좌표 계산 x=col*2, y=row 문자 2개로 1칸을 표현
    mov edx, edi
    shl edx, 1          ; *2 (문자 폭)
    INVOKE DrawSquare, edx, esi, eax

    inc edi             ; col++
    dec ecx             ; colLoop 카운트 감소
    jnz colLoop
    inc esi             ; row++
    pop ecx
    loop rowLoop        ; rowLoop 카운트 감소
    popad
    ret
DrawChessBoard ENDP
;-----------------------------------------------------
DrawSquare PROC USES eax edx,
    xpos:DWORD,
    ypos:DWORD,
    color:DWORD
; 색상을 color로 변경하고 좌표로 이동해 체스판 칸을 표현
; 받은 파라미터 :
;   xpos(edx) = 출력할 콘솔 x좌표
;   ypos(ebx) = 출력할 콘솔 y좌표
;   color(eax) = 칸의 색상
; 반환값 : 없음 (콘솔에 한 칸 출력)
;-----------------------------------------------------
    mov eax, color
    call SetTextColor       ; 색 설정

    mov dl, BYTE PTR xpos
    mov dh, BYTE PTR ypos
    call Gotoxy             ; 커서 이동

    mov edx, OFFSET space
    call WriteString        ; 칸 출력
    ret
DrawSquare ENDP
;-------------------------------------------------------
GetColor PROC USES ebx edx,
    row:DWORD,
    col:DWORD
; (row+col)mod 2를 이용해 칸 색 결정 흰색: 짝, 회색: 홀
; 받은 파라미터 :
;   row : 현재 행 번호
;   col : 현재 열 번호
; 반환값 : EAX = 색상값(흰색 혹은 회색)
;-------------------------------------------------------
    mov eax, row
    add eax, col            ; row+col

    and eax, 1              ; mod 2
    cmp eax, 0              ; 0이면(짝수) 흰색
    jne graySquare          ; 아니면(홀수) 회색

    mov eax, whiteColor
    jmp done
graySquare:
    mov eax, ChangeColor
done:
    ret
GetColor ENDP
END main
```

---
## ★★ 4. FindThrees Procedure  
**Q : Create a procedure named FindThrees that returns 1 if an array has three consecutive values of 3 somewhere in the array. Otherwise, return 0. The procedure’s input parameter list contains a pointer to the array and the array’s size. Use the PROC directive with a parameter list when declaring the procedure. Preserve all registers (except EAX) that are modified by the procedure. Write a test program that calls FindThrees several times with different arrays. **  
**A :** 배열에 3개의 연속된 3이 있다면 1을 반환, 없다면 0을 반환하는 프로시저, 다른 레지스터는 보존하며 3번 작동  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

FindThrees PROTO arrayPtr:DWORD, sizearr:DWORD

.data
    arr1 BYTE 1,3,3,3,4,5,6         ; true
    arr2 BYTE 3,3,2,3,2,3,3,2,2,3   ; false
    arr3 BYTE 1,2,3,4,5,6,5,4,3,2,1 ; false
.code
main PROC
    INVOKE FindThrees, ADDR arr1, SIZEOF arr1
    call DumpRegs
    INVOKE FindThrees, ADDR arr2, SIZEOF arr2
    call DumpRegs
    INVOKE FindThrees, ADDR arr3, SIZEOF arr3
    call DumpRegs
    exit 
main ENDP
;------------------------------------------------------
FindThrees PROC USES ebx ecx edx,
    arrayPtr:DWORD,
    sizearr:DWORD
; 입력 : arrayPtr = 배열 포인터
;        sizearr = 배열 크기
; 반환값 : EAX = (연속된 3개의 3이 있다면)1, (없다면)0
;------------------------------------------------------
    mov ecx, sizearr
    cmp ecx, 3
    jl noThrees         ; 배열 길이 < 3이면 바로 0
    
    xor ebx, ebx        ; 인덱스 i (0으로 초기화)
    mov esi, arrayPtr
checkLoop:
    mov al, [esi+ebx]
    cmp al, 3
    jne nextIndex
    mov al, [esi+ebx+1]
    cmp al, 3
    jne nextIndex
    mov al, [esi+ebx+2]
    cmp al, 3                   ; 3번 연속으로 3인지 확인
    jne nextIndex

    mov eax, 1          ; 있으면 1
    jmp done
nextIndex:
    inc ebx             ; 배열의 다음 인덱스
    mov edx, ecx
    sub edx, 3          ; 마지막으로 3칸 검사가능한 위치
    cmp edx, ebx        ; 검사할 수 있는 인덱스가 3개 이상?
    jg checkLoop        ; 검사
noThrees:
    xor eax, eax        ; 3개 미만으로 남았으면 0
done:
    ret
FindThrees ENDP
END main
```

---
## ★★ 5. DifferentInputs Procedure  
**Q : Write a procedure named DifferentInputs that returns EAX = 1 if the values of its three input parameters are all different; otherwise, return with EAX = 0. Use the PROC directive with a parameter list when declaring the procedure. Create a PROTO declaration for your procedure, and call it five times from a test program that passes different inputs.**  
**A :** A, B, C 3개의 숫자를 받아 모두 다르면 1을 반환하는 프로시저 5번 실행  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

DifferentInputs PROTO pA:DWORD, pB:DWORD, pC:DWORD

.data
    t1 DWORD 1, 2, 3
    t2 DWORD 4, 4, 5
    t3 DWORD 6, 7, 6
    t4 DWORD 8, 9, 9
    t5 DWORD 11, 11, 11
.code
main PROC
    INVOKE DifferentInputs, 1, 2, 3
    call DumpRegs
    INVOKE DifferentInputs, 4, 4, 5
    call DumpRegs
    INVOKE DifferentInputs, 6, 7, 6
    call DumpRegs
    INVOKE DifferentInputs, 8, 9, 9
    call DumpRegs
    INVOKE DifferentInputs, 11, 11, 11
    call DumpRegs
    exit 
main ENDP
;--------------------------------------------------------
DifferentInputs PROC pA:DWORD, pB:DWORD, pC:DWORD
; 3개의 입력 값이 모두 다르면 1을 반환하는 프로시저
; 받은 매개변수 : pA, pB, pC
; 반환값 : 값에따라 1 또는 0
;--------------------------------------------------------
    mov eax, pA
    cmp eax, pB
    je notDiff      ; a=b 이면 0
    cmp eax, pC
    je notDiff      ; a=c 이면 0
    mov eax, pB
    cmp eax, pC
    je notDiff      ; b=c 이면 0

    mov eax, 1      ; 모두 다르면 1
    ret
notDiff:
    xor eax, eax
    ret
DifferentInputs ENDP
END main
```

---
## ★★ 6. Exchanging Integers  
**Q : Create an array of randomly ordered integers. Using the Swap procedure from Section 8.4.6 as a tool, write a loop that exchanges each consecutive pair of integers in the array.**  
**A :** Swap을 이용해 배열의 정수 쌍을 바꾸는 프로시저  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

Swap PROTO pVal1:PTR DWORD, pVal2:PTR DWORD

.data
    arr DWORD 1,2,3,4,5,6,7,8
    n DWORD LENGTHOF arr
.code
main PROC
    mov ecx, n
    shr ecx, 1
    xor ebx, ebx
swapLoop:
    lea esi, arr[ebx*4]
    lea edi, arr[ebx*4+4]
    INVOKE Swap, esi, edi
    add ebx, 2
    loop swapLoop
    xor ebx, ebx
printLoop:
    mov eax, arr[ebx*4]
    call WriteDec
    inc ebx
    cmp ebx, n
    jl printLoop
    exit 
main ENDP
;---------------------------------------------------
Swap PROC USES esi edi,
    pVal1:PTR DWORD,
    pVal2:PTR DWORD
; 두 정수 쌍의 위치를 교환하는 프로시저
;---------------------------------------------------
    mov esi, pVal1
    mov edi, pVal2
    mov eax, [esi]
    xchg eax, [edi]
    mov [esi], eax
    ret
Swap ENDP

END main
```

---
## ★★ 7. Greatest Common Divisor  
**Q : Write a recursive implementation of Euclid’s algorithm for finding the greatest common divisor (GCD) of two integers. Descriptions of this algorithm are available in algebra books and on the Web. Write a test program that calls your GCD procedure five times, using the following pairs of integers: (5,20), (24,18), (11,7), (432,226), (26,13). After each procedure call, display the GCD.**  
**A :** 5쌍의 숫자의 GCD(최대공약수)를 출력하는 프로시저  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

GCD PROTO a:DWORD, b:DWORD

.data
    pair DWORD 5, 20, 24, 18, 11, 7, 432, 226, 26, 13
.code
main PROC
    mov esi, 0
    mov ecx, 5
printLoop:
    mov eax, pair[esi]
    mov ebx, pair[esi+4]
    INVOKE GCD, eax, ebx
    call WriteDec
    call Crlf
    add esi, 8
    loop printLoop
    exit 
main ENDP
;------------------------------------------------
GCD PROC, a:DWORD, b:DWORD
; 두 수의 최대공약수를 반환하는 프로시저
;------------------------------------------------
    
    cmp ebx, 0
    je done         ; b=0이면 GCD=a

    mov eax, a
    mov ebx, b
    mov edx, 0
    div ebx         ; EAX = a/b, EDX = a mod b
    mov eax, ebx
    mov ebx, edx    ; EBX = a mod b
    INVOKE GCD, eax, ebx
    ret
done:
    mov eax, a      ; gcd = a
    ret
GCD ENDP

END main
```

---
## ★★ 8. Counting Matching Elements  
**Q : Write a procedure named CountMatches that receives points to two arrays of signed doublewords, and a third parameter that indicates the length of the two arrays. For each element xi in the first array, if the corresponding yi in the second array is equal, increment a count. At the end, return a count of the number of matching array elements in EAX. Write a test program that calls your procedure and passes pointers to two different pairs of arrays. Use the INVOKE statement to call your procedure and pass stack parameters. Create a PROTO declaration for CountMatches. Save and restore any registers (other than EAX) changed by your procedure.**  
**A :** 두 배열의 동일 인덱스의 값이 같은 개수를 구하는 프로시저  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

CountMatches PROTO array1Ptr:PTR SDWORD,
                    array2Ptr:PTR SDWORD,
                    lenArr:DWORD

.data
    arr1 SDWORD 10, -3, 25, 40, 8, 4, 9, -12
    arr2 SDWORD 10, -3, 20, 40, 7, 4, 10, -12
    len1 DWORD LENGTHOF arr1

    arr3 SDWORD 1,2,3,4,5,6,7,8,9
    arr4 SDWORD 1,0,3,0,5,0,0,0,9
    len2 DWORD LENGTHOF arr3
.code
main PROC
    INVOKE CountMatches, ADDR arr1, ADDR arr2, len1
    call WriteDec
    call Crlf
    INVOKE CountMatches, ADDR arr3, ADDR arr4, len2
    call WriteDec
    call Crlf
    exit 
main ENDP
;-----------------------------------------------------------
CountMatches PROC USES ebx ecx edx,
    array1Ptr:PTR SDWORD,
    array2Ptr:PTR SDWORD,
    lenArr:DWORD
; x[i]==y[i]인 경우의 개수를 세는 프로시저
; 받은 파라미터:
;   array1Ptr = 첫 번째 배열의 주소
;   array2Ptr = 두 번째 배열의 주소
;   length = 배열 크기
; 반환값 : EAX = 일치한 요소 개수
;-----------------------------------------------------------
    xor eax, eax            ; count = 0
    mov ecx, lenArr
    mov ebx, 0
match:
    cmp ecx, 0
    je finish

    mov edx, array1Ptr      ; arr[i]값 가져오기
    mov esi, [edx+ebx*4]

    mov edx, array2Ptr      ; arr[i]값 가져오기
    mov edi, [edx+ebx*4]

    cmp esi, edi
    jne noMatch
    inc eax                 ; 같으면 count++
noMatch:
    inc ebx                 ; 다음 인덱스로 이동
    loop match
finish:
    ret
CountMatches ENDP

END main
```

---
## ★★★ 9. Counting Nearly Matching Elements  
**Q : Write a procedure named CountNearMatches that receives pointers to two arrays of signed doublewords, a parameter that indicates the length of the two arrays, and a parameter that indicates the maximum allowed difference (called diff) between any two matching elements. For each element xi in the first array, if the difference between it and the corresponding yi in the second array is less than or equal to diff, increment a count. At the end, return a count of the number of nearly matching array elements in EAX. Write a test program that calls CountNearMatches and passes pointers to two different pairs of arrays. Use the INVOKE statement to call your procedure and pass stack parameters. Create a PROTO declaration for CountMatches. Save and restore any registers (other than EAX) changed by your procedure.**  
**A :** 8번의 확장. x[i]와 y[i]의 차가 diff 이하인 경우의 개수를 세는 프로시저  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

CountNearMatches PROTO array1Ptr:PTR SDWORD,
                    array2Ptr:PTR SDWORD,
                    lenArr:DWORD,
                    diff:DWORD

.data
    arr1 SDWORD 10, 20, 30, 40, 50, 60, 70, 80
    arr2 SDWORD 12, 18, 29, 45, 56, 57, 61, 74  ; 4
    len1 DWORD LENGTHOF arr1
    d1 DWORD 3

    arr3 SDWORD -5, 100, 23, 44, 60, 80, 123, -222
    arr4 SDWORD -3, 95, 20, 50, 55, 70, 128, -211   ; 5
    len2 DWORD LENGTHOF arr3
    d2 DWORD 5
.code
main PROC
    INVOKE CountNearMatches, ADDR arr1, ADDR arr2, len1, d1
    call WriteDec
    call Crlf
    INVOKE CountNearMatches, ADDR arr3, ADDR arr4, len2, d2
    call WriteDec
    call Crlf
    exit 
main ENDP
;-----------------------------------------------------------
CountNearMatches PROC USES ebx ecx edx esi edi,
    array1Ptr:PTR SDWORD,
    array2Ptr:PTR SDWORD,
    lenArr:DWORD,
    diff:DWORD
; x[i]와y[i]의 차가 diff 이하인 경우의 개수를 세는 프로시저
; 받은 파라미터:
;   array1Ptr = 첫 번째 배열의 주소
;   array2Ptr = 두 번째 배열의 주소
;   length = 배열 크기
; 반환값 : EAX = 일치한 요소 개수
;-----------------------------------------------------------
    xor eax, eax            ; count = 0
    mov ecx, lenArr
    mov ebx, 0
match:
    cmp ecx, 0
    je finish

    mov esi, array1Ptr      ; arr[i]값 가져오기
    mov edx, [esi+ebx*4]

    mov esi, array2Ptr      ; arr[i]값 가져오기
    mov edi, [esi+ebx*4]

    mov esi, edx            ; esi <- x[i]
    sub esi, edi            ; esi = x-y
    cmp esi, 0
    jge absOK
    neg esi                 ; 차가 음수라면 절댓값 변환
absOK:
    cmp esi, diff
    jg noMatch              ; 차가 diff보다 크다면 X
    inc eax                 ; 이하라면 count++
noMatch:
    inc ebx
    loop match
finish:
    ret

CountNearMatches ENDP

END main
```

---
## ★★★★ 10. Show Procedure Parameters  
**Q : Write a procedure named ShowParams that displays the address and hexadecimal value of the 32-bit parameters on the runtime stack of the procedure that called it. The parameters are to be displayed in order from the lowest address to the highest. Input to the procedure will be a single integer that indicates the number of parameters to display. For example, suppose the following statement in main calls MySample, passing three arguments:**  
```asm
INVOKE MySample, 1234h, 5000h, 6543h
```
**Next, inside MySample, you should be able to call ShowParams, passing the number of parameters you want to display:**  
```asm
MySample PROC first:DWORD, second:DWORD, third:DWORD
paramCount = 3
call ShowParams, paramCount
```
**ShowParams should display output in the following format:**  
```
tack parameters:
---------------------------
Address 0012FF80 = 00001234
Address 0012FF84 = 00005000
Address 0012FF88 = 00006543
```
**A :** 자신을 호출한 프로시저의 실행시간 스택에 32비트 매개변수, 16진수 주소 값을 출력하는 ShowParams 프로시저 작성.  
해당 프로시저의 입력은 출력할 매개변수의 개수를 가리키는 정수 한 개. 최하위 주소부터 최상위 주소 순으로 출력.  
[ebp]   = ShowParams의 ebp  
[ebp+4] = ShowParams의 복귀 주소  
[ebp+8] = MySample이 호출했을 때 전달한 매개변수(Count)  
[ebp+12]= MySample의 ebp  
[ebp+16]= MySample의 복귀주소  
이므로 출력해야 할 매개변수의 주소는 [ebp+20] 부터 위로 올라간다 (24...28...)  
코드는 예시를 토대로 작성하였음  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

ShowParams PROTO count:DWORD    ; 출력할 매개변수의 개수 
MySample PROTO first:DWORD,     ; ShowParams를 호출할 프로시저
            second:DWORD,
            third:DWORD

.data
    msgTitle BYTE "Stack parameters:", 0
    msgLine BYTE "---------------------------", 0
    space BYTE " ", 0
    eqStr BYTE "= ", 0
    addrStr BYTE "Address ", 0
.code
main PROC
    INVOKE MySample, 1234h, 5000h, 6543h
    exit 
main ENDP
;-----------------------------------------------------------
MySample PROC first:DWORD, second:DWORD, third:DWORD
; 예제용 호출자
; 받은 파라미터:예제용 파라미터 32비트 정수 3개
; 반환값 : 없음
;-----------------------------------------------------------
    paramCount = 3
    INVOKE ShowParams, paramCount
    ret
MySample ENDP

;-----------------------------------------------------------
ShowParams PROC count:DWORD
; 호출자의 스택에 있는 파라미터 주소와 값을 출력하는 프로시저
; 받은 파라미터 : count = 출력할 파라미터 개수
; 반환값 : 없음(매개변수와 주소값 출력)
;-----------------------------------------------------------
    pushad      ; 스택 프레임 생성 및 모든 레지스터 보존

    mov edx, OFFSET msgTitle
    call WriteString
    call Crlf
    mov edx, OFFSET msgLine
    call WriteString
    call Crlf               ; 위쪽 메시지 출력

    mov ecx, [ebp+8]

    mov esi, ebp
    add esi, 20     ; 출력해야 할 첫 파라미터 주소 = ebp+20
PrintLoop:
    cmp ecx, 0
    je done

    mov edx, OFFSET addrStr
    call WriteString

    mov eax, esi
    call WriteHex   ; 주소 출력
    mov edx, OFFSET space
    call WriteString
    mov edx, OFFSET eqStr
    call WriteString        ; ADDr = val  형태를 위한 출력

    mov eax, [esi]  ; 값을 가져와 출력
    call WriteHex
    call Crlf

    add esi, 4      ; 다음 파라미터
    dec ecx
    jmp PrintLoop
done:
    popad
    ret
ShowParams ENDP
END main
```

---

