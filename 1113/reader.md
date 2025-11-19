reference
>Chat GPT  
>Cursor AI  
>blog.naver.com/heobk11- / 어셈블리 언어  
>intelligentcm.tistory.com/category/Assembly  
>Assembly Language for x86 processors 7th Edition  
---

---
# 1113 수업 정리  
## 논리 시프트(Logical Shifts)와 산술 시프트(Arithmetic Shifts)  
논리 시프트는 주어진 수와 방향 만큼 비트를 밀어내고 빈자리에는 `0`, 밀어낸 비트는 `Carry Flag`에 넣는다.  
<img width="473" height="224" alt="image" src="https://github.com/user-attachments/assets/a6561b38-cc11-483b-9ad3-5e02552fbe77" />  
산술 시프트는 왼쪽 시프트의 경우 MSB를 밀어내고 남은 자리에 복사하여 넣고, 밀어낸 비는 `Carry Flag`에 넣는다.  
<img width="471" height="228" alt="image" src="https://github.com/user-attachments/assets/bca11ca3-207b-4f0e-889b-38f0efc65ff9" />  

---
## SHL과 SHR  
SHL(<<)은 목적지 오퍼랜드에 대해서 최하위 비트를 0으로 채우는 왼쪽 논리 시프트를 수행한다.  
마지막으로 밀어낸 비트는 CF에 저장된다.  
첫번째 피연산자는 목적지, 두번째 피연산자는 시프트 횟수이다.  
ex) `11001111`을 1비트 왼쪽으로 시프트하면 `10011110`이 된다. (CF = 1)  
목적지로는 레지스터와 메모리, 시프트 횟수로는 8비트 즉시값과 CL을 사용할 수 있다.(이는 RCR, RCL까지도 적용된다.)   

SHR(>>)은 목적지 피연산자에 대해서 최상위 비트를 0으로 채우는 오른쪽 논리 시프트를 수행한다.  
SHL과 동일하고 방향만 반대로 수행한다.  

binary 수 특성상 시프트 횟수 i당, Shift Left는 x 2ⁱ / Shift Right는 ÷ 2ⁱ 와 같다.  
만약 CF가 1로 세팅된다면(값을 소실) 곱셈, 나눗셈 처리에서의 오버플로우를 의미한다.  

## SAL과 SAR  
SAL은 SHL과 같은 동작을 한다.  
그렇기에 고급 언어에는 `<<<`과 같은 명령이 없다.  

SAR(>>>)은 오른쪽으로 밀어낸다는 것은 똑같으나, 최상위 비트는 원래의 MSB를 복사하여 할당한다.  

## ROL과 ROR  
rotate 명령어인 ROL과 ROR은 비트를 잃지 않고 옮기는 방법이다.  
ROL은 왼쪽, ROR은 오른쪽으로 밀어내며, 밀어낸 수는 CF, 빈자리에 각각 들어간다.  

<img width="420" height="82" alt="image" src="https://github.com/user-attachments/assets/59114bcc-1167-4b64-936e-f6a34de6c3f3" /> **ROL**  
<img width="420" height="82" alt="image" src="https://github.com/user-attachments/assets/5ff093bf-dd33-4a4a-944e-1b062b12ba29" /> **ROR**  

1보다 큰 회전 카운터를 사용할 때, CF는 마지막으로 회전되어 나온 비트를 포함한다.  
`al = 62h` 일 때 `ROL al, 4`를 수행하면 `al = 26h`가 된다.  
어느 방향으로든 4비트씩 회전하면 옆자리의 수를 가져올 수 있다.  

## RCL과 RCR  
마찬가지로 rotate를 하되, CF에 밀어낸 비트를 넣고, CF에 있던 비트를 빈자리에 넣는다.  
CF와 함께 9자리로 돌아가는 형식이다.  

<img width="420" height="82" alt="image" src="https://github.com/user-attachments/assets/39976d79-7ac6-4e6e-a85b-3d87c9bcf93d" /> **RCL**  
<img width="420" height="82" alt="image" src="https://github.com/user-attachments/assets/55a5b452-7758-4b4e-934f-588265990e8a" /> **RCR**  

## 부호있는 오버플로우  
Overflow 플래그는 부호있는 값을 한 비트 시프트/회전할 때 목적지 피연산자의 부호있는 정수 범위 바깥에 있는 값을 만들어내면 1로 설정된다.  
부호있는 오버플로우 플래그가 셋 되었음을 다른 말로 나타내면 '부호가 바뀌었다'라고 할 수도 있다.  
시프트/회전 수가 1보다 크면 오버플로우 플래그는 정의되지 않는다.  

## SHLD와 SHRD  
목적지 피연산자를 지정된 비트 수만큼 양 방향으로 시프트하는 명령어이다.  
시프트로 비어있게 되는 비트 위치에는 소스 피연산자의 최상위/최하위 비트로 채워진다.  
SHLD와 SHRD 명령어로 올 수 있는 목적지, 소스 오퍼랜드는 16/32비트 레지스터. 카운터 횟수는 8비트 즉시값과 CL만이 가능하다.  
레지스터의 값들을 결합하고 시프트하는 명령어가 된다.  

<img width="711" height="231" alt="image" src="https://github.com/user-attachments/assets/4334b877-d409-4021-8978-904ae442f71f" /> **SHLD**  
<img width="711" height="231" alt="image" src="https://github.com/user-attachments/assets/da8a3433-758b-4763-bba1-d6135bfb6687" /> **SHRD**  

## MUL 명령어  
오퍼랜드는 한 개만 사용가능하며, 해당 오퍼랜드와 정해진 값을 곱해 정해진 곳에 넣는다.  
8/16/32비트의 레지스터/메모리만 사용 가능하며 동작과정에서 묵시적으로 EAX, EDX 레지스터가 사용된다.  
>8비트 : AL를 곱하고 AX에 결과를 넣는다.  
>16비트 : AX를 곱하고 DX : AX에 결과를 넣는다.  
>32비트 : EAX를 곱하고 EDX : EAX에 결과를 넣는다.  

목적지 피연산자가 피승수와 승수의 2배 크기이므로 오버플로우는 발생할 수 없다.  
MUL은 곱의 상반부(AH, DX, EDX)가 0이 아니면 Carry와 Overflow 플래그를 1로 설정한다.  
부호없는 산술이므로 Carry 플래그를 보자면 이 역할은 곱의 상반부(AX, DX, EDX)를 무시해도 되는지 여부를 체크하는 것이다.  
원래 있던 레지스터에 값을 다 담을 수 없다면 오버플로우인 것이다.  
곱의 상반부가 0이 아닌, 다른 값으로 채워졌다면 캐리 플래그가 세팅되며, 곱이 하반부에 저장되지 못함을 알려준다.  

## IMUL 명령어  
IMUL 명령어는 부호있는 정수 곱셈을 수행한다. 곱의 부호를 유지한다.  
해당 동작은 곱의 하반부의 최상위 비트를 곱의 상위 비트들로 확장하여 수행한다.  
IMUL은 이항/ 삼항 형식의 명령어까지 지원한다.  
1항의 경우, 피승수와 저장 장소는 MUL과 동일하다.  

이항의 경우, 피승수는 16/32비트 레지스터, 승수는 16비트의 경우(16비트 레지스터/메모리, 8/16비트 즉시값), 32비트의 경우(32비트 레지스터/메모리, 8/32비트 즉시값)이다.   
각각 dest, src    

<img width="543" height="71" alt="image" src="https://github.com/user-attachments/assets/82c69114-06cc-41d6-9bd3-5bfc475fd7fd" />  


삼항의 경우, 두 번째 피연산자와 세 번째 피연산자의 값을 곱하고, 첫 번째 피연산자에는 곱을 저장한다.  
16비트의 경우(16비트 레지스터/메모리와, 8/16비트 즉시값), 32비트의 경우(32비트 레지스터/메모리와, 8/32비트 즉시값)이다.  
각각 dest, src, imm  

<img width="543" height="71" alt="image" src="https://github.com/user-attachments/assets/a36d13f3-c058-4352-88a5-0ef857fef68b" />  

IMUL명령어의 이항/삼항 형식의 경우, 목적지 피연산자의 크기만큼 값을 잘라 저장하므로 유호 자리가 손실되면 Carry, Overflow 플래그가 1로 설정된다.  
오퍼랜드가 2개 이상이면 첫 번째 오퍼랜드로 목적지를 설정한다.  

## DIV 명령어  
부호없는 정수의 나눗셈을 수행한다. 제수로서 쓰이는 수는 MUL 명령어와 같다.
제수의 비트에 따라 피제수와 저장 장소는 다음과 같다.  
>피제수 : 8비트 = AX / 16비트 = DX : AX / 32비트 = EDX : EAX  
>8비트 : 몫 = AL, 나머지 = AH / 16비트 : 몫 = AX, 나머지 = DX / 32비트 : 몫 = EAX, 나머지 = EDX  

<img width="500" height="141" alt="image" src="https://github.com/user-attachments/assets/9c40fc4b-6e56-4131-9244-889c6c608f3c" />   

## 부호 확장 명령어  
부호있는 정수의 나눗셈을 수행하기 전, 해야하는 행동이다.  
제공되는 부호 확장 명령어는 CBW, CWD, CDQ 이다.  
각각 BYTE to WORD, WORD to DWORD, DWORD to QWORD이다.  

이 명령어들은 별도의 오퍼랜드가 필요 없으며 각각의 대상 레지스터를 가진다.  
명령어들은 해당 레지스터의 MSB로 다음 레지스터를 채운다.  
>CBW : AL => AX  
>CWD : AX => DX : AX  
>CDQ : EAX => EDX : EAX  

AL에 `9Fh` 값이 들어있을 때, CBW는 AX를 `FF9Fh`로 값을 변경한다.  

## IDIV 명령어  
부호있는 정수 나눗셈을 수행한다.  
부호 확장 명령어를 이전에 사용해주어야 한다는 것 말고는 DIV 명령어와 거의 같다.  
DIV와 IDIV 명령어를 수행한 후의 모든 상태 플래그 값은 정의되어 있지 않다.  

## 나눗셈 오버플로우  
나눗셈 피연산자가 목적지 피연산자에 들어갈 수 없는 큰 값의 몫을 만들어내면 나눗셈 오버플로우 조건이 발생한다.  
ex) 1000h ÷ 10h = 100h <= 목적지 피연산자인 1바이트 크기에 들어가지 못함.  
해당 오버플로우는 CPU 인터럽트를 발생, 현재의 프로그램은 정지한다.  
0으로 나누는 명령어를 작성해도 조금 다르지만 오버플로우 조건을 충족, 인터럽트가 발생한다.  

## 확장 덧셈과 뺄셈(ADC와 SBB)  
가능한 피연산자는 ADD와 같다.  
두 오퍼랜드를 더하여/빼서 목적지에 저장하지만 더하는/빼는 값에는 CF도 포함한다.  
경우에 따라 추가로 ±1을 하는 것이다.  
ADC의 경우 AL의 덧셈결과를 DL에 확장 가능하다.  
SBB의 경우 EDX : EAX로 확장된 값의 뺄셈을 CF로 반영 가능하다.  
여러개의 레지스터/메모리를 이어붙여 매우 큰 수의 덧셈/뺄셈 작업이 가능하다.  

## 비압축 10진 산술 연산  
비압축 10진수는 한 바이트를 사용하여 10진수 숫자 하나를 표현하는 방식이다.  
AX의 경우 AH는 10의 자리, AL는 1의 자리를 뜻한다.  
비압축 10진수의 경우 상위 4비트는 항상 0인 반면에, ASCII 10진수의 같은 비트는 0011b 이다.  
AAA와 AAS는 산술 연산 수행시, 이전에 문자열 변환이 필요하지 않다는 장점이 있다.  
다만 입력 범위가 '0' ~ '9' 사이여야 하고, 20 이상의 수를 표현할 수 없다는 단점도 있다.  
```asm
mov ah, 0
mov al, '8'    ; AL = 0038h
add al, '2'    ; AL = 006Ah
aaa            ; AX = 0100h = '10'
```
AAA 명령어는 AF = 1 이거나 `AND AL, 0Fh` 결과 값이 9보다 크다면 al +6을 하고 ah +1을 한다.  
아니라면 `AND AL, 0Fh`만 한다.  

## 압축 10진 산술 연산  
압축 10진수는 한 바이트에 10진수 두 자리를 저장한다.  
각 자리는 4비트로 나타낸다.  
35 + 48은 각각 35h, 48h로 나타내며, 둘을 합한 뒤 DAA 명령어로 값을 고친다.  
AAA의 판정과 비슷하게  
>하위 니블이 9를 넘거나, AF=1이라면 06h를 더하고  
>상위 니블이 9를 넘거나, CF=1이라면 60h를 더한다.  

100h을 넘는 수는 AL에 모두 담지는 못하고 CF를 세팅함으로서 올림이 필요함을 알린다.  

동일하게 DAS는 뺄셈 결과를 압축 10진수 형식으로 변환한다.  