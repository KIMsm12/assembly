# Short Answer 풀이 (변형본)

## Q1

**Q:** 32비트 범용 레지스터 전부를 한 번에 스택에 저장하는 명령어는?

**A:** `PUSHA` (또는 `PUSHAD`)

---

## Q2

**Q:** 32비트 EFLAGS 값을 스택에 집어넣는 명령은?

**A:** `PUSHFD`

---

## Q3

**Q:** 스택 최상단 값을 EFLAGS로 되돌리는 명령은?

**A:** `POPFD`

---

## Q4

**Q:** NASM에서는 `PUSH EAX EBX ECX`처럼 여러 레지스터를 선택적으로 넣을 수 있다. 왜 `PUSHAD`보다 유리할까?

**A:** MASM의 `PUSHAD`는 모든 GPR을 통째로 저장해 불필요한 메모리 접근이 발생할 수 있다. NASM처럼 원하는 레지스터만 명시하면 공간 절약, 실행 효율 증가, 어떤 레지스터를 보존하는지 명확해져 가독성도 좋아진다.

---

## Q5

**Q:** 만약 `push eax`가 없다면 어떤 두 명령으로 대체할 수 있을까?

```asm
sub esp, 4
mov [esp], eax
```

---

## Q6

**Q:** (T/F) `RET`는 스택 맨 위 값을 명령 포인터(EIP/RIP)에 로드한다.

**A:** True

---

## Q7

**Q:** (T/F) Microsoft assembler에서 중첩 호출을 하려면 NESTED 옵션이 필요하다.

**A:** False

---

## Q8

**Q:** (T/F) 보호 모드에서 함수 호출은 최소 4바이트의 스택을 사용한다.

**A:** True

---

## Q9

**Q:** (T/F) 32비트 인자를 전달할 때 ESI/EDI는 사용할 수 없다.

**A:** False

---

## Q10

**Q:** (T/F) ArraySum(5.2.5)은 DWORD 배열을 가리키는 포인터를 받는다.

**A:** True

---

## Q11

**Q:** (T/F) `USES`는 함수에서 건드리는 레지스터를 모두 나열하는 데 쓰인다.

**A:** True

---

## Q12

**Q:** (T/F) `USES`는 PUSH만 자동으로 만들고 POP은 수동으로 작성해야 한다.

**A:** False

---

## Q13

**Q:** (T/F) USES에 나열하는 레지스터는 쉼표로 구분해야 한다.

**A:** False (공백 구분)

---

## Q14

**Q:** ArraySum이 WORD(16비트) 배열을 더하도록 바꾸려면?

```asm
ArraySum PROC
    push esi
    push ecx
    push edx
    xor  eax, eax
L1:
    movzx edx, WORD PTR [esi]
    add   eax, edx
    add   esi, TYPE WORD
    loop  L1
    pop   edx
    pop   ecx
    pop   esi
    ret
ArraySum ENDP
```

---

## Q15

```asm
push 5
push 6
pop eax
pop eax
```

**결과:** EAX = 5

---

## Q16

```asm
push 10
push 20
call Ex2Sub
pop  eax
...
Ex2Sub:
    pop eax
    ret
```

**정답:** d — 라인 11에서 잘못된 복귀로 런타임 오류

---

## Q17

```asm
mov eax,30
push eax
push 40
call Ex3Sub
...
Ex3Sub:
    pusha
    mov eax,80
    popa
    ret
```

**정답:** c — EAX = 30

---

## Q18

```asm
mov eax,40
push offset Here
jmp Ex4Sub
Here:
    mov eax,30
...
Ex4Sub:
    ret
```

**정답:** a, c

---

## Q19

```asm
mov edx,0
mov eax,40
push eax
call Ex5Sub
...
Ex5Sub:
    pop eax
    pop edx
    push eax
    ret
```

**정답:** a — EDX = 40

---

## Q20

**array 결과:** `[10, 20, 30, 40]`
