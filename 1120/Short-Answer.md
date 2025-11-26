# Short Answer 문제 풀이  
## 문제 1  
**Q : Which statements belong in a procedure’s epilogue when the procedure has stack parameters and local variables?**  
**A :**
```asm
mov esp, ebp  ; 지역 변수 클리어 
pop ebp       ; ebp 제거        > 이 2줄 대신 leave 도 가능 
ret (n*4)     ; 받았던 인수들 클리어  
```

---
## 문제 2  
**Q : When a C function returns a 32-bit integer, where is the return value stored?**  
**A :** 대부분 묵시적으로 EAX에 저장된다.  

---
## 문제 3  
**Q : How does a program using the STDCALL calling convention clean up the stack after a procedure call?**  
**A :** STDCALL 호출 규약을 사용할 때에는 `ret (n*4)`와 같이 스택을 클리어한다.  

---
## 문제 4  
**Q : How is the LEA instruction more powerful than the OFFSET operator?**  
**A :** LEA 명령어는 단순히 주소를 가져오는 OFFSET과는 달리, 실행 중에 복잡한 주소 계산을 수행할 수 있다.    
```asm
LEA eax, [ebx + ecx*2]
```

---
## 문제 5  
**Q : In the C++ example shown in Section 8.2.3, how much stack space is used by a variable of type int?**  
**A :** int 타입 변수 하나에는 4바이트의 스택 공간을 할당한다.  

---
## 문제 6  
**Q : What advantages might the C calling convention have over the STDCALL calling convention?**  
**A :** C 호출 규약은 호출자가 스택을 정리하기 때문에 가변 인자 함수를 지원할 수 있다는 큰 장점이 있다. 또한 C에서 기본적으로 사용되는 규약이기 때문에 이식성과 호환성이 높고 디버깅이 용이하다.  

---
## 문제 7  
**Q : (True/False): When using the PROC directive, all parameters must be listed on the same line.**  
**A :** False : 여러 줄에 나열해도 상관 없다.  

---
## 문제 8  
**Q : (True/False): If you pass a variable containing the offset of an array of bytes to a procedure that expects a pointer to an array of words, the assembler will flag this as an error.**  
**A :** False : 어셈블러는 타입 체크를 하지 않는다. 데이터 타입 불일치에 따른 문제(오류)는 런타임 시간에 발생한다.  

---
## 문제 9  
**Q : (True/False): If you pass an immediate value to a procedure that expects a reference parameter, you can generate a general-protection fault.**  
**A :** True : 참조 매개변수는 메모리 주소를 전달하도록 설계되어 있다. 즉시 값을 참조로 전달하게 되면 잘못된 메모리 영역에 접근하여 런타임 오류가 발생할 수 있다.  

---
