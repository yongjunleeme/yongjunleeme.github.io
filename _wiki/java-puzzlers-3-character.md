---
layout  : wiki
title   : 
summary : 
date    : 2022-06-18 17:39:33 +0900
updated : 2022-06-18 17:42:12 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

# 11번째 퍼즐 - 최후의 웃음

```java
public class LastLaugh {  
    public static void main(String[] args) {  
        System.out.print("H" + "a");  
        System.out.print('H' + 'a');  
    }  
}
```

1번째 `"H" + "a"`는 `+` 연산자가 문자열 연결 연산자로 사용됩니다.

2번째 `'H' + 'a'`는 char 자료형이라 `+` 연산자가 일반 덧셈 연산자로 사용됩니다.

자바 컴파일러에는 기본 자료형 확장 변환이 있어서 char 자료형을 피연산자로 `+` 연산을 할 때는 int 자료형으로 바꾸어 계산합니다. 'H'는 72이고 'a'는 97이므로 169가 됩니다.

char 자료형은 Strig과 비슷해 보이지만 사실 컴퓨터 내부에서는 16비트 정수로 String보다 int에 가깝습니다.

라이브러리를 사용해 char을 연결할 수 있습니다. 

```java
StringBuffer sb = new StringBuffer();  
sb.append('H');  
sb.append('a');  
System.out.println(sb);


System.out.println(String.valueOf('H') + 'a');
```

하지만 `StringBuffer`를 이렇게 쓰면 좋지 않습니다. `String.valueOf()` 메서드로 첫 번째 숫자를 문자열로  변경해도 됩니다. 그리고 아래와 같이 연결하기도 합니다.

```java
System.out.println("" + 'H' + 'a');
```

# 12번째 퍼즐 - ABC  

```java
public class Abc {  
  
    public static void main(String[] args) {  
        String letters = "ABC";  
        char[] numbers = {'1', '2', '3'};  
        System.out.println(letters + " easy as " + numbers);  
    }  
}
```

문자열 연결 연산자는 char을 라이브러리의 메서드처럼(println() : char을 숫자가 아니라 문자로 취급해 유니코드 문자를 출력) 다루지 않습니다. 문자열 연결 연산자는 피연산자를 문자열로 변환하고 양쪽의 문자열을 결합합니다.

배열을 포함한 모든 객체 참조는 다음과 같이 처리됩니다.

참조가 null이면 "null"로 변환하고 null이 아니라면 `toString()`을 호출해 문자열로 변환됩니다. 하지만 `toString()` 리턴값이 null이라면 문자열 "null"로 변환됩니다.

배열의 `toString()` 메서드는 Object 클래스에서 상속받은 것입니다. Object의 `toString()` 메서드는 `"클래스 이름@"`과 `[16진수로 이루어진 객체 해시]`를 합친 문자열을 리턴합니다.

코드를 수정하려면 다음과 같이 char 배열을 문자열로 바꿔줄 수 있습니다.

```java
System.out.println(letters + " easy as " + String.valueOf(numbers));
```

메서드를 나누어 사용할 수 있습니다. 그러면 `println()` 메서드의 `println(char[])` 오버로딩 형태가 사용됩니다. 하지만 `char[]` 자료형이 아닌 다른 자료형을 사용한다면 다른 자료형의 오버로딩 메서드를 사용하므로 문제가 발생할 수 있습니다.

```java
System.out.print(letters + " easy as ");  
System.out.println(numbers);
```

# Source

- [자바 퍼즐러](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788968481444)

