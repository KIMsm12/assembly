# Programming Exercises

## ★ 1. 배열 채우기
```asm
.data
arr1 DWORD 10 DUP(?)
arr2 DWORD 10 DUP(?)

.code
FillArray PROC ptrArray:DWORD, N:DWORD, j:DWORD, k:DWORD
    pushad
    mov ecx, N
    mov esi, ptrArray
FillLoop:
    call RandomRange     ; EAX=0~(k-j)
    add eax, j
    mov [esi], eax
    add esi, 4
    loop FillLoop
    popad
    ret
FillArray ENDP

main PROC
    mov eax, OFFSET arr1
    push 10
    push 1
    push 20
    call FillArray

    mov eax, OFFSET arr2
    push 10
    push 50
    push 100
    call FillArray
    exit
main ENDP
```

## ★★ 2. 범위 내 배열 요소 합계
```asm
SumInRange PROC ptrArray:DWORD, N:DWORD, j:DWORD, k:DWORD
    pushad
    mov ecx, N
    mov esi, ptrArray
    xor eax, eax
SumLoop:
    mov edx, [esi]
    cmp edx, j
    jl Skip
    cmp edx, k
    jg Skip
    add eax, edx
Skip:
    add esi, 4
    loop SumLoop
    popad
    ret
SumInRange ENDP

main PROC
    mov eax, OFFSET arr1
    push 10
    push 1
    push 20
    call SumInRange
    exit
main ENDP
```

## ★★ 3. 시험 점수 평가
```asm
CalcGrade PROC score:DWORD
    pushad
    mov eax, score
    cmp eax, 90
    jae GradeA
    cmp eax, 80
    jae GradeB
    cmp eax, 70
    jae GradeC
    cmp eax, 60
    jae GradeD
    cmp eax, 50
    jae GradeE
    mov al, 'F'
    jmp Done
GradeA:
    mov al, 'A'
    jmp Done
GradeB:
    mov al, 'B'
    jmp Done
GradeC:
    mov al, 'C'
    jmp Done
GradeD:
    mov al, 'D'
    jmp Done
GradeE:
    mov al, 'E'
Done:
    popad
    ret
CalcGrade ENDP

main PROC
    mov ecx, 10
GenerateLoop:
    call RandomRange
    add eax, 50
    push eax
    call CalcGrade
    loop GenerateLoop
    exit
main ENDP
```

## ★★ 4. 대학 수강 신청
```asm
; Irvine32 필요
main PROC
    ; 사용자 입력 예시
    call ReadInt
    mov ebx, eax    ; grade average
    call ReadInt
    mov ecx, eax    ; credits

    cmp ecx, 1
    jl CannotRegister
    cmp ecx, 30
    jg CannotRegister
    cmp ebx, 70
    jae CanRegister
CannotRegister:
    ; 메시지 출력: 수강 불가
    call WriteString
    jmp Done
CanRegister:
    ; 메시지 출력: 수강 가능
    call WriteString
Done:
    exit
main ENDP
```

## ★★★ 5. 불리언 계산기 (1)
```asm
Menu PROC
    mov eax, OFFSET MenuText
    call WriteString
    ; 사용자 선택
    call ReadInt
    cmp eax, 1
    je AND_op
    cmp eax, 2
    je OR_op
    cmp eax, 3
    je NOT_op
    cmp eax, 4
    je XOR_op
    cmp eax, 5
    je ExitProgram
    ret
Menu ENDP
```

## ★★★ 6. 불리언 계산기 (2)
```asm
AND_op PROC
    call ReadHex
    mov ebx, eax
    call ReadHex
    and eax, ebx
    call WriteHex
    ret
AND_op ENDP

OR_op PROC
    call ReadHex
    mov ebx, eax
    call ReadHex
    or eax, ebx
    call WriteHex
    ret
OR_op ENDP

NOT_op PROC
    call ReadHex
    not eax
    call WriteHex
    ret
NOT_op ENDP

XOR_op PROC
    call ReadHex
    mov ebx, eax
    call ReadHex
    xor eax, ebx
    call WriteHex
    ret
XOR_op ENDP
```

## ★★ 7. 확률과 색상
```asm
main PROC
    mov ecx, 20
ColorLoop:
    call RandomRange
    cmp eax, 2
    jle White
    cmp eax, 3
    je Blue
    jmp Green
White:
    mov al, WhiteColor
    jmp PrintLine
Blue:
    mov al, BlueColor
    jmp PrintLine
Green:
    mov al, GreenColor
PrintLine:
    ; 텍스트 출력
    loop ColorLoop
    exit
main ENDP
```

## ★★★ 8. 메시지 암호화
```asm
Encrypt PROC ptrMsg:DWORD, ptrKey:DWORD, length:DWORD
    pushad
    mov esi, ptrMsg
    mov edi, ptrKey
    mov ecx, length
    mov ebx, 0
EncryptLoop:
    mov al, [esi]
    mov dl, [edi+ebx]
    xor al, dl
    mov [esi], al
    inc esi
    inc ebx
    cmp ebx, KeyLength
    jl Continue
    mov ebx, 0
Continue:
    loop EncryptLoop
    popad
    ret
Encrypt ENDP
```

## ★★ 9. PIN 검증
```asm
Validate_PIN PROC ptrPIN:DWORD
    pushad
    mov esi, ptrPIN
    mov ecx, 5
    xor ebx, ebx
ValidateLoop:
    mov al, [esi+ebx]
    cmp al, MinArray[ebx]
    jl Invalid
    cmp al, MaxArray[ebx]
    jg Invalid
    inc ebx
    loop ValidateLoop
    xor eax, eax
    jmp Done
Invalid:
    mov eax, ebx
Done:
    popad
    ret
Validate_PIN ENDP
```

## ★★★★ 10. 패리티 체크
```asm
CheckParity PROC ptrArray:DWORD, length:DWORD
    pushad
    mov esi, ptrArray
    mov ecx, length
    xor eax, eax
ParityLoop:
    mov bl, [esi]
    xor al, bl
    inc esi
    loop ParityLoop
    ; EAX LSB 검사
    test al, 1
    jz Even
    xor eax, eax
    jmp Done
Even:
    mov eax, 1
Done:
    popad
    ret
CheckParity ENDP
```