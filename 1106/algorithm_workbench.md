# Algorithm Workbench

## 문제 1
**Q : AL 레지스터에 있는 ASCII 숫자를 이진 값으로 변환하는 한 줄 명령어를 작성하시오. AL이 이미 0~9 사이의 이진 값이라면 그대로 두시오.**
**A :** ASCII 코드에서 '0'은 0x30, '9'는 0x39입니다. 만약 AL이 0~9 사이의 값이면 변환할 필요가 없고, ASCII 코드일 경우 0~9로 변환해야 합니다. 0x30은 `0011 0000b`입니다. 따라서 다음과 같이 작성할 수 있습니다.

```asm
and AL, 0Fh
```

## 문제 2
**Q : 32비트 메모리 피연산자의 패리티(parity)를 계산하는 명령어를 작성하시오. 힌트: 이전에 나온 B0 XOR B1 XOR B2 XOR B3 공식을 사용합니다.**
**A :**
```asm
.data
   Value DWORD ?
.code
   mov al, BYTE PTR Value
   xor al, BYTE PTR Value+1
   xor al, BYTE PTR Value+2
   xor al, BYTE PTR Value+3
```

## 문제 3
**Q : SetX와 SetY라는 두 비트맵 집합이 있을 때, SetX에는 속하지만 SetY에는 속하지 않는 멤버를 나타내는 비트 문자열을 EAX에 생성하는 명령어를 작성하시오.**
**A :**
```asm
.data
   SetX DWORD ?
   SetY DWORD ?
.code
   mov eax, SetX
   xor eax, SetY
   and eax, SetX
```

## 문제 4
**Q : DX 레지스터에 있는 부호 없는 정수가 CX보다 작거나 같으면 L1 라벨로 점프하는 명령어를 작성하시오.**
**A :**
```asm
cmp dx, cx
jbe L1
```

## 문제 5
**Q : AX 레지스터에 있는 부호 있는 정수가 CX보다 크면 L2 라벨로 점프하는 명령어를 작성하시오.**
**A :**
```asm
cmp ax, cx
jg L2
```

## 문제 6
**Q : AL 레지스터의 0번과 1번 비트를 먼저 0으로 클리어한 후, 결과가 0이면 L3로 점프하고, 그렇지 않으면 L4로 점프하는 명령어를 작성하시오.**
**A :**
```asm
and al, 11111100h
jz L3
jmp L4
```

## 문제 7
**Q : 다음 의사코드를 어셈블리로 구현하시오. short-circuit 평가를 사용하며, val1과 X는 32비트 변수라고 가정합니다.**

```asm
if( val1 > ecx ) AND ( ecx > edx )
   X = 1
else
   X = 2;
```
**A :**
```asm
cmp val1, ecx
ja L1
jmp next
L1:
   cmp ecx, edx
   ja L2
   jmp next
L2:
   mov X, 1
   jmp END
next:
   mov X, 2
END:
```

## 문제 8
**Q : 다음 의사코드를 어셈블리로 구현하시오. short-circuit 평가를 사용하며, X는 32비트 변수라고 가정합니다.**

```asm
if( ebx > ecx ) OR ( ebx > val1 )
   X = 1
else
   X = 2
```
**A :**
```asm
cmp ebx, ecx
jbe L1
jmp next
L1:
   cmp ebx, val1
   jbe L2
   jmp next
L2:
   mov X, 2
   jmp END
next:
   mov X, 1
END:
```

## 문제 9
**Q : 다음 의사코드를 어셈블리로 구현하시오. short-circuit 평가를 사용하며, X는 32비트 변수라고 가정합니다.**

```asm
if( ebx > ecx AND ebx > edx) OR ( edx > eax )
   X = 1
else
   X = 2
```
**A :**
```asm
cmp ebx, ecx
jbe L1
cmp ebx, edx
ja next
L1:
   cmp edx, eax
   ja next
   jmp L2
L2:
   mov X, 2
   jmp END
next:
   mov X, 1
END:
```

## 문제 10
**Q : 다음 의사코드를 어셈블리로 구현하시오. short-circuit 평가를 사용하며, A, B, N은 32비트 부호 있는 정수라고 가정합니다.**

```asm
while N > 0
  if N != 3 AND (N < A OR N > B)
     N = N – 2
  else
     N = N – 1
end while
```
**A :**
```asm
begin:
   cmp N, 0
   jng ENDwhile
   cmp N, 3
   jnz L1
   jmp ELSE
L1:
   cmp N, A
   jl YAP
   cmp N, B
   jg YAP
   jmp ELSE
YAP:
   sub N, 2
   jmp begin
ELSE:
   sub N, 1
   jmp begin  
ENDwhile:
```

