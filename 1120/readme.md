reference  
>

---

---
# 1120 수업 정리  
## 서브루틴  
C나 C++에서는 함수(function)  
자바에서는 메서드(method)  
MASM에서는 프로시저(procedure)  
라고 한다.  

호출하는 프로그램이 서브루틴에게 전달되는 값을 `인수/인자(argument(s))` 라고 하며, 호출된 서브루틴이 이 전달된 값을 받을 때에는 `매개변수(parameter(s))` 라고 한다.  

---
## 스택 프레임  
스텍 프레임(또는 활성 레코드/activation record)은 전달되는 인수, 복귀 주소, 지역 변수, 레지스터 저장을 위해 확보한 스택 영역이다.  
프로시저를 시작할 때에, 프레임이 만들어지고 `(있다면)전달되는 인수 > 복귀 주소 > EBP > (필요시)지역 변수 공간 > 레지스터` 순으로 확보한다.  
기본적으로 stack에 무언가를 저장할 때에는 4바이트가 필요하다.(push와 pop 명령어로 알 수 있음)  

---
## 스택 매개변수  
이전에는 프로시저로 인수 전달을 위해 레지스터를 사용하였지만, 이로 인한 push와 pop 명령어는 코드를 더럽게 만들 수 있다.  
스택에 푸쉬되는 인수는 대개 2가지이다. `1. 값 인수, 2. 참조 인수`  
값 인수는 변수, 상수의 값을 그대로 저장하는 방식이고 > `push val1`  
참조 인수는 변수의 주소값을 저장하는 방식이다. > `push OFFSET val1`  
고급 언어(C/C++)의 경우 참조 인수로 값을 전달할 때 `X(&val1);`과 같이 인수의 주소를 전달한다.  

배열을 전달할 때는 배열 이름을 오프셋으로 전달하면 된다. 배열 이름은 배열 공간의 첫 주소이다.  
### 명시적 스택 매개변수  
서브루틴을 위한 인수가 저장되고, 호출이 되면 복귀 주소가 저장되고 EBP가 다음에 들어오기에 `[EBP+4]`는 복귀 주소, `[EBP+8 + 4x] (x >= 0)`는 인수들이다.  
x+y를 위한 서브루틴을 만들고 싶다고 할 때, 스택에 y, x 순으로 저장했다면
```asm
y_param EQU [ebp+12]
x_param EQU [ebp+8]
```
과 같이 매개변수의 오프셋을 상수값으로 명시할 수도 있다.  

---
## 스택 정리  
매개변수에 관해 살펴보았는데, 서브루틴은 꼭 인수만을 받아 실행하지만은 않는다.  
서브루틴 내에서 지역 변수를 놓을 수도 있는데, 이는 EBP다음에 설정되는 값이다.(지역변수는 `[ebp-4x] (x>0)`의 값을 가진다.)  

---
## 지역 변수  
고급 언어에서 한 서브루틴 내에서 생성되어 사용되고, 소멸하는 변수를 지역 변수라고 한다.  
이러한 지역 변수는 지역 변수가 선언된 서브루틴 내의 문장들만 접근하여 보거나 수정이 가능하고, 이름 충돌을 일으키지 않아 또 다른 서브루틴의 지역 변수와 같은 이름을 가질 수도 있다.  
아까도 말했듯이 지역변수는 EBP아래에 생성되어 `[ebp-4],[ebp-8]`과 같은 값을 가진다.  

---
## LEA 명령어  
간접 피연산자의 오프셋을 반환한다.  
단순 OFFSET 지시어와 달리 실행 시간에 계산된다.  
`LEA esi, [ebp-8]`과 같은 명령이 가능하다. OFFSET은 컴파일 시간에 작업하므로 실행 시간에 달라지는 저런 계산식을 사용할 수 없다.  

---
## ENTER와 LEAVE 명령어  
매개변수 용으로 4바이트씩, 복귀를 위한 주소 4바이트를 합하면 각 호출이 해당 바이트만큼의 스택 공간을 사용한다.    
이런식으로 사용된 스택을 정리해주지 않으면 루프 내에서 프로시저를 계속 호출할 때 오버플로될 수 있다.  
이럴 때는 `mov esp, ebp`로 사용한 인수들을 지워 스택을 정리한다.  
또는 스택에 넣은 지역 변수 n개 만큼 `ret 4n`으로 스택 정리를 해주면 된다.(개당 4바이트이므로)  
이는 STDCALL이라고 하는 호출 규약을 이용한 방법이다. 리턴하고 `add esp, 4n` 만큼의 값을 더해준다.  
ENTER 명령어는  
```asm
push ebp      ; ebp 스택에 푸쉬
mov ebp, esp  ; ebp를 스택 프레임의 시작 주소로 설정
sub esp, num  ; 지역 변수를 위한 공간 확보
```
를 자동으로 해준다. `ENTER n, n`과 같이 사용한다. 두 n은 모두 즉시값으로 첫번째는 지역 변수를 위해 확보할 공간만큼 4의 배수로 적어주면 된다.  
LEAVE는 ENTER와 대응되어 알아서 스택을 정리해준다.  
```asm
mov esp, ebp
pop ebp
```

---
## LOCAL 지시어  
하나 이상의 지역 변수를 이름으로 선언한다. ENTER 명령어는 이름이 부여되지 않은 지역 변수를 위한 단일 블록의 스택 공간만을 확보하지만 이는 여러 개의 지역 변수를 선언하여 추가 속성을 부여한다.  
`LOCAL varlist`와 같이 사용하며 varlist는 콤마로 구분된 여러개의`label:type` 형식의 지역 변수이다.  
type은 BYTE, WORD, DWORD와 같은 표준 자료형이다.  

---
## 재귀  
자기자신을 직접 혹은 간접적으로 호출하는 서브루틴을 재귀 서브루틴이라고 한다.  
```asm
Endless PROC
  mov edx, OFFSET endlessStr
  call WriteString
  call Endless
  ret
Endless ENDP
```
는 재귀 서브루틴의 예시이며 조건없이 계속하여 자기자신을 실행하므로 ret 명령어는 절대로 실행되지 않는다.  
```asm
CalcSum PROC
  cmp ecx, 0
  add eax, ecx
  dec ecx
  call CalcSum
  L2: ret
CalcSum ENDP
```
와 같은 서브루틴은 마찬가지로 재귀 서브루틴이지만 여기는 ecx와 eax에 있는 값을 인수로 받아(ecx에 실행 횟수) ecx가 0이 될 때까지 실행하는 서브루틴이다. ret 명령어를 처음으로 만나면 이전에 호출된 서브루틴으로 되돌아가며 이와 같이 반복되어 실행될 것이다.  

---
## INVOKE 지시어  
스택에 인수를 넣고 프로시저를 호출한다. INVOKE 한 줄로 여러 개의 인수를 전달할 수 있으므로 call 명령어 대신 사용할 수 있다.  
`INVOKE procName [, argumentlist]`의 형식으로 사용하며 프로시저의 이름 뒤에는 콤마로 구분되는 프로시저에 전달되는 인수의 리스트를 넣으면 된다.  
예를 들어, DumpArray를 호출할 때에 이런 식으로 호출한다고 가정한다.  
```asm
push TYPE array
push LENGTHOF array
push OFFSET array
call DumpArray
```
이걸 한 줄로 줄일 수 있다.  
```asm
INVOKE DumpArray, OFFSET array, LENGTHOF array, TYPE array
```
MODEL 디렉티브의 언어 지정자가 지정하는 순서대로 스택에 넣는다.  
STDCALL을 사용하므로 오른쪽에서부터 왼쪽 순으로 스택에 넣어준다.  

---
## ADDR 연산자  
INVOKE를 사용하여 프로시저를 호출할 때에 포인터 인수를 전달하는 데 사용될 수 있다.   
성능은 LEA 명령어와 비슷하다. 자동으로 스택에 PUSH해주는 LEA 명령어이다.  
다만 어셈블리 시간에 ADDR에 대한 인수는 상수만 올 수 있다.  
`INVOKE mySub, ADDR [ebp+12]`와 같이는 사용할 수 없다.  
또한 INVOKE 지시어와만 사용할 수 있다.

---
## PROC 지시어  
`label PROC [attributes] [USES reglist] [parameter_list]`과 같이 사용한다.  
label : 식별자 규칙을 따르는 사용자 정의 레이블이다.  
attributes : 속성이다. (distance, langtype, visibility, prologue)    
>distance : NEAR 또는 FAR. 어셈블러가 생성하는 RET 또는 RETF의 유형을 나타낸다.  
>langtype : 호출 규약(우리는 보통 STDCALL)을 지정. .MODEL 디렉티브에서 지정한 언어에 우선한다.  
>visibility : 다른 모듈에 대한 가시성이다. public 또는 private, EXPORT 중 선택한다.  
>prologue : 프로시저의 시작과 마지막 코드의 생성에 영향을 주는 인수를 지정한다.   

parameter_list : 콤마로 구분되는 매개변수의 리스트를 갖는 프로시저를 선언할 수 있게 해준다.  
`매개변수이름:type`의 형식으로 사용된다.  
이때 해당 매개변수이름의 유효 범위는 현재 프로시저로 제한된다.  
같은 매개변수이름이 여러 프로시저에서 사용될 수는 있지만 전역 변수의 이름이나 코드 레이블의 이름일 수는 없다.  
```asm
AddTwo PROC,
  val1:DWORD,        ; push ebp
  val2:DWORD         ; mov ebp, esp               => 해당 두 줄 대신 ENTER 0, 0도 가능능
  mov eax, val1      ; mov eax, DWORD PTR[ebp+8]
  add eax, val2      ; add eax, DWORD PTR[ebp+12]
                     ; leave
  ret                ; ret 8
AddTwo ENDP
```
두 프로시저는 같다.  
PROC에 하나 이상의 매개변수가 함께 사용되고 STDCALL 호출 규약을 사용한다면 MASM은 n개의 매개변수에 대해 처음과 마지막에 다음과 같은 코드를 만들어낸다.  
```asm
push ebp
mov ebp, esp
.
.
.
ret (nx4)
```
편하다.  

---
## PROTO 지시어  
존재하는 프로시저를 위한 프로토타입을 만든다.  
프로시저의 이름과 매개변수 리스트를 선언하며, 프로시저를 정의하기 전에 호출할 수 있게 하고 인수 개수와 유형이 프로시저의 정의와 일치하는지 검사할 수 있게 한다.  
프로시저에 대한 PROTO는 INVOKE 이전에 나타나야 한다.  
프로시저의 구현이 INVOKE 이전에 나타나 있다면 구현된 프로시저(PROC)가 원형으로서 동작한다.   

## 프로시저 숨김  
기본적으로 MASM은 프로시저를 public으로 만들어 같은 프로그램의 어떤 모듈에서도 호출될 수 있게 한다.  
`mySub PROC PRIVATE`와 같이 PRIVATE 한정자를 사용하여 바꿀 수 있다.  
이러면 모듈 내부의 프로시저를 숨기는 캡슐화 원리를 사용하며 다른 모듈에 있는 프로시저가 같은 이름을 갖고 있을 때에 일어날 수 있는 이름 충돌을 피할 수 있다.  
또는 `OPTION PROC:PRIVATE`을 파일의 꼭대기에 놓는다. 이러면 모든 프로시저가 기본적으로 private 속성을 가진다.  
이때 익스포트 하기를 웧나는 프로시저는 PUBLIC 디렉티브를 사용하여 지정한다.  
```asm
OPTION PROC:PRIVATE
PUBLIC mySub
; or
mySub PROC PUBLIC
.
.
muSub ENDP
```
PUBLIC 지시어는 콤마로 구분되는 이름의 리스트를 가진다.  
아니면 프로시저를 개별적으로 PUBLIC으로 지정한다.  

---
## 외부 프로시저 호출  
```asm
INCLUDE Irvine32.inc
EXTERN sub1@0:PROC
.code
main PROC
  call sub1@0:PROC
  exit
main ENDP
END main
```
현재 모듈 외부의 프로시저를 호출할 때는 EXTERN 지시어를 사용한다.  
해당 파일에 호출한 프로시저가 없다면 오류 메시지를 내보내지만, EXTERN은 다른 파일에 해당 프로시저가 있으니 에러 메시지를 보내지 않게 한다. 그러고는 링커가 실행 파일을 만들 때에 그 프로시저에 대한 빈 주소를 만들게 하여 연결한다.   
프로시저 이름 끝에 붙는 `@n` 접미사는 선언된 매개변수가 사용하는 전체 스택 공간을 지정한다.  
선언된 매개변수가 없는 기본적인 PROC 지시어를 사용한 프로시저에 대해서는 @0이 붙겠지만  
```asm
AddTwo PROC,
  val1:DWORD,
  val2:DWORD
  .
  .
AddTwo ENDP
```
와 같은 AddTwo 프로시저가 있다면? 선언된 매개변수가 스택공간을 추가로 8바이트 더 사용하므로 `@8`이 붙을 것이다.  
```asm
EXTERN AddTwo@8:PROC
```
INVOKE를 이용하여 해당 프로시저를 호출하려고 한다면 EXTERN 대신에 PROTO를 사용한다.  
```asm
AddTwo PROTO,
  val1:DWORD,
  val2:DWORD
```

## 변수와 심볼 사용  
변수와 심볼은 기본적으로 private으로, 이들을 포함하는 모듈에서만 접근 가능하다.  
PUBLIC을 사용하면 물론 바깥에서 사용할 수 있도록 익스포트할 수 있다.  
```asm
PUBLIC count, SYM1
SYM1 = 10
.data
count DWORD 0
```
EXTERN을 사용하여 외부 모듈에서 정의된 변수와 심볼에 접근할 수 있다.  
```asm
EXTERN name:type  ; 콤마로 구분되는 여러개의 변수, 심볼 가능
```
EQU와 = 로 정의되는 심볼에 대해서는 type은 `ABS` 여야 한다. 변수에 대해서는 BYTE, WORD 등이며 PTR을 포함할 수 있다.  

## EXTERNDEF  
PUBLIC과 EXTERN을 합쳤다.  
외부에서의 접근을 허용할 때는 PUBLIC, 외부에서 가져올 때는 EXTERN을 사용하는데, 이 지시어는 텍스트 파일에 놓여 INCLUDE 문으로 각 프로그램 모듈에 복사될 수 있다.  

다음 선언이 포함되어있는 var.inc 파일을 정의했다고 하자.
```asm
; vars.inc
EXTERNDEF count:DWORD, SYM1:ABS
```
다음으로 count와 SYM1과 vars.inc를 컴파일 스트림으로 복사하는 INCLUDE 문이 포함된 sub1.asm이라는 소스 파일을 만든다.  
```asm
TITLE sub1.asm
.386
.mode flat, STDCALL  ; .STACK 불필요
INCLUDE vars.inc
SYM1 = 10
.data
count DWORD 0
END ; END main 불필요
```
이는 프로그램의 시작 모듈이 아니므로 END 디렉티브에 있는 프로그램 시작지점 레이블을 생략하고 실행시간 스택을 선언하지 않아도 된다.  
다음으로 이 두 파일을 포함, 참조하는 main.asm 시작 모듈을 만든다.  
```asm
TITLE main.asm
INCLUDE Irvine32.inc
INCLUDE vars.inc

.code
main PROC
  mov count, 2000h
  mov eax, SYM1
  exit
main ENDP
END main
```
해당 모듈은 Irvine32.inc내에 선언된 .STACK/ 실행시간 스택을 포함하고, END 디렉티브에서 프로그램 시작지점도 정의한다.  
