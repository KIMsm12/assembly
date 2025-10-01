# 어셈블리어 프로그래밍 과제 풀이

---

## 1. 정수 표현식 계산
표현식: **A = (A + B) − (C + D)**  

```asm
; 레지스터 값 예시: A=5, B=3, C=2, D=1
mov eax, 5      ; A
mov ebx, 3      ; B
mov ecx, 2      ; C
mov edx, 1      ; D

add eax, ebx    ; (A + B)
add ecx, edx    ; (C + D)
sub eax, ecx    ; (A + B) - (C + D)
```
최종적으로 **EAX**에는 결과 값이 저장됨.

---

## 2. 기호 정수 상수
```asm
MONDAY    EQU 1
TUESDAY   EQU 2
WEDNESDAY EQU 3
THURSDAY  EQU 4
FRIDAY    EQU 5
SATURDAY  EQU 6
SUNDAY    EQU 7

days BYTE MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
```

---

## 3. 데이터 정의 (Table 3-2 기준)
```asm
myByte    BYTE  127        ; 8비트 부호 있는 정수
mySByte   SBYTE -128       ; 8비트 부호 있는 정수
myWord    WORD  65535      ; 16비트 부호 없는 정수
mySWord   SWORD -32768     ; 16비트 부호 있는 정수
myDWord   DWORD 4294967295 ; 32비트 부호 없는 정수
mySDWord  SDWORD -2147483648 ; 32비트 부호 있는 정수
myQWord   QWORD 18446744073709551615 ; 64비트 부호 없는 정수
myTByte   TBYTE 12345678901234567890123456789012345678 ; 80비트 실수
myReal4   REAL4 1.23
myReal8   REAL8 1.23
myReal10  REAL10 1.23
```

---

## 4. 기호 텍스트 상수
```asm
HELLO   TEXTEQU <"Hello">
WORLD   TEXTEQU <"World">
COLOR   TEXTEQU <"Blue">

msg1 BYTE HELLO,0
msg2 BYTE WORLD,0
msg3 BYTE COLOR,0
```

---

## 5. AddTwoSum 프로그램의 Listing 파일 분석
예시 코드:  
```asm
mov eax, 5
add eax, 6
```
생성된 기계어 (예상):  
- `mov eax, 5` → B8 05 00 00 00  
- `add eax, 6` → 83 C0 06  

**설명:**  
- `B8`은 즉시값을 `EAX`에 로드하는 명령어. 뒤의 `05 00 00 00`은 32비트 리틀엔디언 정수 5.  
- `83 C0 06`은 `EAX`에 8비트 상수(06)를 더하는 명령.  

---

## 6. AddVariables 프로그램 64비트 변환
```asm
.data
var1 QWORD 10000000000
var2 QWORD 20000000000
sum  QWORD ?

.code
mov rax, var1
add rax, var2
mov sum, rax
```
**문제점:**  
- 32비트 어셈블러에서는 `QWORD`와 `RAX` 레지스터를 지원하지 않아 **syntax error** 발생.  

**해결 방법:**  
- 64비트 MASM (ML64) 또는 x86-64 어셈블러 환경을 사용해야 함.  
- `.model flat` 대신 `.x64` 사용.  
