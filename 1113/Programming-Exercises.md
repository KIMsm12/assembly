# Programming Exercises 문제 풀이

## ★ 1. Display ASCII Decimal    
**Q : Write a procedure named WriteScaled that outputs a decimal ASCII number with an implied decimal point. Suppose the following number were defined as follows, where DECIMAL_OFFSET indicates that the decimal point must be inserted five positions from the right side of the number:**  
```asm
DECIMAL_OFFSET = 5
.data
decimal_one BYTE "100123456789765"
```
**WriteScaled would display the number like this:**  
```
1001234567.89765
```
** When calling WriteScaled, pass the number’s offset in EDX, the number length in ECX, and the decimal offset in EBX. Write a test program that passes three numbers of different sizes to the WriteScaled procedure.**  
**A :** ASCII 숫자로 된 문자열을 받아 소수점을 갖는 10진수로 출력하는 WriteScaled 프로시저 작성  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc
DECIMAL_OFFSET = 5     ; 소수점이 오른쪽으로부터 5번째 위치에 삽입
.data
    dec1 BYTE "100123456789765", 0
    dec2 BYTE "12345678", 0
    dec3 BYTE "999999999999", 0
.code
main PROC
    ;첫 번째 숫자
    mov edx, OFFSET dec1            
    mov ecx, LENGTHOF dec1 - 1  ; 개행문자 0 제외
    mov ebx, DECIMAL_OFFSET
    call WriteScaled

    ;두 번째 숫자
    mov edx, OFFSET dec2            
    mov ecx, LENGTHOF dec2 - 1
    mov ebx, 4
    call WriteScaled

    ;세 번째 숫자
    mov edx, OFFSET dec3            
    mov ecx, LENGTHOF dec3 - 1
    mov ebx, 6
    call WriteScaled

    exit 
main ENDP
;---------------------------------------------------
; WriteScaled
; 문자열을 n.m의 형태로 출력
; 입력 : EDX = 숫자 문자열 주소
;        ECX = 숫자 길이
;        EBX = 소수점 위치
; 출력 : 소수점을 갖는 10진수 문자열
;---------------------------------------------------
WriteScaled PROC
    push eax
    push esi

    mov esi, edx
    mov eax, ecx
    sub eax, ebx    ; 소수점 위치 계산
                    ; ecx - ebx = LENGTHOF left
left:
    cmp eax, 0      ; 왼쪽 부분이 끝났다면
    je dot          ; 소수점 삽입

    push eax
    mov al, BYTE PTR[esi]   ; 현재 문자를 al에 로드하여 출력
    call WriteChar
    pop eax
    inc esi         ; 다음 문자로 이동하고
    dec eax         ; 남은 문자 수는 1 감소
    jmp left
dot:
    mov al, '.'
    call WriteChar
right:
    cmp ebx, 0      ; 오른쪽 부분이 끝났다면
    je done         ; 종료
    push eax
    mov al, BYTE PTR[esi]
    call WriteChar
    pop eax
    inc esi
    dec ebx
    jmp right
done:
    call Crlf

    pop esi
    pop eax
    ret
WriteScaled ENDP

END main
```

---
## ★ 2. Extended Subtraction Procedure   
**Q : Create a procedure named Extended_Sub that subtracts two binary integers of arbitrary size. The storage size of the two integers must be the same, and their size must be a multiple of 32 bits. Write a test program that passes several pairs of integers, each at least 10 bytes long.**  
**A :** 32비트의 배수 길이인 숫자쌍들을 받아 뺄셈을 계산하는 프로시저 작성  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
    val1 DWORD 12345678h
    val2 DWORD 11111111h

    val3 DWORD 12345678h, 9ABCDEF0h 
    val4 DWORD 11111111h, 22222222h 

    val5 DWORD 12345678h, 9ABCDEF0h, 00001234h 
    val6 DWORD 11111111h, 22222222h, 00000001h 
.code
main PROC
    ; 32비트 뺄셈
    mov edx, OFFSET val1
    mov esi, OFFSET val2
    mov ecx, SIZEOF val1
    call Extended_Sub

    mov eax, [edx]
    call WriteHex
    call Crlf

    ; 64비트 뺄셈
    mov edx, OFFSET val3
    mov esi, OFFSET val4
    mov ecx, SIZEOF val3
    call Extended_Sub

    mov ecx, 2
    mov edx, OFFSET val3
print64:
    mov eax, [edx]
    call WriteHex
    add edx, 4
    loop print64
    call Crlf

    ;96비트 뺄셈
    mov edx, OFFSET val5
    mov esi, OFFSET val6
    mov ecx, SIZEOF val5
    call Extended_Sub

    mov ecx, 3
    mov edx, OFFSET val5
print96:
    mov eax, [edx]
    call WriteHex
    add edx, 4
    loop print96
    call Crlf

    exit 
main ENDP
;--------------------------------------------------------
; Extended_Sub
; 32비트 단위 뺄셈 수행(A - B)
; 입력 : EDX = A(피감수) 주소
;        ESI = B(피수) 주소
;        ECX = 길이(바이트 단위)
; 출력 : 결과를 피감수 주소에 저장
;--------------------------------------------------------
Extended_Sub PROC
    push edx
Subloop:
    cmp ecx, 0
    je done

    mov eax, [edx]  ; 현재 워드의 피감수
    sbb eax, [esi]  ; 현재 워드의 피수 + CF
    mov [edx], eax  ; 결과를 피감수 주소에 저장

    add edx, 4
    add esi, 4
    sub ecx, 4  ; 다음 워드로 이동
    jmp Subloop
done:
    pop edx
    ret
Extended_Sub ENDP

END main
```

---
## ★★ 3. Packed Decimal Conversion   
**Q : Write a procedure named PackedToAsc that converts a 4-byte packed decimal integer to a string of ASCII decimal digits. Pass the packed integer and the address of a buffer holding the ASCII digits to the procedure. Write a short test program that passes at least 5 packed decimal integers to your procedure.**  
**A :** 4바이트 압축 10진 정수를 받아 ASCII 문자열로 변환하는 프로시저를 작성  
압축 10진 정수는 한 바이트에 10진수 정수 2개를 넣은 형태의 수이다.  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
    pack1 BYTE 12h, 34h, 56h, 78h
    pack2 BYTE 01h, 23h, 45h, 67h
    pack3 BYTE 18h, 27h, 36h, 45h
    pack4 BYTE 11h, 22h, 33h, 44h
    pack5 BYTE 87h, 65h, 43h, 21h

    buffer BYTE 16 DUP(0)
.code
main PROC
    mov edx, OFFSET pack1
    mov edi, OFFSET buffer
    call PackedToAsc
    call WriteString
    call Crlf

    mov edx, OFFSET pack2
    mov edi, OFFSET buffer
    call PackedToAsc
    call WriteString
    call Crlf

    mov edx, OFFSET pack3
    mov edi, OFFSET buffer
    call PackedToAsc
    call WriteString
    call Crlf

    mov edx, OFFSET pack4
    mov edi, OFFSET buffer
    call PackedToAsc
    call WriteString
    call Crlf

    mov edx, OFFSET pack5
    mov edi, OFFSET buffer
    call PackedToAsc
    call WriteString
    call Crlf

    exit 
main ENDP
;----------------------------------------------
; PackedToAsc
; 4바이트 압축 10진 정수를 ASCII 문자열로 변환
; 입력 : EDX = 패킹된 정수 주소
;        EDI = 변환된 ASCII를 저장할 버퍼 주소
; 출력 : 버퍼에 저장된 ASCII 문자열을 반환
;----------------------------------------------
PackedToAsc PROC
    push eax
    push esi
    push ecx

    mov esi, edx        ; 패킹된 정수 주소
    mov ecx, 4          ; 패킹된 바이트 수
    push edi
next_byte:
    mov al, [esi]       ; 현재 바이트 읽어 들이기
    mov ah, al
    shr al, 4           ; AL은 상위 4비트 추출
    and ah, 0Fh         ; AH는 하위 4비트 추출

    add al, '0'         ; ASCII 변환
    add ah, '0'

    mov [edi], al       ; 상위 4비트 문자 저장
    inc edi
    mov [edi], ah       ; 하위 4비트 문자 저장
    inc edi

    inc esi
    loop next_byte

    mov BYTE PTR[edi], 0    ; WriteString 위해 null-terminated 문자열 작성
    pop edi
    mov edx, edi            ; WriteString은 edx를 보고 출력함
    pop ecx
    pop esi
    pop eax
    ret
PackedToAsc ENDP

END main
```

---
## ★★ 4. Encryption Using Rotate Operations   
**Q : Write a procedure that performs simple encryption by rotating each plaintext byte a varying number of positions in different directions. For example, in the following array that represents the encryption key, a negative value indicates a rotation to the left and a positive value indicates a rotation to the right. The integer in each position indicates the magnitude of the rotation:**  
```asm
key BYTE -2, 4, 1, 0, -3, 5, 2, -4, -4, 6
```
**Your procedure should loop through a plaintext message and align the key to the first 10 bytes of the message. Rotate each plaintext byte by the amount indicated by its matching key array value. Then, align the key to the next 10 bytes of the message and repeat the process. Write a program that tests your encryption procedure by calling it twice, with different data sets.**  
**A :** 키 값에 따라 주어진 메시지를 회전하여 암호문을 만드는 프로시저 작성. 두번 호출한다.  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
    key BYTE -2, 4, 1, 0, -3, 5, 2, -4, -4, 6

    m1 BYTE "HELLO WORLD!!!", 0
    len1 = ($ - m1)
    m2 BYTE "SECOND MESSAGE ENCRYPTION", 0
    len2 = ($ - m2)
.code
main PROC
    mov esi, OFFSET m1
    mov ecx, len1
    call EncryptROT
    mov edx, OFFSET m1
    call WriteString
    call Crlf

    mov esi, OFFSET m2
    mov ecx, len2
    call EncryptROT
    mov edx, OFFSET m2
    call WriteString
    call Crlf

    exit 
main ENDP
;-----------------------------------------------------
; EncryptROT
; key 값을 이용, 회전하여 메시지를 암호화하는 프로시저
; 입력: ESI = 암호화할 메시지의 주소
;       ECX = 메시지의 길이
; key값 총 10개를 한 블록으로 반복 사용한다.
; 양수라면 오른쪽, 음수라면 왼쪽으로 숫자만큼 회전
; 출력 : 직접 수정되어 암호문이 된 메시지
;-----------------------------------------------------
EncryptROT PROC
    push ebx
    push edi
    mov edi, 0
EncryptLoop:
    cmp ecx, 0
    je done             ; 남은 길이가 0이라면 종료

    push ecx            ; 회전을 위해 루프 횟수 저장
    mov al, [esi]
    movsx ebx, key[edi]
    cmp ebx, 0
    je Daum            ; 받은 키 값이 0이라면 회전 안하고 패스

    
    cmp ebx, 0
    jl LeftROT          ; 음수라면 왼쪽 회전으로, 양수라면 그대로 진행
RightROT:
    mov cl, bl
    ror al, cl
    jmp Daum            ; 돌렸으면 다음으로
LeftROT:
    mov cl, bl
    neg cl              ; 음수값이니 절댓값 변경
    ror al, cl
    jmp Daum
Daum:
    mov [esi], al
    inc esi
    inc edi             ; 키 값과 메시지 모두 다음 문자로
    pop ecx             ; 루프 횟수 꺼낸 뒤 -1
    dec ecx
    
    cmp edi, 10         ; key는 10개짜리
    jne EncryptLoop     ; 아직 key 인덱스 안이면 반복
    mov edi, 0
    jmp EncryptLoop     ; 인덱스 범위 밖이면 0으로 리셋 후 반복
done:
    pop edi
    pop ebx
    ret
EncryptROT ENDP


END main
```

---
## ★★★ 5. Prime Numbers   
**Q :Write a program that generates all prime numbers between 2 and 1000, using the Sieve of Eratosthenes method. You can find many articles that describe the method for finding primes in this manner on the Internet. Display all the prime values.**  
**A :** 2~1000 사이의 소수를 에라토스테네스의 방법으로 모두 생성하는 프로그램 작성  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
    val BYTE 1001 DUP(1)    ; 0~1000 까지 초기값은 모두 1
    space BYTE ' ', 0

.code
main PROC
    ; 0과 1은 소수가 아님
    mov BYTE PTR val[0], 0
    mov BYTE PTR val[1], 0

    call FindPrime          ; 소수를 구하고
    call PrintPrimes        ; 출력

    exit 
main ENDP
;-------------------------------------------------
; FindPrime
; 에라토스테네스의 체 알고리즘 구현
; 2부터 sqrt(1000)(≒32)까지 반복
; 소수가 아니라면 0으로 표시
; 입력 없음
; 출력 : val 배열에 소수 표시
;-------------------------------------------------
FindPrime PROC
    mov ebx, 2          ; 2의 배수부터 찾아라
NextPRime:
    cmp ebx, 32         ; sqrt(1000) 정도인 32까지 반복
    jge done

    mov al, val[ebx]
    cmp al, 1           ; 지금 숫자가 1이 아니면
    jne EndMulti        ; 소수가 아님-> 배수 제거 루틴 건너뜀

    ; ebx의 배수를 0으로 표시하는 루프
    mov edi, ebx
Multi:
    add edi, ebx        ; 다음 배수
    cmp edi, 1001       ; 배열 범위를 넘어가면 종료
    jge EndMulti
    mov BYTE PTR val[edi], 0
    jmp Multi
EndMulti:
    inc ebx             ; 다음 수도 조사한다
    jmp NextPrime
done:
    ret
FindPrime ENDP
;-------------------------------------------------
; PrintPrimes
; val 배열에서 1로 표시된 소수를 출력하는 프로시저
;-------------------------------------------------
PrintPrimes PROC
    mov esi, OFFSET val
    mov ecx, 1001
    xor edi, edi
PrintLoop:
    mov al, [esi]
    cmp al, 1
    jne NotPrime        ; 1이 아니면 소수가 아님

    mov eax, esi
    sub eax, OFFSET val ; 1인 소수들의 정보가 담긴 주소값 - 배열 시작 주소 = 소수
    call WriteDec
    mov edx, OFFSET space
    call WriteString    ; 소수들은 공백으로 구분

    inc edi
    cmp edi, 10 
    jne NotTen
    call Crlf           ; 소수를 10개 출력 했다면 줄 바꿈
    xor edi, edi
NotTen:
NotPrime:
    inc esi
    loop PrintLoop
    ret
   PrintPrimes ENDP

END main
```

---
## ★★★ 6. Greatest Common Divisor (GCD)   
**Q : The greatest common divisor (GCD) of two integers is the largest integer that will evenly divide both integers. The GCD algorithm involves integer division in a loop, described by the following pseudocode:**  
```pseudocode
int GCD(int x, int y)
    {
       x = abs(x)          // absolute value
       y = abs(y)
       do {
         int n = x % y
         x = y
         y = n
       } while (y > 0)
       return x
   }
```
** Implement this function in assembly language and write a test program that calls the function
 several times, passing it different values. Display all results on the screen.**  
**A :** 위의 의사코드에 따라 두 정수의 최대공약수를 구하는 알고리즘 구현  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
    val DWORD 48, 18, 81, 153, 270, 96, 42, 6
    pair = LENGTHOF val / 2

    msgOpen BYTE "GCD(", 0
    msgComma BYTE ", ", 0
    msgClose BYTE ") = ", 0
.code
main PROC
    mov esi, OFFSET val     ; 현재 정수 쌍의 주소
    mov ecx, pair           ; 반복 횟수(정수쌍의 개수)
TestLoop:
    mov edx, OFFSET msgOpen
    call WriteString

    mov eax, [esi]          ; 첫 번째 정수
    call WriteInt

    mov edx, OFFSET msgComma
    call WriteString

    mov eax, [esi+4]        ; 두 번째 정수
    call WriteInt

    mov edx, OFFSET msgClose
    call WriteString

    mov eax, [esi]
    mov ebx, [esi + 4]
    call GCDP

    call WriteInt
    call Crlf
    add esi, 8              ; 다음 정수 쌍 주소
    loop TestLoop

    exit 
main ENDP
;------------------------------------------------------------
; GCDP
; 두 정수의 최대공약수를 구하는 프로시저
; 입력 : EAX = 첫 번째 정수 x
;        EBX = 두 번째 정수 y
; 출력 : 최대 공약수
;------------------------------------------------------------
GCDP PROC
    cmp eax, 0
    jge AbsXdone
    neg eax                 ; x가 음수라면 절댓값으로
AbsXdone:
    cmp ebx, 0
    jge AbsYdone
    neg ebx                 ; y가 음수라면 절댓값으로
AbsYdone:

    cmp ebx, 0
    jne GCDLoop
    ret                     ; y가 0이면 x가 이미 최대공약수이므로 반환
GCDLoop:
    mov edx, 0              ; 상위 32비트를 0으로 클리어
    div ebx                 ; (EDX : EAX) => 몫은 EAX, 나머지는 EDX
    mov eax, ebx            ; x = y
    mov ebx, edx            ; y = 이전 나머지 값으로 교체
    cmp ebx, 0
    jne GCDLoop             ; 나머지가 0이 될 때까지 반복

    ret                     ; y가 0이면 현재 x가 최대공약수
GCDP ENDP

END main
```

---
## ★★★ 7. Bitwise Multiplication   
**Q : Write a procedure named BitwiseMultiply that multiplies any unsigned 32-bit integer by EAX, using only shifting and addition. Pass the integer to the procedure in the EBX register, and return the product in the EAX register. Write a short test program that calls the procedure and displays the product. (We will assume that the product is never larger than 32 bits.) This is a fairly challenging program to write. One possible approach is to use a loop to shift the multiplier to the right, keeping track of the number of shifts that occur before the Carry flag is set. The resulting shift count can then be applied to the SHL instruction, using the multiplicand as the destination operand. Then, the same process must be repeated until you find the last 1 bit in the multiplier.**  
**A :** 곱셈 명령어 없이 시프트와 덧셈만으로 두 수의 곱셈결과를 출력하는 프로시저 작성  
곱셈 결과는 32비트를 넘지 않는다고 가정하므로 EDX를 간이 저장소로 사용가능  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
    val DWORD 7, 5, 1234, 56, 1, 4096, 255, 255    ; 피승수와 승수의 쌍
    pair = LENGTHOF val / 2

    msgFront BYTE "BitwiseMultiply(", 0
    msgMid BYTE ", ", 0
    msgRear BYTE ") = ", 0
    
.code
main PROC
    mov esi, OFFSET val         ; 현재 정수 쌍
    mov ecx, pair               ; 정수 쌍 개수(반복 횟수)
ShowLoop:                       ; 결과 출력 위한 루프
    mov edx, OFFSET msgFront
    call WriteString

    mov eax, [esi]
    call WriteDec

    mov edx, OFFSET msgMid
    call WriteString

    mov eax, [esi+4]
    call WriteDec

    mov edx, OFFSET msgRear
    call WriteString

    mov eax, [esi]              ; 피승수
    mov ebx, [esi+4]            ; 승수
    call BitwiseMultiply

    call WriteDec
    call Crlf
    add esi, 8                  ; 다음 숫자 쌍으로 이동
    loop ShowLoop

    exit 
main ENDP
;-------------------------------------------------------------------------
; BitwiseMultiply
; shift와 add만 이용한 곱셈을 수행하는 프로시저
; EDX에 덧셈을 누적하여 총 값을 EAX에 전달
; 입력 : EAX = 피승수
;        EBX = 승수
; 출력 : EAX에 곱셈 결과를 저장(결과는 32비트를 넘지 않는다고 가정)
;-------------------------------------------------------------------------
BitwiseMultiply PROC
    xor edx, edx            ; 결과 누적 레지스터 초기화
CheckMul:                   ; (덧셈 누적만 사용할 거임)
    cmp ebx, 0
    je done                 ; 승수가 0이면 더할 게 없음

    test ebx, 1             ; 승수의 LSB가 1인지?
    jz SkipAdd
    add edx, eax            ; LSB가 1이면 피승수를 결과에 더함(부분곱 누적)
SkipAdd:
    shl eax, 1              ; 피승수를 2배로 이동 (다음 비트에 대비)
    shr ebx, 1              ; 승수를 오른쪽으로 이동하여 다음 비트 확인
    jmp CheckMul
done:
    mov eax, edx            ; 최종 결과를 eax에 넣어 반환
    ret
BitwiseMultiply ENDP
    

END main
```

---
## ★★★ 8. Add Packed Integers   
**Q : Extend the AddPacked procedure from Section 7.6.1 so that it adds two packed decimal integers of arbitrary size (both lengths must be the same). Write a test program that passes AddPacked several pairs of integers: 4-byte, 8-byte, and 16-byte. We suggest that you use the following registers to pass information to the procedure:**  
```
ESI - pointer to the first number
EDI - pointer to the second number
EDX - pointer to the sum
ECX - number of bytes to add
```
**A :** 일정 바이트 길이의 압축 10진수 숫자 쌍을 받아 합을 출력하는 프로시저  
4, 8, 16바이트 별로 한쌍씩 준비하여 각각 프로시저를 만들고 계산함  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
    ; -------- 4바이트(8자리) --------
    packed4_A  BYTE 78h,56h,34h,12h          ; 12345678 (리틀 엔디언)
    packed4_B  BYTE 21h,43h,65h,87h          ; 87654321
    sum4       BYTE 5 DUP(0)                 ; 결과+캐리 저장 버퍼

    ; -------- 8바이트(16자리) --------
    packed8_A  BYTE 56h,34h,12h,90h,78h,56h,34h,12h
    packed8_B  BYTE 44h,44h,33h,33h,22h,22h,11h,11h
    sum8       BYTE 9 DUP(0)

    ; -------- 16바이트(32자리) --------
    packed16_A BYTE 78h,56h,34h,12h,90h,78h,56h,34h,12h,90h,78h,56h,34h,12h,90h,78h
    packed16_B BYTE 11h,22h,33h,44h,55h,66h,77h,88h,99h,00h,11h,22h,33h,44h,55h,66h
    sum16      BYTE 17 DUP(0)

    msgCase4   BYTE "== 4바이트 패킷 10진수 ==",0
    msgCase8   BYTE "== 8바이트 패킷 10진수 ==",0
    msgCase16  BYTE "== 16바이트 패킷 10진수 ==",0
    msgOp1     BYTE "첫 번째: ",0
    msgOp2     BYTE "두 번째: ",0
    msgSum     BYTE "합계: ",0
    msgCarry   BYTE "자리올림 1이 추가로 발생했습니다.",0
.code
main PROC
    call RunCase4
    call RunCase8
    call RunCase16

    exit 
main ENDP
;----------------------------------------------
; RunCaseX 
; 각 길이(X)별 테스트
; 입출력 없음
;----------------------------------------------
RunCase4 PROC
    mov edx, OFFSET msgCase4             
    call WriteString
    call Crlf

    mov edx, OFFSET msgOp1
    call WriteString
    mov esi, OFFSET packed4_A            ; 첫 번째 피연산자 시작 주소를 ESI에 적재
    mov ecx, LENGTHOF packed4_A          ; 바이트 수 만큼 루프
    call DisplayPacked
    call Crlf

    mov edx, OFFSET msgOp2
    call WriteString
    mov esi, OFFSET packed4_B            ; 두 번째 피연산자 주소
    mov ecx, LENGTHOF packed4_B
    call DisplayPacked
    call Crlf

    mov esi, OFFSET packed4_A            ; AddPacked 입력: 첫 피연산자 포인터
    mov edi, OFFSET packed4_B            ; AddPacked 입력: 둘째 피연산자 포인터
    mov edx, OFFSET sum4                 ; 결과를 저장할 버퍼 포인터
    mov ecx, LENGTHOF packed4_A          ; 처리할 바이트 수 = 4
    call AddPacked                       ; 패킷 덧셈 실행 (CF에 최종 자리올림이 남음)

    setc al                              ; CF→AL 복사 (자리올림 여부 저장)
    mov sum4[LENGTHOF packed4_A], al     ; sum4 배열의 마지막 바이트에 캐리값 기록

    mov edx, OFFSET msgSum
    call WriteString
    mov esi, OFFSET sum4                 ; 결과 표시 시에는 sum 버퍼 주소를 전달
    mov ecx, LENGTHOF packed4_A
    call DisplayPacked
    call Crlf

    cmp al, 0
    je  NoCarry4
    mov edx, OFFSET msgCarry
    call WriteString
    call Crlf
NoCarry4:
    call Crlf
    ret
RunCase4 ENDP

RunCase8 PROC
    mov edx, OFFSET msgCase8
    call WriteString
    call Crlf

    mov edx, OFFSET msgOp1
    call WriteString
    mov esi, OFFSET packed8_A
    mov ecx, LENGTHOF packed8_A
    call DisplayPacked
    call Crlf

    mov edx, OFFSET msgOp2
    call WriteString
    mov esi, OFFSET packed8_B
    mov ecx, LENGTHOF packed8_B
    call DisplayPacked
    call Crlf

    mov esi, OFFSET packed8_A
    mov edi, OFFSET packed8_B
    mov edx, OFFSET sum8
    mov ecx, LENGTHOF packed8_A
    call AddPacked

    setc al
    mov sum8[LENGTHOF packed8_A], al

    mov edx, OFFSET msgSum
    call WriteString
    mov esi, OFFSET sum8
    mov ecx, LENGTHOF packed8_A
    call DisplayPacked
    call Crlf

    cmp al, 0
    je  NoCarry8
    mov edx, OFFSET msgCarry
    call WriteString
    call Crlf
NoCarry8:
    call Crlf
    ret
RunCase8 ENDP

RunCase16 PROC
    mov edx, OFFSET msgCase16
    call WriteString
    call Crlf

    mov edx, OFFSET msgOp1
    call WriteString
    mov esi, OFFSET packed16_A
    mov ecx, LENGTHOF packed16_A
    call DisplayPacked
    call Crlf

    mov edx, OFFSET msgOp2
    call WriteString
    mov esi, OFFSET packed16_B
    mov ecx, LENGTHOF packed16_B
    call DisplayPacked
    call Crlf

    mov esi, OFFSET packed16_A
    mov edi, OFFSET packed16_B
    mov edx, OFFSET sum16
    mov ecx, LENGTHOF packed16_A
    call AddPacked

    setc al
    mov sum16[LENGTHOF packed16_A], al

    mov edx, OFFSET msgSum
    call WriteString
    mov esi, OFFSET sum16
    mov ecx, LENGTHOF packed16_A
    call DisplayPacked
    call Crlf

    cmp al, 0
    je  NoCarry16
    mov edx, OFFSET msgCarry
    call WriteString
    call Crlf
NoCarry16:
    call Crlf
    ret
RunCase16 ENDP
;-------------------------------------------------------------
; AddPacked
; 바이트마다 add/adc + daa를 수행하는 프로시저
;-------------------------------------------------------------
AddPacked PROC
    clc                                   ; 첫 바이트 연산 전에 CF=0으로 초기화
AddLoop:
    mov al, BYTE PTR [esi]                ; AL ← 첫 번째 수의 현재 바이트 (두 자리)
    adc al, BYTE PTR [edi]                ; 동일 바이트 + 이전 자리올림(CF) 더하기
    daa                                   ; 10진 자리 보정, CF는 새 자리올림
    mov BYTE PTR [edx], al                ; 보정된 두 자리 값을 버퍼에 저장
    inc esi                               ; 첫 번째 수 포인터를 다음 바이트로 이동
    inc edi                               ; 두 번째 수 포인터도 다음 바이트
    inc edx                               ; 결과 버퍼 포인터도 다음 바이트
    loop AddLoop                          ; ECX 카운터가 0이 될 때까지 반복
    ret                                   ; CF에는 마지막 자리올림이 남아 있음
AddPacked ENDP
;-----------------------------------------------------
; DisplayPacked
; BCD를 사람이 읽는 10진 문자열로 출력하는 프로시저
;-----------------------------------------------------
DisplayPacked PROC
    pushad                                ; 호출자 보존용으로 모든 GPR 저장

    mov ebx, ecx                          ; EBX ← 총 바이트 수 (루프 개수 저장)
    add esi, ecx                          ; 포인터를 최상위 바이트 바로 뒤로 이동
    dec esi                               ; 실제 최상위 바이트 위치로 되돌림
    xor edi, edi                          ; EDI=0 → 아직 출력한 숫자가 없음을 표시 

NextByte:
    mov al, [esi]                         ; AL에 현재 바이트(두 자리) 로드
    mov ah, al                            ; AH에 백업, 상/하위 니블 따로 쓰기 위해 보관

    mov dl, ah
    shr dl, 4                             ; DL ← 상위 니블(더 높은 자리)
    cmp dl, 0
    jne PrintHigh
    cmp edi, 0
    jne PrintHigh
    jmp SkipHigh                          ; 아직 숫자 출력 전이고 니블이 0이면 생략

PrintHigh:
    cmp dl, 9
    ja  SkipHigh                          ; (오류 방지) 0~9 범위만 출력
    mov edi, 1                            ; 실제 숫자 하나 출력했음을 기록
    mov al, dl
    add al, '0'                           ; 10진 문자로 변환
    call WriteChar

SkipHigh:
    mov dl, ah
    and dl, 0Fh                           ; DL ← 하위 니블(더 낮은 자리)
    cmp dl, 0
    jne PrintLow
    cmp edi, 0
    jne PrintLow
    jmp SkipLow                           ; 상위와 마찬가지로 선행 0이면 생략

PrintLow:
    cmp dl, 9
    ja  SkipLow
    mov edi, 1
    mov al, dl
    add al, '0'
    call WriteChar

SkipLow:
    dec esi                               ; 이전 바이트(다음 상위 자리)로 이동
    dec ecx                               ; 처리한 바이트 수 만큼 카운터 감소
    jnz NextByte                          ; 모든 바이트를 다 출력할 때까지 반복

    cmp edi, 0
    jne DisplayDone
    mov al, '0'                           ; 전체가 0이라면 0 하나 출력
    call WriteChar
DisplayDone:
    popad
    ret
DisplayPacked ENDP
END main
```

---