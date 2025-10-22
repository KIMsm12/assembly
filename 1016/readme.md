
reference  
>Copilot  
>blog.naver.com/heobk11- / 어셈블리 언어  
>intelligentcm.tistory.com/category/Assembly
>https://github.com/shim4786-pixel/assembly/blob/main/1016/readme.md
>Assembly Language for x86 processors 7th Edition  
---

---
# 1016 수업 정리  
## 오퍼랜드 타입  
[label:] mnemonic [operands] [; comment] << 가 어셈블리의 일반적인 명령어 형식이다.  
여기서 **mnemonic**은 실행할 명령어, **operands**는 명령어 실행의 대상이다.  
**Operands**는  
>mnemonic  
>mnemonic A  
>mnemonic A B  
>mnemonic A B C  

와 같이 명령어에 따라 안 올 수도 여러개가 올 수도 있다.  
오퍼랜드가 붙어있다면 첫 번째 자리, 여기에서는 A에 해당하는 오퍼랜드가 **Destination**을 담당하고 (여러 개의 오퍼랜드가 존재한다면)뒤에 붙는 오퍼랜드들은 **Source**가 된다.  
명령어에 따라 실행하면 **Source**의 값이 **Destination**에 들어간다.  

오퍼랜드로 올 수 있는 타입은 3가지 있는데  
1. 즉시값(Immediate) : (EQU, = 으로 상수 정의 할 때)1+2*3과 같은 수식이 올 수 있다. 이때는 연산 결과가 저장된다. 단순히 숫자를 넣을 수도 있다.  
2. 레지스터(Register) : CPU의 고속 기억장치이다.
3. 메모리(Memory) : 메모리의 위치를 참조하여 값을 가져올 수 있다.
---
## 레지스터
**레지스터들의 이름의 앞에 E가 붙어있다면 32비트, R이 붙어있다면 64비트 크기로 알아두면 된다. 아니면 16비트다.**  
범용 레지스터들의 용도  
>EAX(Extended Accumulator Register)확장 누산기 레지스터 : 상수/ 변수 값의 저장, 연산 결과를 저장, data이동 및 일시적 저장, 입출력 명령, 곱셈과 나눗셈 명령어에서 자동으로 사용.  
>EBX(Extended Base Register)확장 베이스 레지스터 : 주소의 간접 지정(데이터 접근 시 기준 주소 역할), data이동 및 일시적 저장.  
>ECX(Extended Counter Register)확장 카운터 레지스터 : 반복문, 문자열 명령어, 비트 이동, 회전 명령 등에서 Count하며 제어하는 역할. CPU는 루프 카운터에서 ECX를 자동으로 사용.  
>EDX(Extended Data Register)확장 데이터 레지스터 : EAX 혼자 담을 수 없는 data를 저장하는 보조 레지스터, 나눗셈 시 몫은 EAX, 나머지는 EDX에 저장.  
>>모든 위 레지스터의 경우 8비트씩 나누어 상위는 H, 하위는 L로 나타낸다.(AH, AL, BH, BL, CH, CL, DH, DL)  
>>이들은 입출력 주소 지정, 데이터 이동 및 일시적 저장을 담당한다.  

>ESI(Extended Source Index Register)확장 소스 인덱스 레지스터 : (복사나 비교 시)출발지 주소를 저장.  
>EDI(Extended Destination Index Register)확장 목적지 인덱스 레지스터 : (복사나 비교 시)목적지 주소를 저장.  
>>이들은 고속 메모리 전송 명령어에서 사용한다.  

>EBP(Extended Base Pointer Register)확장 베이스 포인터 레지스터 : 고급 언어에서 스택에 있는 함수 매개 변수와 지역 변수를 참조하기 위해 사용.  
 스택 포인터의 기준 주소(바닥 부분)을 저장함. 고급 수준의 프로그래밍 이외의 일반적인 계산과 데이터 전송에 사용되지 않아야 함.  
 확장 프레임 포인터(extended frame pointer)라고도 부름. ESP와 다르게 스택이 쌓이더라도 값이 변하지 않음.  
>ESP(Extended Stack Pointer Register)확장 스택 포인터 레지스터 : 현재 스택 top 위치를 표시. Stack Pointer의 가장 최근에 저장된 공간의 주소를 저장.  
 EBP와 다르게 함수 실행동안 새로운 값이 저장시 위치가 바뀜.  

특수 목적 레지스터들의 용도
>EIP(Extended Instruction Pointer Register)확장 명령어 포인터 레지스터 : 다음에 실행할 명령어의 주소를 저장. 어떤 명령어는 EIP를 조작하여 프로그램을 새 위치로 분기하게 만들기도 함.  
>EFLAGS(FLAGS Register)플래그 레지스터 : CPU의 동작을 제어하거나 CPU 연산의 결과를 반영하는 개별적인 2진수 비트들로 구성된 레지스터. 아래는 연산의 결과를 나타내는 상태 플래그들임.  
>>CF(Carry flag)캐리 플래그 : 부호없는 산술 연산의 결과가 너무 커 목적지에 저장이 불가능하다면 1로 세팅.  
>>OF(OverFlow flag)오버플로우 플래그 : 부호있는 산술 연산의 결과가 너무 크거나 작아 목적지에 저장 불가능하다면 1로 세팅.  
>>**오버플로우는 CF와 결과값의 MSB의 XOR연산으로 알 수 있다.**  
>>SF(Sign flag)부호 플래그 : 산술 논리 연산의 결과가 음수라면 1로 세팅.  
>>ZF(Zero flag)제로 플래그 : 산술 논리 연산의 결과가 0이라면 1로 세팅.  
>>AC(Auxiliary Carry flag)보조 캐리 플래그 : 산술 연산이 8비트 피연산자에서 비트 3에서 비트4로의 캐리가 발생했을 때 1로 세팅.  
>>PF(Parity flag)패리티 플래그 : 결과의 1의 개수가 짝수라면 1로 세팅.(데이터가 바뀌었거나 훼손될 가능성 있을 시 오류 검사에 사용)

>세그먼트 레지스터 : 메모리를 나누어 세그먼트 단위로 접속할 수 있도록 기준이 되는 세그먼트들의 시작 주소를 저장하는 레지스터들 모두 16비트의 크기를 가짐.  
>>CS(Code Segment)코드 세그먼트 레지스터 : 어셈블리에서 .code로 선언된 영역/ 실행중인(혹은 실행할) 코드가 위치한 메모리 영역을 지정.  
>>DS(Data Segment)데이터 세그먼트 레지스터 : 어셈블리에서 .data로 선언된 영역(c언어의 프로그램 주소 공간에서  
 데이터, 힙, 스택에 해당하는 부분)/ 일반 데이터가 위치한 메모리 영역을 지정.  
>>SS(Stack Segment)스택 세그먼트 레지스터 : .stack으로 정의된 스택 영역의 시작 주소를 저장.  
>>ES(Extra Segment)보조 세그먼트 레지스터 : 추가 데이터 접근용 보조 세그먼트  
>>FS(File/Far Segment)시스템 세그먼트 레지스터 : (OS에 따라 용도는 다르지만)운영체제 또는 스레드 관련 데이터 접근.  
>>GS(Global/General Segment)전역 세그먼트 레지스터 : (OS에 따라 용도는 다르지만)커널 구조체나 데이터 접근에 사용.  
---
## 기본(MOV같은) 명령어의 규칙
**MOV Destination, Source**   
1. 데스티네이션과 소스는 무조건 같은 크기여야 한다.  
2. 두 오퍼랜드는 모두 다 메모리 주소이면 안된다.  
3. IP(다음에 실행할 명령어의 주소를 담는 레지스터)는 데스티네이션 오퍼랜드로 들어오면 안된다.  
   MOV reg, reg  
   MOV mem, reg  
   MOV reg, mem  
   MOV mem, imm  
   MOV reg, imm 은 가능
--- 
## 부호 확장
signedVal SWORD -16 으로 FFF0h 값을 가지는 변수를 만들었을 때.  
ecx를 0으로 초기화하고 cx에 signedVal을 넣으면 ecx의 값(0000FFF0h)은 양수가 된다.  
그러니까 음수값을 넣을 때는 ecx를 0FFFFFFFFh로 초기화 하고 넣어야한다.  

---
## MOVZX와 MOVSX
**MOVZX**와 **MOVSX** 명령어는 두 오퍼랜드의 값이 달라도 옮길 수 있는 명령어이다.  
MOVZX reg32, reg/mem8  
MOVZX reg32, reg/mem16  
MOVZX reg16, reg/mem8 의 3가지 형식만 허용된다.  
여전히 데스티네이션 오퍼랜드의 사이즈가 작으면 안된다.  
이 때, 옮기고자 하는 레지스터의 하위비트에 값을 채우고 나머지는 0으로 채운다.  
**MOVSX**는 부호를 유지하며 확장하는 명령어이다.  
즉, 소스 오퍼랜드의 최상위 비트(MSB) 그러니까 부호 비트에 따라 나머지를 0또는 1로 채운다.  
MOVSX는 MOVZX와 달리 부호를 유지하는 명령어이다.  

---
## LAHF와 SAHF
L은 Load, S는 Store, F는 플래그 레지스터이다.  
**LAHF**는 F의 하위 8비트(상태 플래그)를 AH로 Load하는 명령어,  
**SAHF**는 AH의 값을 F의 하위 8비트(상태 플래그)에 Store하는 명령어이다.  
캐리, 플래그 등이 존재하는 플래그 레지스터의 하위 8비트는 이후 jmp 명령어등을 사용할 때 중요하다.  
그것을 내가 AH에 가져와 내용을 마음대로 바꿀 수 있는것에 의의가 있다.  

---
## XCHG
말 그대로 **exchange data**, 두 메모리 오퍼랜드를 서로 교환하는 명령어.  
reg to reg, reg to mem 만 가능하다.  
mem to mem은 기본적으로 불가능하므로
```asm
mov ax, val1
xchg ax, val2
mov val1, ax
```
처럼 교환해야 한다.  

---
## 직접 오프셋 피연산자
변수의 이름에 변위를 더하는 식이다.  
arrayB BYTE 10h, 20h, 30h, 40h, 50h 와 같은 배열이 있을 때,  
**mov al, arrayB** 라면 **al = 10h** 이다.  
여기서 arrayB에 1을 더하여 배열의 두 번째 바이트에 접근하는 것이다.  
**mov al, [arrayB + 1]** 라면 **al = 20h** 이다.  
2를 더하면 세 번째 바이트에 접근할 수 있을 것이다.  
**mov al [arrayB + 2]** 라면 **al = 30h** 이다.  
유효 주소를 대괄호로 둘러싸면 해당 주소의 메모리 내용을 얻기 위해 주소 수식을 역참조함을 의미한다.  
배열의 각 크기가 WORD, DWORD라면 다음 내용을 얻기 위해서는 +2, +4씩 해주어야 한다.  

---
## INC와 DEC
INC는 +1, DEC는 -1하는 명령어이다.  
ADD, SUB 명령어와 다른 점이 있다면 CF에 영향을 주지 않는다.  
예를 들어, al = 11111111h 일때 INC al 을 하면 al = 00000000h 가 되어 제로 플래그는 1로 세팅되지만 캐리 플래그는 세팅되지 않는다.  
이들은 단순한 카운터 명령어로 루프, 인덱스 증가/감소 등의 반복 제어를 하는 용도인데, 대부분의 경우 캐리는 중요하지 않기 때문에  
**실용성, 호환성, 효율성**을 고려하여 CF를 건드리지 않도록 설계된 것이다.  

---
## ADD와 SUB
더하고 빼는 명령어이다.  
데스티네이션 오퍼랜드에서 소스 오퍼랜드 값을 더하거나 빼 데스티네이션 오퍼랜드에 저장한다.  
해당 값에 따라 플래그 레지스터까지 변하면 명령어가 완료된다.  

---
## NEG
숫자를 2의 보수로 변환하여 부호를 바꾸는 명령어이다.  
```asm
NEG reg
NEG mem
```
과 같이 사용 가능하다.  
A-B 같은 수식을 수행할 때,
```asm
MOV eax, A
MOV ebx, B
SUB eax, ebx
```
대신
```asm
MOV eax, A
MOV ebx, B
NEG ebx
ADD eax, ebx
```
처럼 쓸 수 있는 것이다. SUB명령어에는 알아서 2의 보수를 취한 뒤 더하는 과정이 포함되어 있다.  
SBYTE에서 나타낼 수 있는 수는 **+127 ~ -128** 이다.
```asm
mov al, -128        ; AL = 10000000b
neg al              ; AL = 10000000b, OF = 1

mov al, +127        ; AL = 01111111b
neg al              ; AL = 10000001b, OF = 0
```
와 같이 -128을 부호 반전시키려 하면 AL에는 +128이 저장될 수 없다. 즉, OF가 1로 세팅된다.  
+127을 부호 반전시키면 유효한 값이므로 OF는 0으로 해제된다.  

---
## 데이터 관련 연산자와 디렉티브
연산자와 디렉티브는 실행가능한 명령어는 아니지만, 어셈블러가 이를 해석하여 실행한다.
### OFFSET 연산자
데이터 레이블의 오프셋을 반환한다.  
DS 시작으로부터 레이블이 몇 바이트 떨어져있는지를 나타낸다.  
```asm
.data
bVal  BYTE  ?     ; 1바이트
wVal  WORD  ?     ; 2바이트
dVal  DWORD ?     ; 4바이트
dVal2 DWORD ?     ; 4바이트
```
이고 bVal의 위치가 1000(16진수)라면 다음과 같다.  
```asm
mov esi, OFFSET bVal   ; esi = 0x1000
mov esi, OFFSET wVal   ; esi = 0x1001
mov esi, OFFSET dVal   ; esi = 0x1003
mov esi, OFFSET dVal2  ; esi = 0x1007
```
OFFSET 연산자는 직접 오프셋 피연산자에도 사용 가능한데, 5개의 16비트 워드를 포함하는 myArray 배열이 있다고 하자,  
```asm
.data
myArray WORD 1, 2, 3, 4, 5
.code
MOV esi, OFFSET myArray + 4
```
이 때 esi는 myArray의 시작 주소로부터 4바이트 떨어진 즉, 배열의 세 번째 정수를 가리킬 것이다.  
```asm
.data
bigArray DWORD 500 DUP(?)
pArray DWORD bigArray
.code
mov esi, pArray
```
위의 코드는 결과적으로 포인터를 만든 예시를 보인다.  
pArray는 bigArray의 시작을 가리키고 코드에서 esi에 pArray를 적재하였으므로 esi는 배열의 시작을 가리킬 것이다.  

---
### ALIGN 디렉티브
변수를 바이트, 워드, 더블워드 또는 문단의 경계에 정렬한다. **ALIGN bound**와 같이 사용한다.  
bound는 1, 2, 4 또는 16일 수 있다. 
```asm
bVal BYTE ?        ; 404000h
ALIGN 2            
wVal WORD ?        ; 404002h
bVal2 BYTE ?       ; 404004h
ALIGN 4
dVal DWORD ?       ; 404008h
dVal2 DWORD ?      ; 40400Ch
```
위의 코드에서 bVal은 BYTE니까 다음 변수의 위치는 404001이지만 ALIGN으로 경계를 2의 배수로 바꾸어 404002에 저장되었다.  
bVal2는 BYTE이므로 다음 변수의 위치는 404006이지만 경계를 다시 4로 바꾸었으므로 4의 배수인 404008에 저장되었다.  
CPU는 홀수 주소에 저장된 데이터보다는 짝수 주소에 저장된 데이터를 더 빠르게 처리할 수 있어 이런 디렉티브를 사용한다.  

---
### PTR 연산자
피연산자의 선언된 크기를 바꾸어 사용하기 위해 사용할 수 있다.
```asm
.data
myDouble DWORD 12345678h
.code
mov ax, myDouble        ; error
```
예로, myDouble의 하위 16비트를 AX로 이동시키려하면 두 피연산자의 크기가 다르므로 error가 날 것이다.  
**mov ax, WORD PTR myDouble** 은 이를 가능하게 한다.  
이때는 myDouble의 하위비트인 5678h가 AX에 들어가게 되는데, 이는 변수의 시작주소에 하위 바이트가 저장되는 리틀 엔디안 방식을 사용하기 때문이다.  
myDouble은 4바이트에 걸쳐 12345678h를 저장하지만 00 => 78h, 01 => 56h, 02 => 34h, 03 => 12h 과 같이 반대로 저장된다.  
WORD만큼을 AX에 저장하고자 하면 00, 01번지의 값만 들고와 AX에 5678h이 들어가는 식이다.  
1234h를 저장하고 싶다면 **mov ax, WORD PTR [myDouble + 2]** 와 같이 사용하면 된다.  

---
### TYPE 연산자
변수의 단일 원소의 크기를 바이트 단위로 반환한다.  
BYTE의 TYPE은 1, WORD의 TYPE은 2, DWORD의 TYPE은 4, QWORD의 TYPE은 8이다.  

---
### LENGTHOF 연산자
레이블과 같은 줄에 있는 값들로 정의되는 배열에 있는 원소의 개수를 반환한다.  
```asm
.data
byte1 BYTE 10, 20, 30
array1 WORD 30 DUP(?), 0, 0
array2 WORD 5 DUP(3 DUP(?))
array3 DWORD 1, 2, 3, 4
digitStr BYTE "12345678", 0
```
배열의 정의에서 DUP 연산자가 중첩되어 사용되면 LENGTHOF 연산자는 두 카운터의 곱을 반환하며 각 줄의 반환값은 다음과 같다.  
3 / 30+2 / 5*3 / 4 / 9  
배열을 여러줄에 걸쳐 선언해도 LENGTHOF는 첫 번째 줄의 데이터만을 배열 부분으로 간주하여 반환한다. 다만 첫 번째 줄을 컴마로 끝내고  
초기값을 다음 줄로 계속하여 나열할 수 있다. 이러면 LENGTHOF는 다음 줄 까지 배열로 간주한다.  

---
### SIZEOF 연산자
LENGTHOF와 TYPE을 곱한 값을 반환한다. 즉, 몇 바이트만큼을 차지하는지를 알려준다.  
**intArray WORD 32 DUP(?)** 이라면 SIZEOF intArray는 **64**이다.  

---
### LABLE 디렉티브
저장공간을 할당하지 않으면서 레이블을 넣고 그것에 크기 속성을 주게 한다.  
모든 크기 표준 속성이 이와 함께 사용될 수 있는데, LABEL의 일반적인 용도는 데이터 세그먼트에서 다음에 선언된 변수에 대해
다른 이름과 크기 속성을 제공하는 것이다.
```asm
.data
val16 LABEL WORD
val32 DWORD 12345678h
.code
mov ax, val16          ; AX = 5678h
mov dx, [val16 + 2]    ; DX = 1234h
```
val16은 다음에 선언된 val32라는 이름의 저장공간에 대한 별명이다. LABEL 디렉티브는 어떠한 저장공간도 할당하지 않는다.  
```asm
.data
LongValue LABEL DWORD
val1 WORD 5678h
val2 WORD 1234h
.code
mov eax, LongValue      ; EAX = 12345678h
```
다음과 같이 두 개의 작은 정수로부터 큰 정수를 만들 때 LABEL을 사용할 수도 있다.  

---
## 간접 주소지정
보호 모드에서 대괄호로 둘러싸인 임의의 32비트 범용 레지스터를 간접 피연산자로 하여 오프셋을 역참조한 뒤 값을 가져온다.  
```asm
.data
byteVal BYTE 10h
.code
mov esi, OFFSET byteVal
mov al, [esi]            ; AL = 10h
inc [esi]                ; error : operand must have size
inc BYTE PTR [esi]
```
위의 코드에서는 ESI가 byteVal 변수의 오프셋을 포함한다.  
mov 명령어는 간접 피연산자를 소스로 사용하고 ESI에 있는 오프셋이 역참조되어 바이트가 AL로 이동한다.  
목적지 피연산자가 간접 주소지정을 사용하면 소스의 내용이 [destination]으로 주소지정 되는 메모리 위치로 복사된다.  
inc [esi] 에서는 오류를 보이는데 피연산자의 크기가 명령어의 문맥상 명확하지 않을 수 있기때문에 PTR 연산자를 같이 사용하여 크기를 지정한다.  

---
## 배열
간접 피연산자는 배열을 처리하기에 적합하다.  
```asm
.data
arrayB BYTE 10h, 20h, 30h
.code
mov esi, OFFSET arrayB
mov al, [esi]        ; AL = 10h
inc esi
mov al, [esi]        ; AL = 20h
inc esi
mov al, [esi]        ; AL = 30h
```
위의 코드에서 ESI의 값을 증가시킴에 따라서 ESI는 각 바이트를 순서대로 가리킨다.  
배열이 WORD를 사용한다면 inc esi 대신에 **add esi, 2** 를 사용하면 된다. DWORD라면 4씩 더해주면 될 것이다.  
위의 코드 대신 [esi + n] 과 같이 n에 상수를 더하여 유효 주소를 만들 수도 있다. 이를 **인덱스 피연산자**라고 한다.  
[arrayB + esi]와 같이 접근해도 되지만, 이전에 esi를 미리 0으로 초기화시켜야 할 것이다.  
실제 주소 모드에서는 16비트 레지스터를 인덱스 피연산자로 사용하무로 이 때 사용하는 레지스터는 **SI, DI, BX, BP**로 제한해야 한다.  
배율 인자로도 계산할 수 있는데, esi를 임의의 수로 설정한 뒤 [esi * TYPE arrayB] 와 같은 방식으로 원소를 찾을 수도 있다.  

---
## 포인터
다른 변수의 주소를 포함하는 변수인 포인터또한 다룰 수 있다.  
```asm
.data
arrayB BYTE 10h, 20h, 30h, 40h
ptrB DWORD arrayB
; 관계를 명확히 하기 위해 이런식으로 나타낼 수도 있다.
ptrB DWORD OFFSET arrayB
```
이 경우 ptrB는 arrayB의 오프셋을 포함한다.

---
## TYPE 연산자 사용
TYPEDEF 연산자는 사용자 정의 자료형을 만들 수 있게 한다.
```asm
PBYTE TYPEDEF PTR BYTE
```
이렇게 정의하면 이후 **PTR BYTE** 대신에 **PBYTE** 로 사용할 수 있다.  

---
## JMP와 LOOP
JMP는 코드 레이블로 표시되는 목적지로 무조건 이동시키는 반복문이다.  
```asm
top:
    .
    .
    .
    jmp top
```
top으로 무한 루프하는 JMP문이다. 빠져나가는 방법을 정의해 두어야 하는 무조건 이동문이다.  
LOOP도 동일하게 사용하고 동일하게 반복하지만 **묵시적으로 ECX의 값을 1씩 감소**한다.  
0이 될 때까지 반복하는 조건부 이동문으로, LOOP를 실행하기 이전에 반복 횟수를 ECX에 넣어야 한다.  

---
