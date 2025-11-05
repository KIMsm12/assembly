
---

# 1030 Lecture Notes

## ✅ Stack Overview
스택(Stack)은 **LIFO(Last-In, First-Out)** 구조로 동작하는 메모리 공간이다. 즉, **마지막에 들어온 데이터가 가장 먼저 빠져나간다.**  
스택의 최상단은 **Top**, 바닥은 **Bottom**이라 하며, 32비트 환경 기준으로 스택의 최상단 주소는 **ESP** 레지스터가 가리킨다.

```asm
.stack
```
위와 같이 선언되는 스택 영역의 시작 주소는 **SS(Stack Segment)** 레지스터에 저장되고, ESP가 스택 내 위치를 지시한다.

일반적으로 메모리 구조는 **위 → 아래** 방향으로 주소값이 증가하지만, 스택은 그 반대로 **주소가 높은 쪽이 위**에 있다고 표현하는 방식으로 그려지는 경우가 많다.

---

## ✅ PUSH & POP
스택을 다루는 기본 명령 두 가지이다.

> 참고: 자료구조에서 push/pop 시 스택 포인터를 다루듯, 어셈블리에서는 ESP가 해당 역할을 한다.

### 🔹 PUSH
PUSH는 `ESP를 감소`시킨 뒤 해당 위치에 값을 저장한다.

(예시 그림 생략 — 원본과 유사하게 생략)

PUSH가 허용하는 피연산자:
1) 16-bit reg/mem  
2) 32-bit reg/mem  
3) 32-bit immediate

---

### 🔹 POP
POP은 스택 최상단 값을 꺼내 해당 위치에 넣고, 이후 ESP를 다시 `증가`시킨다.

POP 허용 피연산자:
1) 16-bit reg/mem
2) 32-bit reg/mem

즉시값(immediate)은 POP할 수 없음.

> 별도 대상 지정이 없으면 POP은 기본적으로 **EAX**에 값을 가져오는 형태로 동작한다.

---

## ✅ Stack에 저장되는 정보
스택에는 일반적으로 아래 요소들이 기록된다:
1) **지역 변수 (Local Variables)**
2) **함수 인자 (Arguments)**
3) **저장된 레지스터 값 (Register Backup)**
4) **복귀 주소 (Return Address)** — CALL 명령 실행 시 자동 PUSH

---

## ✅ PUSHFD / POPFD
- 플래그 레지스터(EFLAGS)를 통째로 push/pop.

## ✅ PUSHAD / POPAD, PUSHA / POPA
- 8개의 범용 레지스터를 순서대로 저장/복원.
- PUSHAD는 EAX → EDI 순서로 저장.
- PUSHAD 시 ESP는 원래 값을 스택에 넣는다.

> 반환 값(EAX)을 유지해야 할 경우, POPAD로 EAX가 덮어쓰기될 수 있으므로 주의.

```asm
MySub PROC
    pushad
    ; ...
    popad
    ret
MySub ENDP
```

복원 순서 때문에 `push`와 `pop` 순서는 반드시 반대로 해야 한다.

---

## ✅ PROC
고급 언어의 함수 개념과 비슷한 **프로시저**를 만들 때 사용.
```asm
main PROC
    ; ...
main ENDP

foo PROC
    ; ...
    ret
foo ENDP
```
RET는 호출한 지점으로 프로그램 실행을 되돌린다.

> 한 프로시저에서 선언된 레이블은 기본적으로 그 내부에서만 사용 가능.  
전역 레이블을 만들고 싶은 경우:
```asm
SomeLabel::
```

---

## ✅ 프로시저 문서화
프로시저 시작부에 아래 내용을 주석으로 정리하는 것이 권장됨:
- 기능 설명
- 전달 인자
- 반환값
- 필요 조건

---

## ✅ CALL & RET
CALL: 다른 프로시저를 실행
1) 현재 위치(IP)를 스택에 PUSH
2) 대상 주소로 점프

RET: 스택에서 복귀 주소 POP → 원래 흐름으로 돌아감

---

## ✅ Register로 인수 전달
```asm
.data
result DWORD ?
.code
main PROC
    mov eax, 10000h
    mov ebx, 20000h
    mov ecx, 30000h
    call SumAll    ; eax ← eax+ebx+ecx
    mov result, eax
```
고급 언어처럼 인수를 스택에 넣는 대신 레지스터를 활용하기도 한다.

---

## ✅ USES
프로시저 안에서 사용할 레지스터를 미리 선언해 자동으로 push/pop 시켜주는 방식:

```asm
ArraySum PROC USES esi ecx
    mov eax, 0
L1:
    add eax, [esi]
    add esi, TYPE DWORD
    loop L1
    ret
ArraySum ENDP
```

ASSEMBLER가 자동으로:
- PROC 시작 시 열거된 레지스터를 PUSH
- RET 전 POP

을 삽입해줌.

---

## ✅ 링크 라이브러리
링커는 여러 개의 OBJ 파일 + 라이브러리(kernel32.lib 등)를 묶어 실행 파일(EXE)을 생성한다.

- `kernel32.lib` → Windows 기본 API 정보 포함
- `Irvine32.lib` → 학습 지원용 라이브러리 (입출력 등 간단한 기능 제공)

실행 시 OS가 `kernel32.dll`을 로드하여 API를 수행한다.

즉:
```
source.asm → obj → (link w/ kernel32.lib & Irvine32.lib) → exe → runtime → DLL 사용
```

해당 DLL/라이브러리가 설치되어 있어야 실행 가능.

---
