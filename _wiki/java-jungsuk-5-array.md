---
layout  : wiki
title   : 
summary : 
date    : 2022-06-18 17:41:23 +0900
updated : 2022-06-18 17:41:23 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

# 배열(array)
## 배열의 선언과 생성

```java
int[] score;
int score[];
```

- 배열의 길이는 int범위의 양의 정수(0도 포함)이어야 한다.
```java
int[] arr = new int[0]; // 가능
```

- 배열은 한번 생성하면 길이를 변경할 수 없기 때문에 '배열이름.length'는 상수다.
```java
int[] arr = new int[5];
arr.length = 10;  // 에러.
```

## 배열의 초기화
```java
int[] score = new int[]{50, 60, 70};
int[] score = {50, 60, 70}; // new int[] 생략 가능
```

```java
int[] score;
score = new int[]{50, 60}; // OK
```

```java
int[] score = new int[]{};  // 길이가 0인 배열
int[] score = {};  // 길이가 0인 배열
```

### 배열의 출력
- 배열이 참조형이니 주소가 출력될 것 같지만 타입@주소를 출력
- 타입@주소 : I는 1차원 int, @뒤의 16진수는 배열의 주소
```java
int[] iArr = { 100, 95 };
System.out.println(Arrays.toString(iArr))
// I@12318bb 출력
```

## 배열의 복사
### System.arraycopy()를 이용한 배열의 복사
- 지정된 범위의 값들을 통째로 복사. 연속적으로 저장되는 특성을 이용.
- num[0]에서 newNum[0]으로 num.length개의 데이터를 복사

```java
System.arraycopy(num, 0, nuwNum, 0, num.length);
```

```java
char[] result = new char[abc.length + num.length];
System.arraycopy(abc, 0, result, 0, abc.length);
System.arraycopy(num, 0, result, abc.length, num.length);
```

# String배열
## String배열의 선언과 생성
- 배열은 참조형 변수이므로 초기화 시 기본값은 null이다.
- 값을 할당하면 배열에 실제 객체가 아닌 주소(4바이트 정수)가 저장된다.

## String배열의 초기화
- String 타입의 변수와 char 배열은 문자를 연이어 늘어놓은 문자배열로 같은 의미다.
- String 클래스를 이용하는 이유는 char 배열에서 기능을 추가 확장했기 때문이다.
- String 클래스와 char 배열의 차이는 String 객체는 읽기만 가능하고 변경은 불가능한 점이다.
	- String 클래스 문자열 변경 시 새로운 문자열 생성, 변경 가능한 문자열을 다루려면 StringBuffer 클래스 사용

## 2차원 배열의 초기화
- 2차원배열에서 향상된 for문을 사용하려면 score의 각 요소가 1차원 배열이므로 아래와 같이 쓴다.
- 향상된 for문은 배열의 각 요소 읽기 가능 변경 불가능
```java
int[][] score = {
	 {100, 100}
	,{20, 20}
}

for (int[] tmp : score)
	for (int i : tmp)		
```

## 가변 배열
- 각 행마다 다른 길이의 배열 생성
```java
int[][] score = new int[3][];
score[0] = new int[5];
score[1] = new int[2];
score[2] = new int[4];
```
 
## Source

- [자바의 정석](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=76083001)

