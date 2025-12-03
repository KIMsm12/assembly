reference
>Chat GPT  
>blog.naver.com/heobk11- / 어셈블리 언어  
>intelligentcm.tistory.com/category/Assembly  
>Assembly Language for x86 processors 7th Edition  
---

---
# 1127 수업 정리  
## 스트링 프리미티브 명령어  
바이트, 워드, 더블워드 배열을 처리하기 위한 5개 그룹의 명령어이다.  
모두 공통적으로 명령어 뒷부분에 S(string)가 붙고 크기에 따라 B(byte), W(word), D(doubleword)로 나뉜다.  
스트링 프리미티브 명령어들은 32비트 모드에서 묵시적으로 ESI, EDI 레지스터를 사용하며 참조용으로 누산기 레지스터(AL/AX/EAX)를 사용하기도 한다.  
앞에서 보던 명령어들은 대개 mem to mem의 형태를 허용하지 않았지만 이 문자열 명령어들은 mem to mem을 허용한다.  
스트링 프리미티브는 자동으로 반복 실행되며 배열 인덱스를 증가시키기에 효율적으로 실행된다.  
그러니까 스트링 프리미티브 명령어에 사용되는 ESI나 EDI값은 자동으로 1/2/4씩 증가 또는 감소한다.(방향 플래그에 따라)  

## 접두사 repeat  
`rep (스트링 프리미티브)`  
의 방식으로 명령어를 반복 사용할 수 있다.  
기본적으로 스트링 프리미티브 명령어는 하나의 메모리 혹은 한 쌍의 값만을 처리하는데,  
접두사 repeat으로 ECX의 값만큼 반복하여 실행 가능하다.  
rep 뿐만 아니라 `REPZ, REPE` , `REPNZ, REPNE` 또한 사용 가능한데,  
ECX > 0일 동안 반복할 뿐 아니라  
REPZ, REPE : ZERO FLAG가 1인 동안  
REPNZ, REPNE : ZERO FLAG가 0인 동안  
반복한다.  

또한 스트링 프리미티브 명령어는 방향 플래그(Direction Flag)에 따라 ESI, EDI를 증가시키거나 감소시킨다.  
1이라면 작은 값으로 감소(역방향)  
0이라면 큰 값으로 증가(정방향)한다.  
CLD, STD 명령어로 설정 가능하다.  

## MOVSB, MOVSW, MOVSD  
ESI가 가리키는 메모리 위치에서 EDI가 가리키는 메모리 위치로 값을 복사한다.  

## CMPSB, CMPSW, CMPSD  
ESI가 가리키는 메모리 피연산자와 EDI가 가리키는 메모리 피연산자를 비교한다.  
cmp의 기준은 ESI이다.  

## SCASB, SCASW, SCASD  
AL/AX/EAX의 값을 EDI가 주소지정하는 바이트 / 워드 / 더블워드와 각각 비교한다.  
SCAN의 기준은 AL/AX/EAX이다.  

## STOSB, STOSW, STOSD  
AL/AX/EAX의 값을 EDI가 가리키는 메모리에 저장한다.  

## LODSB, LODSW, LODSD  
ESI가 가리키는 메모리에서 AL/AX/EAX로 바이트 / 워드 / 더블워드를 각각 적재한다.  

## 2차원 배열  
행 우선 순서, 열 우선 순서가 있다.  
우리가 평소 접하고, 고급언어에서 쓰던 방식은 행 우선 순서를 가진 배열이다.  
<img width="555" height="333" alt="image" src="https://github.com/user-attachments/assets/eb72057e-a1a6-44fd-ba14-3883715438fa" />  

2차원 배열에 접근하기 위해 사용하는 주소 연산 방법은 다양하다.  

베이스-인덱스 피연산자  
`[base + index]`  

베이스-인덱스-변위 피연산자  
`[base + index + displacement]`  
혹은  
`displacement[base + index]`  

```asm
tableW WORD  10h,  20h,  30h,  40h,  50h
RowSizeW = ($ - tableW)
       WORD  60h,  70h,  80h,  90h, 0A0h
       WORD 0B0h, 0C0h, 0D0h, 0E0h, 0F0h
```
와 같은 식으로 2차원 배열을 정의했다고 했을 때,  
RowSizeW는 tableW[i][j]의 'i'에 접근하기 위한, 즉 행 인덱스에 접근하기 위한 값이다.  
`현재주소 - 배열의 시작 주소 = 한 행의 크기`  
열은 `j * TYPE tableW` 해주면 된다.  

tableW[1][2]에 접근하려면  
```asm
[ebx + esi*TYPE tableW]
```
와 같이 해주면 된다.  
ebx = 배열의 행 시작 주소  
esi = 열 인덱스  


