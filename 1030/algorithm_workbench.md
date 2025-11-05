# ✅ Algorithm Workbench 풀이

## ✅ 문제 1  
**Q. PUSH / POP만 사용하여 EAX와 EBX(RAX/RBX)의 값을 서로 교환하는 코드를 작성하라.**

**A.**  
스택을 임시 보관소처럼 사용한다.  
두 레지스터를 순서대로 PUSH 한 뒤, 끄집어낼 때 순서가 거꾸로 되므로 서로 교환된다.

```asm
push eax     ; 먼저 EAX 저장
push ebx     ; 다음 EBX 저장
pop eax      ; EBX → EAX
pop ebx      ; EAX → EBX
```

---

## ✅ 문제 2  
**Q. 현재 스택에 있는 반환 주소보다 3바이트 앞을 가리키도록 RET 직전에 조정하라.**

**A.**  
리턴 주소를 하나 꺼낸 뒤 +3 증가시키고, 다시 스택에 되돌려 놓으면 된다.

```asm
pop edx       ; 기존 return addr 가져오기
add edx, 3    ; 주소 +3
push edx      ; 수정된 주소 재삽입
ret
```

---

## ✅ 문제 3  
**Q. 지역 변수로 doubleword 2개 공간을 확보하고, 각각 1000h, 2000h로 초기화하는 코드를 작성하라.**

**A.**  
스택을 줄여 공간을 확보한 후, 해당 영역에 값을 채우면 고급 언어의 지역변수 구현과 동일하다.

```asm
MyFunc PROC
    sub esp, 8                   ; doubleword * 2 공간 확보
    mov DWORD PTR [esp],    1000h
    mov DWORD PTR [esp + 4], 2000h

    ; ... 함수 내용 ...

    add esp, 8                   ; 스택 복구
    ret
MyFunc ENDP
```

---

## ✅ 문제 4  
**Q. 인덱스 주소 방식을 이용해, doubleword 배열의 한 요소를 바로 이전 위치에 복사하라.**

**A.**  
현재 요소의 인덱스를 이용하여 값을 읽고, 이전 요소 위치에 써 넣는다.

```asm
; ebx → 배열 시작 주소
; edi → index (단위: doubleword)

mov eax, [ebx + edi*4]         ; 현재 원소 가져오기
mov [ebx + edi*4 - 4], eax     ; 이전 위치에 저장
```

---

## ✅ 문제 5  
**Q. 서브루틴의 return address를 출력하되, 원래 호출자로 정상 복귀할 수 있도록 작성하라.**

**A.**  
스택에서 return address를 잠시 꺼내 출력하고, 다시 push 해주면 문제없이 복귀 가능하다.

```asm
ShowRet PROC
    pop  ecx            ; return 주소 임시 보관
    call WriteHex       ; 주소 출력
    push ecx            ; 다시 스택에 복구
    ret
ShowRet ENDP
```

---

## ✅ 요약

| 문제 | 핵심 아이디어 |
|------|----------------|
| 1 | Push → Push → Pop → Pop 으로 값 교환 |
| 2 | 반환 주소 pop → +3 → push |
| 3 | `sub esp, 8`로 공간 → 값 저장 |
| 4 | 배열 index로 현재값 읽어 이전 위치에 write |
| 5 | return address pop → 출력 → push → ret |
