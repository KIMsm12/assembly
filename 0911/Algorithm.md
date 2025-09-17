
# 연습문제 번역 및 풀이 

---

## 풀이 요약 및 코드

### 문제 1: 16비트 이진 문자열 → 정수 (C)

```c
#include <stdio.h>

int binary16_to_int(const char *bin) {
    int value = 0;
    for (int i = 0; bin[i] != '\0'; i++) {
        char c = bin[i];
        if (c != '0' && c != '1') return -1;
        value = value * 2 + (c - '0');
    }
    return value;
}

int main(void) {
    char s[32];
    printf("16비트 이진수 입력: ");
    if (scanf("%31s", s) != 1) return 0;
    int v = binary16_to_int(s);
    if (v < 0) printf("입력 오류\n");
    else printf("정수: %d\n", v);
    return 0;
}
```

---

### 문제 2: 32비트 16진수 문자열 → 정수 (C, 부호/비부호 선택 가능)

```c
#include <stdio.h>

long long hex32_to_ll(const char *hex) {
    long long value = 0;
    for (int i = 0; hex[i] != '\0'; i++) {
        char c = hex[i];
        int digit;
        if (c >= '0' && c <= '9') digit = c - '0';
        else if (c >= 'A' && c <= 'F') digit = c - 'A' + 10;
        else if (c >= 'a' && c <= 'f') digit = c - 'a' + 10;
        else return -1;
        value = value * 16 + digit;
    }
    return value;
}

int main(void) {
    char s[40];
    printf("32비트 16진수 입력 (최대 8자리): ");
    if (scanf("%39s", s) != 1) return 0;
    long long v = hex32_to_ll(s);
    if (v < 0) printf("입력 오류\n");
    else printf("값: %lld\n", v);
    return 0;
}
```

---

### 문제 3: 정수 → 이진 문자열 (C, 음수 포함 간단 처리)

```c
#include <stdio.h>
#include <string.h>

void int_to_binary(int n, char *out) {
    unsigned int x = (unsigned int)n;
    char tmp[65];
    int idx = 0;
    if (x == 0) { out[0] = '0'; out[1] = '\0'; return; }
    while (x > 0) {
        tmp[idx++] = (x & 1) ? '1' : '0';
        x >>= 1;
    }
    for (int i = 0; i < idx; i++) out[i] = tmp[idx-1-i];
    out[idx] = '\0';
}

int main(void) {
    int n; char out[65];
    printf("정수 입력: ");
    if (scanf("%d", &n) != 1) return 0;
    int_to_binary(n, out);
    printf("이진: %s\n", out);
    return 0;
}
```

---

### 문제 4: 정수 → 16진 문자열 (C)

```c
#include <stdio.h>

void int_to_hex(int n, char *out) {
    unsigned int x = (unsigned int)n;
    char tmp[32];
    int idx = 0;
    if (x == 0) { out[0] = '0'; out[1] = '\0'; return; }
    while (x > 0) {
        int d = x % 16;
        tmp[idx++] = (d < 10) ? ('0' + d) : ('A' + (d - 10));
        x /= 16;
    }
    for (int i = 0; i < idx; i++) out[i] = tmp[idx-1-i];
    out[idx] = '\0';
}

int main(void) {
    int n; char out[32];
    printf("정수 입력: ");
    if (scanf("%d", &n) != 1) return 0;
    int_to_hex(n, out);
    printf("16진: %s\n", out);
    return 0;
}
```

---

### 문제 5: 밑 b (2 ≤ b ≤ 10)에서 두 숫자 문자열 더하기 (C, 최대 1000자리)

```c
#include <stdio.h>
#include <string.h>

void add_base(const char *a, const char *b, int base, char *res) {
    int la = strlen(a), lb = strlen(b);
    int i = la - 1, j = lb - 1, k = 0, carry = 0;
    char tmp[1105];
    while (i >= 0 || j >= 0 || carry) {
        int da = (i >= 0) ? (a[i]-'0') : 0;
        int db = (j >= 0) ? (b[j]-'0') : 0;
        int s = da + db + carry;
        tmp[k++] = (s % base) + '0';
        carry = s / base;
        i--; j--;
    }
    for (int t = 0; t < k; t++) res[t] = tmp[k-1-t];
    res[k] = '\0';
}

int main(void) {
    char a[1100], b[1100], res[1105]; int base;
    printf("진법 b(2~10) 입력: ");
    if (scanf("%d", &base) != 1) return 0;
    printf("첫 숫자 입력: "); if (scanf("%1099s", a) != 1) return 0;
    printf("두번째 숫자 입력: "); if (scanf("%1099s", b) != 1) return 0;
    add_base(a,b,base,res);
    printf("합: %s\n", res);
    return 0;
}
```

---

### 문제 6: 16진수 문자열 덧셈 (각 최대 1000자리) (C)

```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>

int hexval(char c) {
    if (c >= '0' && c <= '9') return c - '0';
    if (c >= 'A' && c <= 'F') return c - 'A' + 10;
    if (c >= 'a' && c <= 'f') return c - 'a' + 10;
    return -1;
}
char hexchr(int v) { return (v < 10) ? ('0'+v) : ('A'+(v-10)); }

void add_hex(const char *a, const char *b, char *res) {
    int la = strlen(a), lb = strlen(b);
    int i = la-1, j = lb-1, k = 0, carry = 0;
    char tmp[1105];
    while (i>=0 || j>=0 || carry) {
        int da = (i>=0) ? hexval(a[i]) : 0;
        int db = (j>=0) ? hexval(b[j]) : 0;
        int s = da + db + carry;
        tmp[k++] = hexchr(s % 16);
        carry = s / 16;
        i--; j--;
    }
    for (int t = 0; t < k; t++) res[t] = tmp[k-1-t];
    res[k] = '\0';
}

int main(void) {
    char a[1100], b[1100], res[1105];
    printf("첫 16진수 입력: "); if (scanf("%1099s", a) != 1) return 0;
    printf("두번째 16진수 입력: "); if (scanf("%1099s", b) != 1) return 0;
    add_hex(a,b,res);
    printf("합: %s\n", res);
    return 0;
}
```

---

### 문제 7: 16진수 한 자리 × 16진수 문자열 (최대 1000자리) (C)

```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>

int hexval(char c) {
    if (c >= '0' && c <= '9') return c - '0';
    if (c >= 'A' && c <= 'F') return c - 'A' + 10;
    if (c >= 'a' && c <= 'f') return c - 'a' + 10;
    return -1;
}
char hexchr(int v) { return (v < 10) ? ('0'+v) : ('A'+(v-10)); }

void mul_hex_digit(char d, const char *s, char *res) {
    int ld = hexval(d);
    int n = strlen(s);
    int carry = 0, k = 0;
    char tmp[1105];
    for (int i = n-1; i>=0; i--) {
        int v = hexval(s[i]);
        int p = v * ld + carry;
        tmp[k++] = hexchr(p % 16);
        carry = p / 16;
    }
    while (carry) { tmp[k++] = hexchr(carry % 16); carry /= 16; }
    for (int t = 0; t < k; t++) res[t] = tmp[k-1-t];
    res[k] = '\0';
}

int main(void) {
    char d, s[1100], res[1105];
    printf("16진수 한 자리 입력: ");
    if (scanf(" %c", &d) != 1) return 0;
    printf("16진수 문자열 입력: "); if (scanf("%1099s", s) != 1) return 0;
    mul_hex_digit(d, s, res);
    printf("곱: %s\n", res);
    return 0;
}
```

---

### 문제 8: Java 코드 + (예상) `javap -c` 출력과 주석

**Java 소스**

```java
public class Calc {
    public static void main(String[] args) {
        int Y = 0; // 초기화 (예시)
        int X = (Y + 4) * 3;
        System.out.println(X);
    }
}
```

**예상 `javap -c Calc` 출력 (대략적인 주석 포함)**

```
Compiled from "Calc.java"
public class Calc {
  public Calc();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: iconst_0                       // int 0 (Y 초기값)
       1: istore_1                       // 지역변수 1에 저장 (Y)
       2: iload_1                        // Y 불러오기
       3: iconst_4                       // 상수 4
       4: iadd                           // Y + 4
       5: iconst_3                       // 상수 3
       6: imul                           // (Y+4) * 3
       7: istore_2                       // 결과를 지역변수 2에 저장 (X)
       8: getstatic     #2               // Field java/lang/System.out:Ljava/io/PrintStream;
      11: iload_2                        // X 불러오기
      12: invokevirtual #3              // Method java/io/PrintStream.println:(I)V
      15: return
```

(위 바이트코드와 주석은 javap로 실제 확인할 때와 약간 다를 수 있으나, 기본 동작 흐름은 같다.)

---

### 문제 9: 부호 없는 이진 정수 뺄셈 기법 (C)

아래 코드는 두 개의 이진 문자열(a, b)을 받아 a >= b 가정 하에 뺄셈을 수행한다. 자리별로 빌려오는(borrow) 방식을 사용한다.

```c
#include <stdio.h>
#include <string.h>

void binary_subtract(const char *a, const char *b, char *res) {
    int la = strlen(a), lb = strlen(b);
    int i = la - 1, j = lb - 1, k = 0;
    char tmp[1105];
    int borrow = 0;
    while (i >= 0) {
        int da = a[i] - '0';
        int db = (j >= 0) ? (b[j]-'0') : 0;
        int sub = da - db - borrow;
        if (sub < 0) { sub += 2; borrow = 1; } else borrow = 0;
        tmp[k++] = sub + '0';
        i--; j--;
    }
    while (k > 1 && tmp[k-1] == '0') k--;
    for (int t = 0; t < k; t++) res[t] = tmp[k-1-t];
    res[k] = '\0';
}

int main(void) {
    char a[1100], b[1100], res[1105];
    printf("빼는 수 (a, 큰 값) 입력: "); if (scanf("%1099s", a) != 1) return 0;
    printf("빼어질 수 (b, 작은 값) 입력: "); if (scanf("%1099s", b) != 1) return 0;
    binary_subtract(a,b,res);
    printf("차: %s\n", res);
    return 0;
}
```

테스트 예시

* 입력: a=10001000, b=00000101 → 출력: 10000011
* 다른 테스트 예: a=10110 (22), b=00101 (5) → 출력: 10001 (17)
* 다른 테스트 예: a=111000 (56), b=001001 (9) → 출력: 110111 (47)

---

