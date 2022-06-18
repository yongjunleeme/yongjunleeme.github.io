---
layout  : wiki
title   : 
summary : 
date    : 2022-06-18 17:39:17 +0900
updated : 2022-06-18 17:39:17 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

# 1번째 퍼즐 - 홀수 확인  

```java
public static boolean isOdd(int i) {
	return i % 2 == 1;
}
```

이 코드는 매개변수로 음수가 들어오면 false를 리턴하므로 25% 확률로 잘못된 값이 나옵니다.

자바에서는 정수를 나눌 때 버림을 수행합니다 .따라서 나머지 연산의 결과는 0이 아닌 값을 반환할 때는 항상 **왼쪽** 피연산자의 부호와 같습니다.

```java
3 % 2   // 1
3 % -2  // 1
-3 % 2  // -1
-3 % -2 // -1
```

해결책1

```java
public static boolean goodIsOdd1(int i) {  
    return i % 2 != 0;  
}
```

해결책2: 해결책2보다 훨씬 빠르게 작동합니다.

```java
public static boolean goodIsOdd2(int i) {  
    // & -> and 비트 연산자: 피연산자 양쪽 모두 1이어야만 1이 나온다. 이외에는 0
    return (i & 1) != 0;  
}
```

# 2번째 퍼즐 - 변화를 위한 시간  

```java
public class Change {  
    public static void main(String args[]) {  
        System.out.println(2.00 - 1.10);  
    }  
}
```

1.1을 정확히 double로 표현할 수 없어서 가장 근접한 double로 표현하므로 0.8999999999999999가 나옵니다.

이런 문제는 정확한 십진 연산을 하는 BigDecimal 클래스로 해결할 수 있습니다. 참고로 JDB(Java Database Connectivity)에서 SQL DECIMAL 자료형을 처리할 때도 BigDecimal 클래스를 활용합니다.

주의할 점은 `BigDecimal(double)` 생성자가 아닌  `BigDecimal(String)`을 사용해야 합니다. double은 매개변수로 입력한 값을 그대로 표현하기 때문입니다.


```java
System.out.println(new BigDecimal("2.00")  
        .subtract(new BigDecimal("1.10")));

```

# 3번째 퍼즐 - Long 자료형 나눗셈  

```java
public class LongDivision {
	public static void main(String[] args) {
		final long MICROS_PER_DAY = 24 * 60 * 60 * 1000 * 1000;
		final long MILLIS_PER_DAY = 24 * 60 * 60 * 1000;
	}
}
```

int 자료형들끼리 곱셉 연산이 이루어져 오버플로가 발생합니다. 첫 번째 숫자를 long 자료형으로 만들면 해결됩니다(나머지 피연산자 타깃 타이핑). 물론 양쪽 모두를 long 자료형으로 만들면 더 좋고요.


```java
public class Answer {  
    public static void main(String[] args) {  
        final long MICROS_PER_DAY = 24L * 60 * 60 * 1000 * 1000;  
        final long MILLIS_PER_DAY = 24L * 60 * 60 * 1000;  
        System.out.println(MICROS_PER_DAY / MILLIS_PER_DAY);  
    }  
}
```

# 4번째 퍼즐 - 초등학교 수준의 문제  
```java
public class Elementary {  
    public static void main(String[] args) {  
        System.out.println(12345 + 5432l);  
    }  
}
```
이 세상의 모든 것은 자세히 봐야 본 모습을 볼 수 있습니다. 오른쪽 피연산자의 마지막 글자는 l(소문자 엘)입니다.

Long 자료형을 나타낼 때는 대문자 L을 사용하세요.

변수 이름으로 l(소문자 엘)을 사용하지 마세요. C#에서는 l을 사용하면 아예 경고가 떠버립니다.

```java
public class Answer {  
    public static void main(String[] args) {  
        System.out.println(12345 + 54321);  
    }  
}
```

# 5번째 퍼즐 - 16진수의 즐거움  

```java
public class JoyOfHex {  
    public static void main(String[] args) {  
        System.out.println(  
                Long.toHexString(0x100000000L + 0xcafebabe)  
        );   
	}  
}
```

16진수, 8진수 값은 뺄셈 기호가 붙지 않아도 음수일 수 있습니다. 상위 비트가 정의되면 음수로 인식합니다. int 자료형의 `0xcafebabe`는 10진수로 -889275714입니다.

같은 값을 `signed int`로 해석할 것인지 `unsigned int`로 해석할 것인지에 따라 달라지며 부호가 있는 경우 2의 보수 표기에 따라 음수를 표현하기 때문입니다.

또한 혼합 자료형 연산이 적용됩니다. 왼쪽은 Long인데 오른쪽은 int이므로 부호확장이 일어나 Long으로 변합니다. `0xcafebabe`는 `0xffffffffcafebabeL`이 됩니다.

 `0x10000000L` + `0xffffffffcafebabeL`의 연산이 일어나고 상위 32비트만 보면 왼쪽 피연산자는 1이고 오른쪽 피연산자는 -1입니다. 따라서 두 값을 더하면 0이 됩니다.

```java
public class Answer {  
    public static void main(String[] args) {  
        System.out.println(  
                Long.toHexString(0x100000000L + 0xcafebabeL)  
        );  
    }  
}
```

# 6번째 퍼즐 - 다중 자료형 변환  

```java
public class Multicast {  
    public static void main(String[] args) {  
        System.out.println((int) (char) (byte) -1);  
    }  
}
```
먼저 32비트 int 자료형 -1을 8비트 byte 자료형으로 바꿉니다.

여기서 하위 8비트를 제외한 모든 비트를 버리는 '작은 자료형으로 변환'이 일어납니다.

다음 byte에서 char로 변환 시 byte는 부호가 있지만 char는 부호가 없어서 '기본 자료형 확장 후 축소 변환'이 발생합니다. byte 자료형이 int 자료형으로 확장 변환된 후 char 자료형으로 축소변환되는 것입니다.

자바는 2의 보수로 사칙 연산을 수행하는데 int 자료형의 -1을 2의 보수로 표현하면 32비트가 모두 1로 채워집니다(부호 확장).

그리고 char 자료형으로 바꾸면 2^16 - 1(또는 65535)이 됩니다. 

최종적으로 char이 int로 변환될 때는 0의 확장이 일어나므로 int 자료형 65535가 나옵니다.

```
-1 (8비트)
-1 (32비트)
2^16 - 1 (16비트)
2^16 - 1 (32비트)
```

(이렇게 되는 건가..? )

### 비트 마스크

부호확장을 원하지 않는다면 비트 마스크를 고려해보세요.

```
1 & 1 = 1
1 & 0 = 0
0 & 1 = 0
0 & 0 = 0


1 1 1 1(2)
는 부호 있는 숫자라면 -1이고 부호 없는 숫자라면 15입니다.

첫 번째, 세 번째 비트를 뽑고 싶으면 2진수 1010으로 & 연산을 하면 됩니다.

1111 & 1010 = 1010
```

```java
byte b = -1;
char c = (char) (b & 0xff);
```

# 7번째 퍼즐 - 변수 교환  

```java
public class CleverSwap {  
    public static void main(String[] args) {  
        int x = 1984; // (0x7c0)  
        int y = 2001; // (0x7d1)  
        x ^= y ^= x ^= y;  
        System.out.println("x = " + x + '\n' + "y = " + y);  
    }  
}
```

과거에 레지스터 메모리가 조금밖에 없었던 시절에는 `(x^y^x) == y` 공식을 활용해서 추가 변수 없이 두 변수를 교환하였습니다.

```java
// 사용금지 - 추가 변수 없이 두 변수 교환
x = x ^ y;
y = y ^ x;
x = y ^ x;
```

자바에서는 제대로 작동하지 않습니다. 자바는 피연산자를 왼쪽에서 오른쪽으로 계산합니다. `x ^= expr` 형태의 표현식을 계산할 때 x는 expr이 계산되기 전에 추출되고 expr이 계산된 이후에 합쳐집니다. 따라서 이번 프로그램에서 변수 x가 두 번 추출되지만 할당이 일어나기 전에 추출됩니다.

자바에서는 다음과 같이 연산됩니다.

```java
int tmp1 = x;     // x 획득
int tmp2 = y;     // y 획득
int tmp3 = x ^ y; // x ^ y 계산

x = tmp3;         // x ^ y를 x에 넣음
y = tmp2 ^ tmp3;  // 원래 x를 y에 저장
x = tmp 1 ^ y;    // x에 0을 저장
```

아래와 같이 작성하면 교환이 가능합니다. 하지만 사용하지 마세요. 더럽잖아요.

```java
y = (x ^= (y ^= x)) ^ y;
```   

# 8번째 퍼즐 - Dos Equis 

```java
public class DosEquis {  
    public static void main(String[] args) {  
        char x = 'X';  
        int i = 0;  
        System.out.println(true  ? x : 0);  
        System.out.println(false ? i : x);  
    }  
}
```
왜 첫 번째는 "X"를 출력하고 두 번째는 "88"을 출력할까요?

피연산자의 자료형이 다릅니다. x는 char, 0과 i는 int입니다.

1) byte, short, char 자료형을 T자료형이라고 합시다. 피연산자 중 하나가 T 자료형이고 다른 하나가 T 자료형으로 변환 가능한 int 상수라면 T 자료형으로 결과를 냅니다.

2) 만약 이것이 아니라면 '이항 숫자 확산(binary numeric promotion)'을 적용합니다.

첫 번째 표현식 `true  ? x : 0`은 1번 조건에 해당해 char 자료형으로 결과를 냅니다.

두 번째 표현식 `false ? i : x`도 숫자지만 i는 변수입니다. 2번  조건에 해당해 int 자료형으로 결과를 냅니다.

물론 변수 i를 선언할 때 final을 넣어 i를 상수로 바꾸면 "X"를 출력합니다. 하지만 복잡하니 변수 i를 int 자료형에서 char 자료형으로 변환해서 혼합 자료형 연산을 피하기 바랍니다.

# 9번째 퍼즐 - 같은 것 같으면서도 다른 것(1)  

```java
public class Tweedleum {  
  
    public static void main(String[] args) {  
        // 아래 코드를 정상 컴파일 되도록 만들어보세요.  
        // x += i;  
        // 아래코드는 컴파일되지 않게 작성하세요.  
        // x = x + i;    }  
}
```

많은 개발자가 `x += i`가 `x = x + i`를 짧게 줄인 것이라 생각하지만 그렇지 않습니다.

두 문장 모두 할당 표현식이지만 첫 번째는 복합 할당 연산자, 두 번째는 단순 할당 연산자(`=`)입니다.

복합 할당 연산자는 연산 결과를 왼쪽 변수의 자료형으로 자동 변환합니다.

```java
short x = 0;
int i = 123456;

x += i; // 자동 자료형 변환이 일어납니다.
```

실행 이후 x는 무엇일까요? int 자료형 123456은 short 자료형에서 오버플로가 발생해 -7616이 나옵니다.

```java
x = x + i;
```

반면에 위와 같이 작성하면 int를 short에 넣으므로 오류가 발생합니다.

이러한 위험을 겪지 않으려면 byte, short, char 자료형의 변수에 복합 할당 연산자를 사용하지 마세요.

또한 int 변수에 복합 할당 연산자를 사용하려면 우측에 long, float, double 표현식이 오지 않게 하세요.

# 10번째 퍼즐 - 같은 것 같으면서도 다른 것(2)  

```java
public class Tweedledee {  
  
    public static void main(String[] args) {  
        // 컴파일되도록 변수 x와 i를 선언해보세요.  
        // x = x + i;  
        // 컴파일되지 않게 작성하세요.  
        // x += i;    }  
}
```

복합할당 연산자는 `+=` 연산자의 왼쪽 피연산자가 String이라면 오른쪽 피연산자로 모든 자료형이 올 수 있습니다.

단순할당 연산자는 왼쪽 피연산자에 객체 참조가 올 때 유연하게 작동합니다. 오른쪽 표현식이 왼쪽 변수에 할당 호환된다면 원하는 대로 사용할 수 있습니다.

```java
public class Answer {  
  
    public static void main(String[] args) {  
        Object x = "buy";  
        String i = "Effective Java";  
  
        x = x + i;  
        x += i; // 책에 오류발생이라고 나왔는데 오류 안 나옴
    }  
}
```

# Source

- [자바 퍼즐러](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788968481444)

