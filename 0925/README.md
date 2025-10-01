# 어셈블리어 기초 (1~21페이지 요약)

## 1. 어셈블리어 개요
- 어셈블리어는 기계어와 1:1로 대응되는 저급 언어로, CPU 명령어 집합(ISA)에 따라 달라진다.
- 사람이 이해할 수 있는 **기호(mnemonic)**를 사용해 작성하고, 어셈블러가 이를 기계어로 번역한다.

## 2. 기본 구성 요소
- **라벨(Label)**: 코드나 데이터의 위치를 표시하는 식별자.
- **명령어(Mnemonic)**: CPU가 실행할 동작을 나타내는 단어 (예: `MOV`, `ADD`).
- **피연산자(Operand)**: 명령어의 입력/출력 값.
  - 없음, 1개, 2개, 3개의 피연산자 가능.

## 3. 주석(Comment)
- `;` → 한 줄 주석.
- `COMMENT` 지시어 → 블록 주석.

## 4. 어셈블리 프로그램 구조
- **.DATA**: 변수/상수 등 데이터 정의.
- **.CODE**: 실행 코드 정의.
- **.STACK**: 스택 크기 정의.

## 5. 기본 지시어
- **DB, DW, DD**: 각각 1바이트, 2바이트, 4바이트 데이터 정의.
- **?**: 초기화되지 않은 데이터 공간 확보.
- **DUP**: 같은 데이터 여러 개를 반복 정의.

## 6. 심볼릭 상수
- `=` : 이름과 정수 표현식 연결.
- `EQU` : 이름과 값/텍스트 연결.
- `TEXTEQU` : 텍스트 매크로 정의.

## 7. 코딩 환경 구축 (Visual Studio + Irvine 라이브러리)
1. **Irvine 라이브러리 설치**  
   - `C:\Irvine` 경로에 설치.
2. **Visual Studio 설정**
   - 프로젝트 속성 → 링커 → 추가 라이브러리 디렉토리: `C:\Irvine`
   - MASM → General → Include Paths: `C:\Irvine`
3. **빌드 및 실행**
   - 소스코드 작성 후 `F5`로 디버깅 시작.
   - `breakpoint` 활용 가능.
4. **명령어로 직접 실행**
   ```bash
   ml /c /coff AddTwo.asm
   link AddTwo.obj /SUBSYSTEM:CONSOLE /OUT:Program.exe
   ```

## 8. 예제: AddTwo 프로그램
```asm
.386
.model flat,stdcall
.stack 4096
ExitProcess proto,dwExitCode:dword

.code
main proc
    mov eax,5
    add eax,6
    invoke ExitProcess,0
main endp
end main
```

## 9. 요약
- 어셈블리어는 **라벨, 명령어, 피연산자**로 구성된다.
- 데이터 정의와 코드 실행 영역이 명확히 구분된다.
- Visual Studio에서 **Irvine 라이브러리 설정** 후 실습 가능하다.
- 프로그램 실행은 **F5 (디버깅)** 또는 **명령어 빌드**로 가능하다.

