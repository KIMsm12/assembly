# Short Answer 문제 풀이  
## 문제 1  
**Q : Which Direction flag setting causes index registers to move backward through memory when executing string primitives?**  
**A :** 1  

---
## 문제 2  
**Q : When a repeat prefix is used with STOSW, what value is added to or subtracted from the index register?**  
**A :** 2  

---
## 문제 3  
**Q : In what way is the CMPS instruction ambiguous?**  
**A :** rep 접두사와 함께 사용할 때, CMPS 명령어는 두 값이 어떻든 정해진 횟수만큼 비교하면서 지나간다.  

---
## 문제 4  
**Q : When the Direction flag is clear and SCASB has found a matching character, where does EDI point?**  
**A :** AL과 같은 값이 저장된 주소에서 +1 된 곳을 가리킨다.  

---
## 문제 5  
**Q : When scanning an array for the first occurrence of a particular character, which repeat prefix would be best?**  
**A :** REPNE 또는 REPNZ  

---
## 문제 6  
**Q : What Direction flag setting is used in the Str_trim procedure from Section 9.3?**  
**A :** 뒤에서부터 비교하기 위해 1로 세팅된다.  

---
## 문제 7  
**Q : Why does the Str_trim procedure from Section 9.3 use the JNE instruction?**  
**A :** 받은 문자와 다른 부분을 찾았을 때, 그 다음 주소에 널 스트링을 넣어 문자열을 매듭짓기 위함이다.  

---
## 문제 8  
**Q : What happens in the Str_ucase procedure from Section 9.3 if the target string contains a digit?**  
**A :** 'a' ~ 'z' 범위 내에 있는 경우에만 대문자로 변환한다. 숫자는 'a'보다 작으므로 그냥 아무 일없이 점프한다.  

---
## 문제 9  
**Q : If the Str_length procedure from Section 9.3 used SCASB, which repeat prefix would be most appropriate?**  
**A :** REPNZ 또는 REPNE  

---
## 문제 10  
**Q : If the Str_length procedure from Section 9.3 used SCASB, how would it calculate and return the string length?**  
**A :** 길이는 (EDI - pString) - 1 이다.  

---
## 문제 11  
**Q :What is the maximum number of comparisons needed by the binary search algorithm when an array contains 1,024 elements?**  
**A :** 10회  

---
## 문제 12  
**Q : In the FillArray procedure from the Binary Search example in Section 9.5, why must the Direction flag be cleared by the CLD instruction?**  
**A :** 낮은 주소부터 차례대로 값을 채우기 위해  

---
## 문제 13  
**Q : In the BinarySearch procedure from Section 9.5, why could the statement at label L2 be removed without affecting the outcome?**  
**A :** 이미 edx와 edi는 비교되었다.  

---
## 문제 14  
**Q : In the BinarySearch procedure from Section 9.5, how might the statement at label L4 be eliminated?**  
**A :** L4를 지우고 다른 점프문을 `jmp L1`으로 바꾼다.  

---
