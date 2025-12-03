# Programming Exercises 문제 풀이
## ★ 1. Improved Str_copy Procedure  
**Q : The Str_copy procedure shown in this chapter does not limit the number of characters to be copied. Create a new version (named Str_copyN) that receives an additional input parameter indicating the maximum number of characters to be copied.**  
**A :** 복사되는 문자 개수를 파라미터로 받는 Str_copyN 만들기(Str_copy를 따라)  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

Str_copyN PROTO, source:PTR BYTE, target:PTR BYTE, maxCount:DWORD

.data
    sourceStr1 BYTE "Hello Assembly World", 0
    sourceStr2 BYTE "Short", 0

    targetStr1 BYTE 50 DUP(?)
    targetStr2 BYTE 50 DUP(?)

    maxD DWORD 10

.code
main PROC
    INVOKE Str_copyN, ADDR sourceStr1, ADDR targetStr1, maxD
    mov edx, OFFSET targetStr1
    call WriteString
    call Crlf

    INVOKE Str_copyN, ADDR sourceStr2, ADDR targetStr2, maxD
    mov edx, OFFSET targetStr2
    call WriteString
    call Crlf
    exit 
main ENDP
;--------------------------------------------------------
Str_copyN PROC USES eax ecx esi edi,
    source:PTR BYTE,
    target:PTR BYTE,
    maxCount:DWORD
; 받은 파라미터 :
;    source : 원본 문자열
;    target : 복사할 목적지 문자열
;    maxCount : 복사할 최대 문자 개수
; source로부터 최대 maxCount만큼 문자를 복사하는 프로시저
;--------------------------------------------------------
    INVOKE Str_length, source
    mov ecx, eax
    mov edx, maxCount

    cmp ecx, edx    ; ecx <= maxCount ?
    jbe SizeOK      ; 문자열의 길이가 더 짧거나 같으면 그대로 사용
    mov ecx, edx    ; 더 길다면 maxCount 까지만 복사
SizeOK:
    mov esi, source
    mov edi, target
    cld
    rep movsb
    mov BYTE PTR[edi], 0
    ret 12
Str_copyN ENDP
END main
```

---
## ★★ 2. Str_concat Procedure  
**Q : Write a procedure named Str_concat that concatenates a source string to the end of a target string. Sufficient space must exist in the target string to accommodate the new characters. Pass pointers to the source and target strings. Here is a sample call:**  
```asm
.data
targetStr BYTE "ABCDE",10 DUP(0)
sourceStr BYTE "FGH",0
.code
INVOKE Str_concat, ADDR targetStr, ADDR sourceStr
```
**A :** 충분한 크기의 target 문자열 뒤에 source 문자열을 이어붙이는 프로시저 Str_concat  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

Str_concat PROTO, pTarget:PTR BYTE, pSource:PTR BYTE

.data
    targetStr BYTE "ABCDE", 10 DUP(0)
    sourceStr BYTE "FGH", 0

.code
main PROC
    INVOKE Str_concat, ADDR targetStr, ADDR sourceStr
    mov edx, OFFSET targetStr
    call WriteString
    call Crlf
    exit 
main ENDP
;---------------------------------------------------------
Str_concat PROC USES eax ecx esi edi,
    pTarget:PTR BYTE,
    pSource:PTR BYTE
; target 문자열 끝에 source 문자열을 이어 붙이는 프로시저
; 받은 파라미터 :
;   pTarget = 충분한 공간을 가진 문자열
;   pSource = pTarget 뒤에 이어붙일 문자열
;---------------------------------------------------------
    INVOKE Str_length, pTarget
    mov edi, pTarget
    add edi, eax    ; target 끝위치로 이동

    INVOKE Str_length, pSource
    mov ecx, eax
    inc ecx         ; 널 문자 포함하여 source 길이만큼 복사준비

    mov esi, pSource
    cld
    rep movsb
    ret 8
Str_concat ENDP
END main
```

---
## ★★ 3. Str_remove Procedure  
**Q : Write a procedure named Str_remove that removes n characters from a string. Pass a pointer to the position in the string where the characters are to be removed. Pass an integer specifying the number of characters to remove. The following code, for example, shows how to remove “xxxx” from target:**  
```asm
.data
target BYTE "abcxxxxdefghijklmop",0
.code
INVOKE Str_remove, ADDR [target+3], 4
```
**A :** 문자열에서 n개의 문자를 제거하는 Str_remove 프로시저  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

Str_remove PROTO, pStart:PTR BYTE, count:DWORD

.data
    target BYTE "abcxxxxdefghijklmop", 0
    msg BYTE "변경 후 => ", 0
.code
main PROC
    mov edx, OFFSET target
    call WriteString
    call Crlf
    INVOKE Str_remove, ADDR target+3, 4
    mov edx, OFFSET msg
    call WriteString
    mov edx, OFFSET target
    call WriteString
    call Crlf
    exit 
main ENDP
;-------------------------------------------------------
Str_remove PROC USES eax ecx esi edi,
    pStart:PTR BYTE,
    count:DWORD
; target에서 주어진 위치의 문자를 count개 삭제하는 프로시저
; 받은 파라미터 :
;   pStart = 삭제할 문자의 첫 위치
;   count = 삭제할 문자의 개수
; 반환값 : 없음 (삭제 후 이어붙인 문자열 target)
;-------------------------------------------------------
    mov esi, pStart
    mov eax, count
    add esi, eax        ; ESI = pStart + count

    INVOKE Str_length, esi
    mov ecx, eax
    inc ecx             ; null 문자를 포함한 남은 문자열
    
    mov edi, pStart
    cld
    rep movsb           ; count만큼 끌어당겨서 작성

    ret 8
Str_remove ENDP
END main
```

---
## ★★★ 4. Str_find Procedure  
**Q : Write a procedure named Str_find that searches for the first matching occurrence of a source string inside a target string and returns the matching position. The input parameters should be a pointer to the source string and a pointer to the target string. If a match is found, the procedure sets the Zero flag and EAX points to the matching position in the target string. Otherwise, the Zero flag is clear and EAX is undefined. The following code, for example, searches for “ABC” and returns with EAX pointing to the “A” in the target string:**  
```asm
.data
target BYTE "123ABC342432",0
source BYTE "ABC",0
pos    DWORD ?
.code
INVOKE Str_find, ADDR source, ADDR target
jnz notFound
mov pos,eax                      ; store the position value
```
**A :** target안에서 source 문자열이 처음으로 나타나는 곳을 찾아 그 위치를 반환하는 프로시저  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

Str_find PROTO, pSource:PTR BYTE, pTarget:PTR BYTE

.data
    target BYTE "123ABC342432", 0
    source BYTE "ABC", 0
    pos DWORD ?
    msgF BYTE "찾았습니다. 주소는 : ", 0
    msgNF BYTE "찾지 못했습니다.", 0
.code
main PROC
    INVOKE Str_find, ADDR source, ADDR target
    jnz NFound
FoundS:
    mov pos, eax
    mov edx, OFFSET msgF
    call WriteString
    call WriteHex
    jmp done
NFound:
    mov edx, OFFSET msgNF
    call WriteString   
done:
    call Crlf
    exit
main ENDP
;-------------------------------------------------------
Str_find PROC USES esi edi ecx,
    pSource:PTR BYTE,
    pTarget:PTR BYTE
; 받은 파라미터 :
;   pTarget = 탐색할 문자열
;   pSource = 검색어가 담긴 문자열
; 반환값 :
;   찾으면 ZF = 1, EAX = 찾은 문자열의 주소
;   못찾으면 ZF = 0, EAX = 정의되지 않음
;-------------------------------------------------------
    mov esi, pSource
    mov edi, pTarget
SearchLoop:
    mov al, [edi]
    cmp al, 0
    je NotFound     ; target이 끝났으면 탐색 실패

    mov al, [edi]
    mov dl, [esi]
    cmp al, dl
    jne NextChar    ; 첫 글자가 불일치하면 다음 문자로

    push edi
    push esi        ; 일치하면 일치테스트 시작
    INVOKE Str_length, esi
    mov ecx, eax

    pop esi
    mov ebx, esi    ; 불일치를 대비해 source 시작값 저장
    pop edi
    repe cmpsb      ; source 와 target 비교
    jecxz Found     ; ecx == 0 이라면 전체 일치

    mov esi, ebx
NextChar:
    inc edi         ; 다음 문자로 이동
    jmp SearchLoop
Found:
    INVOKE Str_length, pSource
    sub edi, eax    ; edi는 비교 후 +1, 시작 위치는 edi - pSource length임
    mov eax, edi    ; eax에는 검색어의 시작 주소값
    cmp eax, eax    ; ZF = 1
    ret 8
NotFound:
    cmp eax, 0      ; ZF = 0
    ret 8   
Str_find ENDP
END main
```

---
## ★★ 5. Str_nextWord Procedure  
**Q : Write a procedure called Str_nextWord that scans a string for the first occurrence of a certain delimiter character and replaces the delimiter with a null byte. There are two input parameters: a pointer to the string and the delimiter character. After the call, if the delimiter was found, the Zero flag is set and EAX contains the offset of the next character beyond the delimiter. Otherwise, the Zero flag is clear and EAX is undefined. The following example code passes the address of target and a comma as the delimiter:**  
```asm
.data
target BYTE "Johnson,Calvin",0
.code
INVOKE Str_nextWord, ADDR target, ','
jnz notFound
```
**In Figure 9-5, after calling Str_nextWord, EAX points to the character following the position where the comma was found (and replaced). **  
<img width="446" height="144" alt="image" src="https://github.com/user-attachments/assets/a03a1245-6dd8-4ab8-be12-91f91d75f1cf" />  
**A :** 문자열 내 분리 문자를 찾아 널 문자로 바꾸고 뒤를 반환하는 프로시저  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

Str_nextWord PROTO, pStr:PTR BYTE, delim:BYTE

.data
    target BYTE "Johnson,Calvin", 0
    msgF BYTE "분리 문자 발견", 0
    msgNF BYTE " 분리 문자 미발견", 0
.code
main PROC
    INVOKE Str_nextWord, ADDR target, ','
    jnz NFound
FoundS:
    mov edx, OFFSET msgF
    call WriteString
    call Crlf
    mov edx, eax
    call WriteString
    jmp done
NFound:
    mov edx, OFFSET msgNF
    call WriteString
done:
    call Crlf
    exit
main ENDP
;-------------------------------------------------------
Str_nextWord PROC USES esi edi,
    pStr:PTR BYTE,
    delim:BYTE
; 받은 파라미터 :
;   pStr = 탐색할 문자열
;   delim = 분리 확인 문자
; 반환값 :
;   찾으면 ZF = 1, EAX = 분리 문자의 다음 오프셋
;   못찾으면 ZF = 0, EAX = 정의되지 않음
;-------------------------------------------------------
    mov esi, pStr
    mov al, delim
    mov ecx, 0FFFFFFFFh         ; 충분히 큰 값
                                ; null 값을 만나면 멈추니 Str_length대신 사용가능

    mov edi, esi
    cld
    repne scasb                 ; al(분리 문자)과 문자열 비교, 일치할 때까지
    jz Found                    ; 찾았다면 Found로
    cmp eax, 0                  ; 못 찾았으면 ZF = 0
    ret 8
Found:
    lea eax, [edi-1+1]          ; 찾았다면 eax에 분리 문자 다음 주소 저장
    mov BYTE PTR[edi-1], 0      ; 분리 문자 위치에는 널 문자 대입
    cmp eax, eax                ; ZF = 1
    ret 8
Str_nextWord ENDP
END main
```

---
## ★★ 6. Constructing a Frequency Table  
**Q :  Write a procedure named Get_frequencies that constructs a character frequency table. Input to the procedure should be a pointer to a string and a pointer to an array of 256 doublewords initialized to all zeros. Each array position is indexed by its corresponding ASCII code. When the procedure returns, each entry in the array contains a count of how many times the corresponding character occurred in the string. For example,**  
```asm
.data
target BYTE "AAEBDCFBBC",0
freqTable DWORD 256 DUP(0)
.code
INVOKE Get_frequencies, ADDR target, ADDR freqTable
```
**Figure 9-6 shows a picture of the string and entries 41 (hexadecimal) through 4B in the frequency table. Position 41 contains the value 2 because the letter A (ASCII code 41h) occurred twice in the string. Similar counts are shown for other characters. Frequency tables are useful in data compression and other applications involving character processing. The Huffman encoding algorithm, for example, stores the most frequently occurring characters in fewer bits than other characters that occur less often.**  
<img width="464" height="128" alt="image" src="https://github.com/user-attachments/assets/c6a043e9-b07e-4324-93de-8c21f64bc419" />  
**A :** 문자열에 나온 문자의 빈도수를 기록하여 빈도표를 만드는 프로시저  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

Get_frequencies PROTO, pString:PTR BYTE, pTable:PTR DWORD
Print_frequencies PROTO

.data
    target BYTE "AAEBDCFBBC", 0
    freqTable DWORD 256 DUP(0)
    msgResult BYTE "ASCII Code: ",0
    msgCount  BYTE "  Count: ",0
.code
main PROC
    INVOKE Get_frequencies, ADDR target, ADDR freqTable
    INVOKE Print_frequencies
    exit
main ENDP
;---------------------------------------------------------------
Get_frequencies PROC USES esi edi eax,
    pString:PTR BYTE,
    pTable:PTR DWORD
; 받은 파라미터 :
;   pString = 처리할 문자열의 주소
;   pTable = 크기 256의 빈도를 나타낼 테이블 주소
; 반환값 : 없음
; 문자열 내 각 문자의 빈도를 계산하여 Table에 기록하는 프로시저
;---------------------------------------------------------------
    mov esi, pString
    mov edi, pTable
    cld
ProcessLoop:
    lodsb
    cmp al, 0       ; 현재 문자가 널 문자라면 종료
    je done

    movzx eax, al           ; 배열 인덱싱 위해 확장
    shl eax, 2              ; Table은 DWORD이므로 x4
    inc DWORD PTR[edi+eax]  ; ASCII 값에 해당하는 주소
    jmp ProcessLoop
done:
    ret 8
Get_frequencies ENDP
;---------------------------------------------------------------
Print_frequencies PROC USES eax ebx ecx esi edx
; 그림 예시와 같이 ASCII 41h ~ 4Bh 까지 출력하는 프로시저
;---------------------------------------------------------------
    mov ecx, 4Bh - 41h + 1      ; 총 출력 개수
    mov esi, 41h                ; 시작은 'A' 부터
    mov ebx, OFFSET freqTable
PrintLoop:
    mov edx, OFFSET msgResult
    call WriteString

    mov eax, esi
    call WriteHex         ; ASCII 코드 HEX 출력

    mov edx, OFFSET msgCount
    call WriteString

    mov eax, [ebx + esi*4]
    call WriteDec         ; 빈도 출력

    call Crlf

    inc esi
    loop PrintLoop

    ret
Print_frequencies ENDP
END main
```

---
## ★★★ 7. Sieve of Eratosthenes  
**Q : The Sieve of Eratosthenes, invented by the Greek mathematician of the same name, provides a quick way to find all prime numbers within a given range. The algorithm involves creating an array of bytes in which positions are “marked” by inserting 1s in the following manner: Beginning with position 2 (which is a prime number), insert a 1 in each array position that is a multiple of 2. Then do the same thing for multiples of 3, the next prime number. Find the next prime number after 3, which is 5, and mark all positions that are multiples of 5. Proceed in this manner until all multiples of primes have been found. The remaining positions of the array that are unmarked indicate which numbers are prime. For this program, create a 65,000-element array and display all primes between 2 and 65,000. Declare the array in an uninitialized data segment (see Section 3.4.11) and use STOSB to fill it with zeros. **  
**A :** 2~65000 사이의 소수를 출력하는 프로시저(비초기화 데이터 선언으로 배열 만들기)  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

InitArray PROTO, pArray:PTR BYTE, count:DWORD
Sieve PROTO, pArray:PTR BYTE, count:DWORD
PrintPrimes PROTO, pArray:PTR BYTE, count:DWORD

.data?
    sieveArray BYTE 65000 DUP(?)    ; 비초기화 데이터 선언
.data
    msgTitle BYTE "2~65000 사이의 소수: ", 0
    space BYTE ' ', 0
.code
main PROC
    INVOKE InitArray, ADDR sieveArray, 65000
    INVOKE Sieve, ADDR sieveArray, 65000
    INVOKE PrintPrimes, ADDR sieveArray, 65000
    exit
main ENDP
;---------------------------------------------------------------
InitArray PROC USES edi ecx eax,
    pArray:PTR BYTE,
    count:DWORD
; 배열 전체를 0으로 초기화하는 프로시저
;---------------------------------------------------------------
    mov edi, pArray
    mov ecx, count
    xor eax, eax        ; al = 0
    cld
FillLoop:
    stosb               ; [edi] <= al(0)
    loop FillLoop
    ret 8
InitArray ENDP
;---------------------------------------------------------------
Sieve PROC USES esi edi ebx ecx eax,
    pArray:PTR BYTE,
    count:DWORD
; 에라토스테네스의 체를 구현하여 소수를 찾는 프로시저
; 반환값 : 없음(소수가 아닌 수는 인덱스 값을 1로 변경)
;---------------------------------------------------------------
    mov esi, pArray
    mov ebx, 2          ; 2부터 탐색
OuterLoop:
    cmp ebx, count
    jae EndSieve        ; ebx >= count라면 종료

    mov al, [esi+ebx]
    cmp al, 0
    jne SkipPrime       ; 1이면 이미 마크됨

    mov edi, esi        ; edi = 배열 시작
    mov ecx, ebx        ; ecx는 ebx의 배수
    add ecx, ebx
Mark:
    cmp ecx, count
    jae SkipPrime

    mov BYTE PTR[edi+ecx], 1    ; 모든 배수는 마크
    add ecx, ebx                ; 다음 배수로 이동
    jmp Mark
SkipPrime:
    inc ebx
    jmp OuterLoop
EndSieve:
    ret 8
Sieve ENDP
;---------------------------------------------------------------
PrintPrimes PROC USES esi ecx eax edx ebx,
    pArray:PTR BYTE,
    count:DWORD
; 배열에서 값이 0인 위치(소수)를 모두 출력하는 프로시저
;---------------------------------------------------------------
    mov esi, pArray
    mov ecx, count
    mov edx, OFFSET msgTitle
    call WriteString
    call Crlf

    mov eax, 2          ; 소수 출력은 2부터
    mov ebx, 0
PrintLoop:
    cmp eax, count
    jae EndPrint

    cmp BYTE PTR[esi+eax], 0    ; 배열값이 0이면 소수
    jne ContinuePrint

    call WriteDec
    mov edx, OFFSET space
    call WriteString
    
    inc ebx
    cmp ebx, 20
    jne ContinuePrint
    call Crlf           ; 20개 출력했다면 줄바꿈
    mov ebx, 0
ContinuePrint:
    inc eax
    jmp PrintLoop
EndPrint:
    call Crlf
    ret 8
PrintPrimes ENDP
END main
```

---
## ★ 8. Bubble Sort  
**Q : Add a variable to the BubbleSort procedure in Section 9.5.1 that is set to 1 whenever a pair of values is exchanged within the inner loop. Use this variable to exit the sort before its normal completion if you discover that no exchanges took place during a complete pass through the array. (This variable is commonly known as an exchange flag.)**  
**A :** 교환 플래그 변수를 추가해 불필요한 반복을 줄여 성능을 최적화한 버블 정렬  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

BubbleSort PROTO, pArray:PTR DWORD, count:DWORD
PrintArray PROTO, pArray:PTR DWORD, count:DWORD

.data
    array DWORD 7, 3, 9, 2, 8, 5, 1
    arrayCount DWORD LENGTHOF array

    excgFlag BYTE 0
    msgBefore BYTE "정렬 전 배열:", 0
    msgAfter BYTE "정렬 후 배열:", 0
    space BYTE ' ', 0
.code
main PROC
    mov edx, OFFSET msgBefore
    call WriteString
    mov edx, OFFSET space
    call WriteString
    INVOKE PrintArray, ADDR array, arrayCount

    INVOKE BubbleSort, ADDR array, arrayCount
    call Crlf
    
    mov edx, OFFSET msgAfter
    call WriteString
    mov edx, OFFSET space
    call WriteString
    INVOKE PrintArray, ADDR array, arrayCount
    exit
main ENDP
;---------------------------------------------------------------
PrintArray PROC USES esi ecx eax,
    pArray:PTR DWORD,
    count:DWORD
; 받은 파라미터:
;   pArray = 배열의 시작 주소
;   count = 배열의 길이
; 배열을 출력하는 프로시저
;---------------------------------------------------------------
    mov esi, pArray
    mov ecx, count
L1:
    mov eax, [esi]
    call WriteInt
    mov edx, OFFSET space
    call WriteString

    add esi, 4
    loop L1
    ret 8
PrintArray ENDP
;---------------------------------------------------------------
BubbleSort PROC USES eax ecx esi,
    pArray:PTR DWORD,
    count:DWORD
; 받은 파라미터:
;   pArray = 배열의 시작 주소
;   count = 배열의 길이
; 교환 플래그를 이용한 개선된 버블정렬 프로시저
;---------------------------------------------------------------
    mov ecx, Count
    dec ecx
L1:
    push ecx
    mov excgFlag, 0         ; 교환 플래그는 0
    mov esi, pArray
L2:
    mov eax, [esi]
    cmp [esi+4], eax
    jg NoSwap               ; 다음 값이 더 크다면 교환 없음

    xchg eax, [esi+4]       ; 교환 발생시
    mov [esi], eax
    mov excgFlag, 1         ; 교환 플래그 1
NoSwap:
    add esi, 4
    loop L2
    pop ecx

    cmp excgFlag, 0
    je done                 ; 한바퀴 돌았을 때 교환 플래그가 0이라면 종료

    loop L1
done:
    ret 8
BubbleSort ENDP
END main
```

---
## ★★ 9. Binary Search  
**Q : Rewrite the binary search procedure shown in this chapter by using registers for mid, first, and last. Add comments to clarify the registers’ usage.**  
**A :** 레지스터만을 이용하여 표현한 이진탐색 알고리즘  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

BinarySearch PROTO, pArray:PTR DWORD, count:DWORD, searchVal:DWORD
PrintArray PROTO, pArray:PTR DWORD, count:DWORD

.data
    array DWORD 1, 3, 5, 7, 9, 11, 13, 15
    arrayCount DWORD LENGTHOF array
    Val DWORD 11
    space BYTE ' ', 0
    mArray BYTE "배열 내용: ", 0
    mFind BYTE "검색 값 위치: ", 0
    mFail BYTE "값을 찾지 못함", 0
.code
main PROC
    mov edx, OFFSET mArray
    call WriteString
    INVOKE PrintArray, ADDR array, arrayCount
    call Crlf
    INVOKE BinarySearch, ADDR array, arrayCount, Val

    cmp eax, -1
    je NotFound

    mov edx, OFFSET mFind
    call WriteString
    call WriteInt
    call Crlf
    jmp done
NotFound:
    mov edx, OFFSET mFail
    call WriteString
    call Crlf
done:
    exit
main ENDP
;---------------------------------------------------------------
PrintArray PROC USES esi ecx eax,
    pArray:PTR DWORD,
    count:DWORD
; 받은 파라미터:
;   pArray = 배열의 시작 주소
;   count = 배열의 길이
; 배열을 출력하는 프로시저
;---------------------------------------------------------------
    mov esi, pArray
    mov ecx, count
L1:
    mov eax, [esi]
    call WriteInt
    mov edx, OFFSET space
    call WriteString

    add esi, 4
    loop L1
    ret 8
PrintArray ENDP
;---------------------------------------------------------------
BinarySearch PROC USES ebx ecx edx esi edi,
    pArray:PTR DWORD,
    count:DWORD,
    searchVal:DWORD
; 이진 탐색을 수행하는 프로시저 (레지스터만을 사용하여)
; 받은 파라미터:
;   pArray = 배열의 시작 주소
;   count = 배열의 길이
;   searchVal = 찾을 값
; 반환값 : EAX = 찾은 인덱스, 못 찾았다면 -1
;---------------------------------------------------------------
    mov ebx, pArray             ; EBX = 배열 시작 주소
    mov esi, 0                  ; ESI = first = 0
    mov eax, count
    dec eax
    mov edi, eax                ; EDI = last = count - 1
L1:
    cmp esi, edi                ; first <= last 일 동안 수행
    jg NotFound

    mov edx, esi                ; EDX = mid
    add edx, edi                ; mid = (first + last)
    shr edx, 1                  ;           / 2

    mov ecx, edx
    shl ecx, 2                  ; 인덱스 x4
    mov eax, [ebx+ecx]
    cmp eax, searchVal
    jl GoRight                  ; array[mid] < Val 이면 오른쪽,
    jg GoLeft                   ; array[mid] > Val 이면 왼쪽 탐색

    mov eax, edx                ; 동일하다면 찾은것, 인덱스를 전달
    ret 12
GoRight:
    mov esi, edx
    inc esi                     ; esi(first) = mid(edx) + 1
    jmp L1
GoLeft:
    mov edi, edx
    dec edi                     ; edi(last)  = mid(edx) - 1
    jmp L1
NotFound:
    mov eax, -1                 ; 발견 못했으면 -1 반환
    ret 12
BinarySearch ENDP
END main
```

---
## ★★★ 10. Letter Matrix  
**Q : Create a procedure that generates a four-by-four matrix of randomly chosen capital letters. When choosing the letters, there must be a 50% probability that the chosen letter is a vowel. Write a test program with a loop that calls your procedure five times and displays each matrix in the console window. Following is sample output for the first three matrices:**  
```
D W A L
S I V W
U I O L
L A I I

K X S V
N U U O
O R Q O
A U U T

P O A Z
A E A U
G K A E
I A G D
```
**A :** 알파벳으로 된 4x4 행렬 생성하는 프로시저, 랜덤으로 뽑되 50% 확률로 모음이 나와야 함  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

GenMatrix PROTO
PrintMatrix PROTO

.data
    matrix BYTE 16 DUP(?)
    vowels BYTE "AEIOU"         ; 모음
    msgLine BYTE 0
    
.code
main PROC
    call Randomize
    mov ecx, 5
L1:                             ; 4x4 행렬 5번 소환
    INVOKE GenMatrix
    INVOKE PrintMatrix
    call Crlf
    loop L1
    exit
main ENDP
;---------------------------------------------------------------
GenMatrix PROC USES eax ebx ecx esi
; 4x4 행렬을 무작위 대문자로 생성하는 프로시저
; 50% 확률로 모음을 선택함
;---------------------------------------------------------------
    mov esi, OFFSET matrix
    mov ecx, 16
L1:
    mov eax, 2
    call RandomRange            ; 0 or 1
    cmp eax, 0
    je MakeVowel                ; 0이면 모음 선택
MakeCon:
Again:
    mov eax, 26
    call RandomRange            ; 0~25
    add eax, 'A'                ; 'A'~'Z'
    ; 모음인지 검사하는 파트
    cmp al, 'A'
    je Again
    cmp al, 'E'
    je Again
    cmp al, 'I'
    je Again
    cmp al, 'O'
    je Again
    cmp al, 'U'
    je Again

    mov [esi], al               ; 자음은 저장
    jmp NextChar
MakeVowel:
    mov eax, 5
    call RandomRange            ; 0~4
    mov bl, [vowels + eax]      ; A,E,I,O,U
    mov [esi], bl               ; 모음 저장
NextChar:
    inc esi                     ; 다음 위치에 저장하기 위해 다시 루프
    loop L1
    ret
GenMatrix ENDP
;---------------------------------------------------------------
PrintMatrix PROC USES eax ecx esi
; 생성된 4x4 행렬을 화면에 출력하는 프로시저
;---------------------------------------------------------------
    mov esi, OFFSET matrix
    mov ecx, 4              ; 행의 수 = 4
RowLoop:
    push ecx
    mov ecx, 4              ; 열의 수 = 4
ColLoop:
    mov al, [esi]
    call WriteChar
    mov al, ' '
    call WriteChar
    inc esi
    loop ColLoop

    call Crlf               ; 한 행 출력 후 줄바꿈
    pop ecx
    loop RowLoop            ; 다음 행 출력
    ret
PrintMatrix ENDP
END main
```

---
## ★★★★ 11. Letter Matrix/Sets with Vowels  
**Q :  Use the letter matrix generated in the previous programming exercise as a starting point for this program. Generate a single random four-by-four letter matrix in which each letter has a 50% probability of being a vowel. Traverse each matrix row, column, and diagonal, generating sets of letters. Display only four-letter sets containing exactly two vowels. Suppose, for example, the following matrix was generated:**  
```
P O A Z
A E A U
G K A E
I A G D
```
** Then the four-letter sets displayed by the program would be POAZ, GKAE, IAGD, PAGI, ZUED, PEAD, and ZAKI. The order of letters within each set is unimportant.**  
**A :** 10번의 행렬에서 행, 열 대각선을 조사하여 모음이 2개인 줄만 출력하는 프로시저  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

GenMatrix PROTO
PrintMatrix PROTO
TravMatrix PROTO
CheckSet PROTO, pSet:PTR BYTE

.data
    matrix BYTE 16 DUP(?)
    vowels BYTE "AEIOU"         ; 모음
    tempSet BYTE 4 DUP(?)       ; 4글자 집합 임시 저장용

    msgMatrix BYTE "생성된 행렬:", 0
    msgResult BYTE "조건 만족 4글자 집합:", 0
    
.code
main PROC
    call Randomize
    
    INVOKE GenMatrix
    mov edx, OFFSET msgMatrix
    call WriteString
    call Crlf
    INVOKE PrintMatrix

    call Crlf
    mov edx, OFFSET msgResult
    call WriteString
    call Crlf

    INVOKE TravMatrix
    exit
main ENDP
;---------------------------------------------------------------
GenMatrix PROC USES eax ebx ecx esi
; 4x4 행렬을 무작위 대문자로 생성하는 프로시저
; 50% 확률로 모음을 선택함
;---------------------------------------------------------------
    mov esi, OFFSET matrix
    mov ecx, 16
L1:
    mov eax, 2
    call RandomRange            ; 0 or 1
    cmp eax, 0
    je MakeVowel                ; 0이면 모음 선택
MakeCon:
Again:
    mov eax, 26
    call RandomRange            ; 0~25
    add eax, 'A'                ; 'A'~'Z'
    ; 모음인지 검사하는 파트
    cmp al, 'A'
    je Again
    cmp al, 'E'
    je Again
    cmp al, 'I'
    je Again
    cmp al, 'O'
    je Again
    cmp al, 'U'
    je Again

    mov [esi], al               ; 자음은 저장
    jmp NextChar
MakeVowel:
    mov eax, 5
    call RandomRange            ; 0~4
    mov bl, [vowels + eax]      ; A,E,I,O,U
    mov [esi], bl               ; 모음 저장
NextChar:
    inc esi                     ; 다음 위치에 저장하기 위해 다시 루프
    loop L1
    ret
GenMatrix ENDP
;---------------------------------------------------------------
PrintMatrix PROC USES eax ecx esi
; 생성된 4x4 행렬을 화면에 출력하는 프로시저
;---------------------------------------------------------------
    mov esi, OFFSET matrix
    mov ecx, 4              ; 행의 수 = 4
RowLoop:
    push ecx
    mov ecx, 4              ; 열의 수 = 4
ColLoop:
    mov al, [esi]
    call WriteChar
    mov al, ' '
    call WriteChar
    inc esi
    loop ColLoop

    call Crlf               ; 한 행 출력 후 줄바꿈
    pop ecx
    loop RowLoop            ; 다음 행 출력
    ret
PrintMatrix ENDP
;---------------------------------------------------------------
TravMatrix PROC USES eax ebx ecx esi edi
; 행, 열, 대각선의 4글자 집합 생성 후 검사
;---------------------------------------------------------------
    cld
    ; 행 검사
    mov esi, OFFSET matrix
    mov ecx, 4
RowScan:
    mov edi, OFFSET tempSet
    push esi

    push ecx
    mov ecx, 4
    rep movsb                       ; 한 행의 알파벳을 tempSet으로 복사
    pop ecx

    pop esi
                                    ; 집합 검사
    INVOKE CheckSet, ADDR tempSet
    add esi, 4                      ; 다음 행 검사
    loop RowScan
    ; 열 검사
    mov ecx, 4
    mov esi, OFFSET matrix
ColScan:
    mov edi, OFFSET tempSet

    mov al, [esi]                   ; 한 열의 알파벳을 tempSet으로 복사
    stosb
    mov al, [esi+4]
    stosb
    mov al, [esi+8]
    stosb
    mov al, [esi+12]
    stosb
                                    ; 집합 검사
    INVOKE CheckSet, ADDR tempSet
    inc esi                         ; 다음 열 검사
    loop ColScan
    ; 대각선 검사
    mov edi, OFFSET tempSet
    mov ecx, 4
    mov ebx, 0                      ; 대각선 (0, 5, 10, 15)
Scan1:
    mov al, matrix[ebx]
    stosb
    add ebx, 5
    loop Scan1

    INVOKE CheckSet, ADDR tempSet

    mov edi, OFFSET tempSet
    mov ecx, 4
    mov ebx, 3                      ; 대각선 (3, 6, 9, 12)
Scan2:
    mov al, matrix[ebx]
    stosb
    add ebx, 3
    loop Scan2

    INVOKE CheckSet, ADDR tempSet

    ret
TravMatrix ENDP
;---------------------------------------------------------------
CheckSet PROC USES eax ebx ecx esi edi,
    pSet:PTR BYTE
; 모음 테이블에서 비교하여 모음이 정확히 2개면 출력하는 프로시저
;---------------------------------------------------------------
    mov esi, pSet
    mov ecx, 4
    mov ebx, 0
NextC:
    lodsb                           ; al = 현재 문자
    push ecx

    mov edi, OFFSET vowels
    mov ecx, 5
    repne scasb                     ; al과 모음들 비교
    jne NotVowel
    inc ebx                         ; 모음이면 +1
NotVowel:
    pop ecx
    loop NextC

    cmp ebx, 2                      ; 모음이 2개라면 출력
    jne ExitCheck                   ; 아니면 그냥 나감

    mov esi, pSet
    mov ecx, 4
PrintL:
    lodsb
    call WriteChar
    loop PrintL
    call Crlf
ExitCheck:
    ret 4
CheckSet ENDP
END main
```

---
## ★★★★ 12. Calculating the Sum of an Array Row  
**Q : Write a procedure named calc_row_sum that calculates the sum of a single row in a two-dimensional array of bytes, words, or doublewords. The procedure should have the following stack parameters: array offset, row size, array type, row index. It must return the sum in EAX. Use explicit stack parameters, not INVOKE or extended PROC. Write a program that tests your procedure with arrays of byte, word, and doubleword. Prompt the user for the row index, and display the sum of the selected row.**  
**A :** 사용자로부터 행 인덱스를 입력받아 한 행의 합을 계산하는 프로시저.  
INVOKE나 PROC 확장 지시어를 사용하지 않고 작성.  
오류 처리는 하지 않은 상태(배열 행 이상의 인덱스를 입력받았을 시 등)  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc



.data
    BArray BYTE 1,2,3,4
    RowSizeB = ($ - BArray)
           BYTE 5,6,7,8
           BYTE 9,10,11,12

    WArray WORD 10,20,30,40
    RowSizeW = ($ - WArray)
           WORD 50,60,70,80
           WORD 90,100,110,120

    DArray DWORD 100,200,300,400
    RowSizeD = ($ - DArray)
           DWORD 500,600,700,800
           DWORD 900,1000,1100,1200

    msg BYTE "행 인덱스를 입력하세요 (0~2): ", 0
    msg1 BYTE "BYTE 배열 선택된 행 합 = ", 0
    msg2 BYTE "WORD 배열 선택된 행 합 = ", 0
    msg3 BYTE "DWORD 배열 선택된 행 합 = ", 0

    rowIndex DWORD ?
    sum DWORD ?
.code
main PROC
    mov edx, OFFSET msg
    call WriteString
    call ReadInt
    mov rowIndex, eax
; BYTE 배열 확인
    push rowIndex
    push TYPE BArray
    push RowSizeB
    push OFFSET BArray
    call calc_row_sum

    mov edx, OFFSET msg1
    call WriteString
    call WriteInt
    call Crlf
; WORD 배열 확인
    push rowIndex
    push TYPE WArray
    push RowSizeW
    push OFFSET WArray
    call calc_row_sum

    mov edx, OFFSET msg2
    call WriteString
    call WriteInt
    call Crlf
; DWORD 배열 확인
    push rowIndex
    push TYPE DArray
    push RowSizeD
    push OFFSET DArray
    call calc_row_sum

    mov edx, OFFSET msg3
    call WriteString
    call WriteInt
    call Crlf
    exit
main ENDP
;---------------------------------------------------------------
calc_row_sum PROC
; [ebp+8] = 배열 시작 주소
; [ebp+12] = 한 행의 바이트 크기
; [ebp+16] = 자료형 크기
; [ebp+20] = 행 인덱스
; 반환값 : EAX = 행의 합
; 사용자가 선택한 행의 합을 반환하는 프로시저
;---------------------------------------------------------------
    push ebp
    mov ebp, esp

    push esi
    push ecx
    push ebx
    push edx
    cld

    mov esi, [ebp+8]            ; 배열 시작 주소
    mov ecx, [ebp+12]           ; 한 행의 바이트 크기
    mov ebx, [ebp+16]           ; 자료형 크기
    mov edx, [ebp+20]           ; 행 인덱스

    mov eax, ecx
    mul edx                     ; 인덱스 x 한 행의 크기
    add esi, eax                ; 행 주소 찾기

    mov eax, ecx
    xor edx, edx
    div ebx                     ; 현재 행의 원소 개수 = RowSize/TypeSize
    mov ecx, eax                ; ECX = 반복 횟수

    mov sum, 0                  ; 누적 합 저장공간 초기화

    cmp ebx, 1
    je ByteSum
    cmp ebx, 2
    je WordSum
    jmp DwordSum
ByteSum:
    lodsb                       ; AL = [esi]
    movzx edx, al
    add sum, edx
    loop ByteSum
    jmp done
WordSum:
    lodsw                       ; AX = [esi]
    movzx edx, ax
    add sum, edx
    loop WordSum
    jmp done
DwordSum:
    lodsd                       ; EAX = [esi]
    add sum, eax                ; 확장 필요없음
    loop DwordSum
done:
    mov eax, sum
    pop edx
    pop ebx
    pop ecx
    pop esi

    mov esp, ebp
    pop ebp
    ret
calc_row_sum ENDP

END main
```

---
## ★★★ 13. Trimming Leading Characters  
**Q : Create a variant of the Str_trim procedure that lets the caller remove all instances of a leading character from a string. For example, if you were to call it with a pointer to the string “###ABC” and pass it the # character, the resulting string would be “ABC.”**  
**A :** Str_trim의 변형으로 문자열 앞에 있는 특정 문자를 모두 제거하는 프로시저 만들기  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

Str_trimLeading PROTO, pString:PTR BYTE, char:BYTE

.data
    testStr BYTE "###ABC", 0
    msg1 BYTE "원본 문자열: ", 0
    msg2 BYTE "trim 결과 : ", 0
.code
main PROC
    mov edx, OFFSET msg1
    call WriteString
    mov edx, OFFSET testStr
    call WriteString
    call Crlf

    INVOKE Str_trimLeading, ADDR testStr, '#'

    mov edx, OFFSET msg2
    call WriteString
    mov edx, OFFSET testStr
    call WriteString
    call Crlf
    exit
main ENDP
;---------------------------------------------------------------
Str_trimLeading PROC USES eax ecx esi edi,
    pString:PTR BYTE,
    char:BYTE
; 문자열의 앞부분에 있는 특정 문자를 모두 제거하는 프로시저
; 받는 파라미터 :
;   pString = 문자열 주소
;   char = 제거할 문자
; 반환값 : 없음 (pString을 수정)
;---------------------------------------------------------------
    cld
    mov esi, pString            ; 원본 문자열 시작 주소
    mov edi, pString            ; 덮어쓸 위치(항상 처음)
SkipLoop:
    lodsb                       ; AL = [esi]
    cmp al, char                ; 제거할 문자와 같으면
    je SkipLoop                 ; 계속 건너뜀
    dec esi                     ; 제거할 마지막 문자로 위치를 되돌림

    INVOKE Str_length, esi
    mov ecx, eax                ; 남은 문자열 길이 계산 후
    inc ecx                     ; 널 문자까지 포함하여 복사 준비

    rep movsb                   ; esi위치부터 edi(처음)위치로 문자열 당겨오기
    ret 8
Str_trimLeading ENDP

END main
```

---
## ★★★ 14. Trimming a Set of Characters  
**Q : Create a variant of the Str_trim procedure that lets the caller remove all instances of a set of characters from the end of a string. For example, if you were to call it with a pointer to the string “ABC#$&” and pass it a pointer to an array of filter characters containing “%#!;$&*”, the resulting string would be “ABC”.**  
**A :** Str_trim의 변형으로 문자열 끝부분에 있는 필터 문자 집합에 해당하는 문자들을 모두 제거하는 프로시저 만들기   
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

Str_trimSet PROTO, pString:PTR BYTE, pSet:PTR BYTE

.data
    testStr BYTE "ABC#$&", 0
    filter BYTE "%#!;$&*", 0

    msg1 BYTE "원본 문자열: ", 0
    msg2 BYTE "trim 결과 : ", 0
.code
main PROC
    mov edx, OFFSET msg1
    call WriteString
    mov edx, OFFSET testStr
    call WriteString
    call Crlf

    INVOKE Str_trimSet, ADDR testStr, ADDR filter

    mov edx, OFFSET msg2
    call WriteString
    mov edx, OFFSET testStr
    call WriteString
    call Crlf
    exit
main ENDP
;---------------------------------------------------------------
Str_trimSet PROC USES eax ebx ecx edx esi edi,
    pString:PTR BYTE,
    pSet:PTR BYTE
; 문자열의 끝부분에 있는 filter에 포함된 모든 문자를
; 제거하는 프로시저
; 받는 파라미터 :
;   pString = 문자열 주소
;   pSet = 필터 문자 집합 주소
; 반환값 : 없음 (pString을 수정)
;---------------------------------------------------------------
    cld
    mov esi, pString
    INVOKE Str_length, esi
    cmp eax, 0
    je done                     ; 빈 문자열이면 종료

    mov ecx, eax
    dec eax
    add esi, eax                ; ESI = 문자열 마지막 문자 주소

    mov edi, pSet
    INVOKE Str_length, edi
    mov ebx, eax                ; EBX = 필터 집합 길이
TrimLoop:
    mov al, [esi]               ; 현재 확인할 끝 문자

    mov edi, pSet               ; 필터 집합 시작 주소
    mov ecx, ebx                ; 필터 집합 길이만큼
    repne scasb                 ; AL과 필터문자 비교

    jnz FinishTrim              ; ZF=0 이면 집합에 없음 => 중단

    dec esi                     ; 있으면 이전 문자열 확인(제거 계속)
    jmp TrimLoop
FinishTrim:
    mov BYTE PTR[esi+1], 0      ; 필터 문자가 있던 곳 만큼 문자열 자르기
done:
    ret 8
Str_trimSet ENDP

END main
```

---
