# Programming Exercises — 완전 풀이집

아래 문서는 사용자가 제공한 연습문제들과 그 해설 코드들을 **같은 기능을 유지하되 표현과 변수 이름을 변형**하여 정리한 완성본입니다. 각 문제마다 (1) 문제 요약 (2) 해설 및 핵심 아이디어 (3) 어셈블리 코드(실행 가능한 형태)를 포함합니다. 모든 코드는 Irvine32 링크 라이브러리를 기반으로 한 32-bit MASM 스타일입니다.

> 주의: 실행 환경(Visual Studio + MASM / Irvine32)이 준비되어 있다고 가정합니다. 필요하면 64-bit(RAX/RBX) 버전으로도 변환해 드립니다.

---

## 문제 1. Draw Text Colors
**문제 요약**: 동일한 문자열을 네 가지 서로 다른 색으로 화면에 출력하라. `SetTextColor`를 사용하고 루프를 활용하라.

**핵심 아이디어**: 색상 코드를 배열에 두고 루프에서 순차적으로 꺼내 `SetTextColor` 호출 후 문자열 출력.

```asm
.386
.model flat, stdcall
.stack 4096
include Irvine32.inc

.data
    msg     BYTE "Hello, Assembly!",0
    palette BYTE 0x0E,0x02,0x09,0x0C    ; 원하는 전경색 코드들

.code
main PROC
    call Clrscr
    call ShowPaletteText
    exit
main ENDP

; ShowPaletteText - palette 배열에 있는 색으로 msg 출력
; 사용 레지스터: esi=인덱스, ecx=카운트, eax=색속성
ShowPaletteText PROC
    mov ecx, LENGTHOF palette
    xor esi, esi
PrintLoop:
    movzx eax, palette[esi]
    push eax
    call SetTextColor
    add esp, 4

    mov edx, OFFSET msg
    call WriteString
    call Crlf

    inc esi
    loop PrintLoop
    ret
ShowPaletteText ENDP

END main
```

---

## 문제 2. Linking Array Items
**문제 요약**: 시작 인덱스와 문자 배열, 링크(다음 인덱스) 배열이 주어진다. 링크를 따라가며 문자들을 새로운 배열에 순서대로 복사하라. (chars: BYTE, links: DWORD 사용)

**핵심 아이디어**: 현재 인덱스를 통해 문자 가져오고 output에 저장한 뒤 links 배열에서 다음 인덱스를 읽어 반복.

```asm
.386
.model flat, stdcall
.stack 4096
include Irvine32.inc

.data
    chars   BYTE 'H','A','C','E','B','D','F','G'
    links   DWORD 0,4,5,6,2,3,7,0
    outbuf  BYTE LENGTHOF chars DUP(?)
    startIdx DWORD 1

.code
main PROC
    mov esi, startIdx   ; 현재 인덱스
    xor edi, edi        ; output 인덱스 = 0
    call FollowLinks
    call PrintResult
    exit
main ENDP

; FollowLinks - 링크를 따라 chars를 outbuf에 복사
; 입력: ESI = 시작 인덱스, EDI = outbuf 인덱스
FollowLinks PROC
    .while 1
        mov eax, esi
        mov al, chars[eax]
        mov outbuf[edi], al
        inc edi

        mov eax, esi
        mov esi, links[eax*4]   ; DWORD 인덱스 접근

        cmp esi, 0
        je DoneCopy
    .endw
DoneCopy:
    ; 마지막 인덱가 0인 경우에도 chars[0]을 이미 복사했으므로 종료
    ret
FollowLinks ENDP

; PrintResult - outbuf를 화면에 출력 (공백 사이로)
PrintResult PROC
    mov ecx, LENGTHOF outbuf
    lea esi, outbuf
PrintLoop:
    mov al, [esi]
    call WriteChar
    mov al, ' '
    call WriteChar
    inc esi
    loop PrintLoop
    call Crlf
    ret
PrintResult ENDP

END main
```

---

## 문제 3. Simple Addition (1)
**문제 요약**: 화면을 지우고, 커서를 화면 중앙 근처로 이동시킨 뒤 두 정수를 입력받아 합을 계산하고 출력하라.

**핵심 아이디어**: `Gotoxy`로 위치를 잡고 `ReadInt`로 입력, `WriteInt`로 출력.

```asm
.386
.model flat, stdcall
.stack 4096
include Irvine32.inc

.data
    prompt1 BYTE "First integer: ",0
    prompt2 BYTE "Second integer: ",0
    sumMsg  BYTE "Sum = ",0
    total   DWORD ?

.code
main PROC
    call Clrscr
    mov dh, 12
    mov dl, 35
    call Gotoxy

    mov edx, OFFSET prompt1
    call WriteString
    call ReadInt
    mov ebx, eax    ; 저장: 첫번째

    mov dh, 13
    mov dl, 35
    call Gotoxy
    mov edx, OFFSET prompt2
    call WriteString
    call ReadInt
    add eax, ebx
    mov total, eax

    mov edx, OFFSET sumMsg
    call WriteString
    mov eax, total
    call WriteInt
    call Crlf
    exit
main ENDP

END main
```

---

## 문제 4. Simple Addition (2)
**문제 요약**: 문제 3의 프로그램을 변형하여 전체 과정을 3번 반복하되 각 반복 뒤 화면을 지운다. 각 반복은 사용자 확인을 위해 일시정지할 수 있다.

**핵심 아이디어**: 루프(ECX=3)를 사용하여 반복하고 루프마다 `Clrscr`로 화면 초기화.

```asm
.386
.model flat, stdcall
.stack 4096
include Irvine32.inc

.data
    prompt1 BYTE "First integer: ",0
    prompt2 BYTE "Second integer: ",0
    sumMsg  BYTE "Sum = ",0
    press   BYTE "Press any key to continue...",0
    total   DWORD ?

.code
main PROC
    mov ecx, 3
Repeat:
    call Clrscr
    mov dh, 12
    mov dl, 35
    call Gotoxy
    mov edx, OFFSET prompt1
    call WriteString
    call ReadInt
    mov ebx, eax

    mov dh, 13
    mov dl, 35
    call Gotoxy
    mov edx, OFFSET prompt2
    call WriteString
    call ReadInt
    add eax, ebx
    mov total, eax

    mov edx, OFFSET sumMsg
    call WriteString
    mov eax, total
    call WriteInt
    call Crlf

    mov edx, OFFSET press
    call WriteString
    call ReadChar

    loop Repeat
    exit
main ENDP

END main
```

---

## 문제 5. BetterRandomRange Procedure
**문제 요약**: 기존 `RandomRange`는 0..N-1 범위 난수를 반환한다. 호출자는 EBX에 M(하한), EAX에 N(상한)을 넣고 `BetterRandomRange`를 호출하면 결과는 M..N-1 범위가 되어야 한다. 테스트 루프 50회 출력.

**핵심 아이디어**: (N - M)을 RandomRange에 넣어 0..(N-M-1) 난수를 얻은 뒤 M을 더해준다.

```asm
.386
.model flat, stdcall
.stack 4096
include Irvine32.inc

.data
    iterations DWORD 50

.code
main PROC
    call Randomize
    mov ecx, iterations
LoopTest:
    mov ebx, -300   ; M
    mov eax, 100    ; N
    call BetterRandomRange
    ; 결과는 EAX에 있음, 출력
    call WriteInt
    call Crlf
    loop LoopTest
    exit
main ENDP

; BetterRandomRange: 입력 EAX=N, EBX=M, 반환 EAX in [M .. N-1]
BetterRandomRange PROC
    push ebx
    push eax
    sub eax, ebx       ; EAX = N - M
    push eax
    call RandomRange   ; RandomRange expects EAX = max, returns in EAX (0..max-1)
    add esp, 4
    pop eax            ; original N (not needed further)
    pop ebx            ; restore M
    add eax, ebx       ; shift into [M .. N-1]
    ret
BetterRandomRange ENDP

END main
```

---

## 문제 6. Random Strings
**문제 요약**: 길이 L인 대문자(A-Z)만으로 이루어진 무작위 문자열을 생성하는 절차를 만들고, 포인터와 길이를 받아 문자열을 버퍼에 채운다. 테스트로 20회 호출하여 출력.

**핵심 아이디어**: RandomRange(26)을 호출해 0..25를 얻고 'A'를 더한다.

```asm
.386
.model flat, stdcall
.stack 4096
include Irvine32.inc

.data
    buf    BYTE 64 DUP(0)
    len    DWORD 30   ; 예시 길이
    tries  DWORD 20

.code
main PROC
    call Randomize
    mov ecx, tries
GenLoop:
    mov eax, len            ; 길이 L을 EAX에
    lea edx, buf            ; 버퍼 주소를 EDX로 (Irvine 스타일)
    call MakeRandomCaps     ; 호출
    ; 출력
    mov edx, OFFSET buf
    call WriteString
    call Crlf
    loop GenLoop

    exit
main ENDP

; MakeRandomCaps: EAX = L, EDX = buffer pointer
; 결과: buffer에 null-terminated 문자열 저장
MakeRandomCaps PROC USES esi
    push ecx
    push ebx
    mov esi, edx           ; esi = buffer
    mov ecx, eax           ; ecx = length
GenLoop2:
    mov eax, 26
    call RandomRange       ; 0..25
    add al, 'A'
    mov [esi], al
    inc esi
    loop GenLoop2
    mov byte ptr [esi], 0
    pop ebx
    pop ecx
    ret
MakeRandomCaps ENDP

END main
```

---

## 문제 7. Random Screen Locations
**문제 요약**: 콘솔 창의 범위 내에서 100개의 임의 위치에 문자 하나를 표시하고, 각 출력 사이에 100ms 지연을 둔다. `GetMaxXY`를 활용하라.

**핵심 아이디어**: GetMaxXY로 (maxX,maxY) 얻은 뒤 RandomRange로 좌표 생성하고 `GotoXY` 후 문자 출력 및 Delay(100).

```asm
.386
.model flat, stdcall
.stack 4096
include Irvine32.inc

.data
    iterations DWORD 100
    ch BYTE '*'

.code
main PROC
    call Randomize
    call GetMaxXY         ; EAX = maxX, EBX = maxY
    mov esi, eax
    mov edi, ebx

    mov ecx, iterations
LocLoop:
    mov eax, esi
    call RandomRange      ; returns 0..maxX-1
    mov dx, ax            ; DX = x
    mov eax, edi
    call RandomRange      ; returns 0..maxY-1
    mov dh, al            ; DH = y (uses high-low pair but fine here)
    call GotoXY
    mov al, ch
    call WriteChar
    mov eax, 100
    call Delay
    loop LocLoop
    exit
main ENDP

END main
```

---

## 문제 8. Color Matrix
**문제 요약**: 전경색(0..15)과 배경색(0..15)의 모든 조합(256가지)을 출력하라. 중첩 루프 사용.

**핵심 아이디어**: 배경색 바깥 루프, 전경색 내부 루프. 속성 = bg*16 + fg.

```asm
.386
.model flat, stdcall
.stack 4096
include Irvine32.inc

.data
    outch BYTE 'X'

.code
main PROC
    xor ebx, ebx    ; bg
BG_LOOP:
    xor ecx, ecx    ; fg
FG_LOOP:
    mov eax, ebx
    imul eax, 16
    add eax, ecx    ; color attribute
    push eax
    call SetTextColor
    add esp, 4
    mov al, outch
    call WriteChar
    inc ecx
    cmp ecx, 16
    jl FG_LOOP

    call Crlf
    inc ebx
    cmp ebx, 16
    jl BG_LOOP

    ; reset to default
    mov eax, 7
    push eax
    call SetTextColor
    add esp, 4
    exit
main ENDP

END main
```

---

## 문제 9. Recursive Procedure (직접 재귀)
**문제 요약**: 재귀 호출을 사용하여 고정된 횟수만큼 자기 자신을 호출하라. ECX에 허용 재귀 횟수를 넣고, `LOOP`만을 이용해 재귀 횟수 제어.

**핵심 아이디어**: 재귀 진입 때마다 카운터를 증가시키고 `push ecx`로 상태 보전, `loop`를 이용해 한 번 더 재귀 호출을 결정.

```asm
.386
.model flat, stdcall
.stack 4096
include Irvine32.inc

.data
    runCount DWORD 0

.code
main PROC
    mov ecx, 6       ; 재귀 깊이 (예)
    call Recur
    ; 결과 출력
    mov eax, runCount
    call WriteInt
    call Crlf
    exit
main ENDP

; Recur: ECX = 남은 반복(LOOP가 사용할 레지스터)
Recur PROC
    inc runCount
    push ecx
    loop DoCall
    pop ecx
    ret

DoCall:
    call Recur
    pop ecx
    ret
Recur ENDP

END main
```

---

## 문제 10. Fibonacci Generator
**문제 요약**: N개의 피보나치 정수를 doubleword 배열에 채우는 절차를 작성하라. 테스트: N = 47.

**핵심 아이디어**: 첫 두 항을 1로 세팅한 다음 반복해서 다음 항 = 이전 두 항의 합을 저장.

```asm
.386
.model flat, stdcall
.stack 4096
include Irvine32.inc

.data
    N DWORD 47
    FibArr DWORD 47 DUP(0)

.code
main PROC
    lea eax, FibArr
    mov ecx, N
    call MakeFibs
    ; (디버거로 배열 확인)
    exit
main ENDP

; MakeFibs: EAX = 배열 포인터, ECX = N
MakeFibs PROC USES esi ebx edx
    mov esi, eax
    mov DWORD PTR [esi], 1
    add esi, 4
    mov DWORD PTR [esi], 1
    add esi, 4
    sub ecx, 2
    mov eax, 1     ; F(n-1)
    mov edx, 1     ; F(n-2)
NextF:
    mov ebx, eax
    add eax, edx   ; eax = F(n)
    mov edx, ebx   ; edx = old eax
    mov [esi], eax
    add esi, 4
    loop NextF
    ret
MakeFibs ENDP

END main
```

---

## 문제 11. Finding Multiples of K
**문제 요약**: 길이 N(예:50) 바이트 배열을 0으로 초기화한 뒤, K의 배수 위치에 1을 설정하는 절차를 작성하라. 이를 K=2, K=3으로 각각 호출하라.

**핵심 아이디어**: 배열을 0으로 초기화하고, K 간격으로 인덱스를 증가시키며 해당 바이트를 1로 설정.

```asm
.386
.model flat, stdcall
.stack 4096
include Irvine32.inc

.data
    N EQU 50
    flags BYTE N DUP(0)

.code
main PROC
    lea esi, flags
    ; K = 2
    mov eax, 2
    call MarkMultiples
    ; K = 3
    mov eax, 3
    call MarkMultiples

    ; (디버거로 flags 확인 또는 출력)
    exit
main ENDP

; MarkMultiples: EAX = K, ESI = flags base (assumed)
; 내부에서 EDI를 인덱스로 사용
MarkMultiples PROC USES edi ecx
    push esi
    xor edi, edi
    mov ecx, N
    ; 반복: 인덱이 N보다 작을 때
MarkLoop:
    cmp edi, N
    jge DoneMark
    mov BYTE PTR [esi+edi], 1
    add edi, eax   ; 인덱 += K
    jmp MarkLoop
DoneMark:
    pop esi
    ret
MarkMultiples ENDP

END main
```

---

### 마무리
원하시면 이 마크다운 파일을 저장해 드리겠습니다. 또한 다음 옵션을 선택할 수 있습니다:

- A: **이 문서를 파일로 저장 (Markdown)**  
- B: **PDF로 변환해서 제공**  
- C: **각 코드에 실행 방법(Visual Studio 설정, 빌드 명령) 추가**  
- D: **전체 코드를 64-bit (x64) 변환**

원하시는 옵션을 알려주세요.
