---
layout  : wiki
title   : 
summary : 
date    : 2022-06-18 17:39:07 +0900
updated : 2022-06-18 17:39:07 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

# 아이템 49. 매개변수가 유효한지 검사하라

자바의 null 검사 기능 사용하기, 원하는 예외 메시지도 지정할 수 있다. 입력을 그대로 반환하므로 값을 사용하는 동시에 null 검사를 수행할 수 있다.

```java
Objects.requireNonNull(strategy, "전략");
```

리스트와 배열 전용 범위 검사 기능 `checkFromIndexSize`, `checkFromToIndex`, `checkIndex`

`assert`를 사용한 매개변수 유효성 검증

```java
private static void sort(long a[], int offset, int length) {
	assert a != null;
	assert offset >= 0 && offset <= a.length;
	assert length >= 0 && length <= a.length - offset;
}
```

# 아이템 50. 적시에 방어적 복사본을 만들라  

[기간을 표현하는 클래스 - 불변식을 지키지 못했다](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter8/item50/Period.java)

`Date`(Deprecated API -> `LocalDateTime`, `ZonedDateTime`으로 대체, 설명을 위해서 사용)가 가변이라는 사실을 이용하면 어렵지 않게 불변식을 깨뜨릴 수 있다.

[Period 인스턴스 내부를 공격해보자](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter8/item50/Attacks.java)

외부 공격으로부터 Period 인스턴스의 내부를 보호하려면 생성자에서 받은 가변 매개변수 각각을 방어적으로 복사(defensive copy)해야 한다. 그런 다음 인스턴스 안에서는 원본이 아닌 복사본을 사용한다.

- 수정한 생성자 - 매개변수의 방어적 복사본을 만든다.
```java
public Period(Date start, Date end) {
	this.start = new Date(start.getTime());
	this.end = new Date(end.getTime());

	if (start.compareTo(end) > 0)
		throw new IllegalArgumentException(
		start + " after " + end);
}
```
매개변수의 유효성을 검사하기 전에 방어적 복사본을 만들고 이 복사본으로 유효성을 검사한 점에 주목하자. 멀티스레딩 환경이라면 원본 객체의 유효성을 검사한 후 복사본을 만드는 그 찰나의 취약한 순간에 다른 스레드가 원복 객체를 수정할 위험이 있기 때문이다.

- 수정한 접근자 - 필드의 방어적 복사본을 반환한다.

```java
public Date start() {
	return new Date(start.getTime());
}

public Date end() {
	return new Date(end.getTime());
}
```
클래스가 클라이언트로부터 받는 혹은 클라이언트로 반환하는 구성요소가 가변이라면 그 요소는 반드시 방어적으로 복사해야 한다. 복사 비용이 너무 크거나 클라이언트가 그 요소를 잘못 수정할 일이 없음을 신뢰한다면 방어적 복사를 수행하는 대신 해당 구성요소를 수정했을 때의 책임이 클라이언트에 있음을 문서에 명시하도록 하자.

# 아이템 51. 메서드 시그니처를 신중히 설계하라  

- 메서드 이름을 신중히 짓자.
- 편의 메서드를 너무 많이 만들지 말자.
- 매개변수 목록은 짧게 유지하자.
	- 여러 메서드로 쪼갠다. 직교성(orthogonality)을 높여 오히려 메서드 수를 줄여주는 효과도 있다(직교 설명 책 참조)
	- 매개변수를 여러 개로 묶어주는 도우미 클래스 생성
	- 빌더 패턴을 응용
- 매개변수의 타입으로는 클래스보다 인터페이스가 더 낫다.

(각 설명의 이유는 책을 참조, 나중에 몇 번이고 다시 봐야 할 것 같음)

# 아이템 52. 다중정의는 신중히 사용하라  

[컬렉션 분류기 - 오류! 이 프로그램은 무엇을 출력할까?](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter8/item52/CollectionClassifier.java)

"집합", "리스트", "그 외"를 차례로 출력할 것 같지만, "그 외"만 세 번 연달아 출력한다. 다중정의(overloading, 오버로딩)된 세 classify 중 어느 메서드를 호출할지가 컴파일타임에 정해지기 때문이다.

다중정의한 메서드는 정적으로 선택되고 재정의한 메서드는 동적으로 선택된다.

메서드를 재정의했다면 해당 객체의 런타임 타입이 어떤 메서드를 호출할지의 기준이 된다. 메서드 재정의란 상위 클래스가 정의한 것과 똑같은 시그니처의 메서드를 하위 클래스에서 다시 정의한 것을 말한다.

위의 다중정의 문제에서 (정적 메서드를 사용해도 좋다면) 모든 메서드를 하나로 합친 후 `instanceof`로 명시적으로 검사하면 말끔히 해결된다.

[classify](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter8/item52/FixedCollectionClassifier.java)

안전하고 보수적으로 가려면 매개변수 수가 같은 다중정의는 만들지 말자.

생성자가 같은 수의 매개변수를 받아야 할 때는? 매개변수 수가 같은 다중정의 메서드가 많더라도 매개변수 하나 이상이 근본적으로 다르다(두 타입의 값을 서로 어느 쪽으로든 형변환할수 없다는 뜻)면 헷갈릴 일이 없다. 이를테면  `ArrayList`에서 int를 받는 생성자와 Collection을 받는 생성자가 있는데 어떤 상황에서든 헷갈릴 일이 없을 것이다.

 메서드를 다중정의할 때 서로 다른 함수형 인터페이스라도 같은 위치의 인수로 받아서는 안 된다.

# 아이템 53. 가변인수는 신중히 사용하라  

인수가 1개 이상이어야 할 때 훨씬 나은 방법은 첫 번째로는 평범한 매개변수를 받고, 가변인수는 두 번째로 받는 것이다.

[인수가 1개 이상이어야 할 때 가변인수를 제대로 사용하는 방법](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter8/item53/Varargs.java)

가변인수는 인수 개수가 정해지지 않았을 때 아주 유용하다.

그러나 성능에 민감한 상황이라면 가변인수 메서드는 호출될 때마다 배열을 새로 하나 할당해 초기화하므로 주의가 필요하다. 

예를 들어 메서드 호출의 95%가 인수를 3개 이하로 사용한다면 아래와 같이 다중정의하여 마지막 다중정의 메서드가 인수 4개 이상인 5% 호출을 담당한다.

따라서 메서드 호출 중 단 5%만 배열을 생성한다.

```java
public void foo() {}
public void foo(int a1) {}
public void foo(int a1, int a2) {}
public void foo(int a1, int a2, int a3) {}
public void foo(int a1, int a2, int a3, int... rest) {}
```
  
# 아이템 54. null이 아닌, 빈 컬렉션이나 배열을 반환하라  

null을 반환하면 클라이언트는 null 상황을 처리하는 코드를 추가로 작성해야 한다.

- 빈 컬렉션을 반환하는 올바른 예
```java
public static List<Cheese> getCheeses() {  
    return new ArrayList<>(cheesesInStock);  
}
```

빈 컬렉션 할당이 성능을 떨어뜨릴 수도 있다. 그러면 매번 똑같은 빈 '불변' 컬렉션을 반환할 수 있다. `Collections.emptyList`가 그러한 예다. 집합이 필요하면 `Collections.emptySet`, 맵이 필요하면 `Collections.emptyMap`을 사용하자.

- 최적화 - 빈 컬렉션을 매번 새로 할당하지 않도록 했다.
```java
public static List<Cheese> getCheeses2() {  
    return cheesesInStock.isEmpty() ? Collections.emptyList()  
            : new ArrayList<>(cheesesInStock);  
}
```

배열을 쓸 때도 마찬가지로 null이 아니라길이가 0인 배열을 반환하자.

```java
public static Cheese[] getCheeses3() {  
    return cheesesInStock.toArray(new Cheese[0]);  
}
```
- 최적화 - 빈 배열을 매번 새로 할당하지 않도록 했다.
```java
public static Cheese[] getCheeses4() {  
    return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);  
}
```  

# 아이템 55. 옵셔널 반환은 신중히 하라  

`Optional<T>`는 null이 아닌 T타입 참조를 하나 담거나, 혹은 아무것도 담지 않을 수 있다.
옵셔널은 원소를 최대 1개 가질 수 있는 '불변' 컬렉션이다.

```java
public static <E extends Comparable<E>> Optioanl<E> max(Collection<E> c) 
```

빈 옵셔널은 `Optional.emtpy()`로 만들고 값이 든 옵셔널은 `Optional.of(value)`로 생성한다. null값도 허용하는 옵셔널을 만들려면 `Optional.ofNullable(value)`를 사용하면 된다. 옵셔널을 반환하는 메서드에서는 절대 null을 반환하지 말자.

```java

public class Max {  
    public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {  
        if (c.isEmpty()) {  
            return Optional.empty();  
        }  
  
        E result = null;  
        for (E e : c)  
            if (result == null || e.compareTo(result) > 0)  
                result = Objects.requireNonNull(e);  
  
        return Optional.of(result);  
    }  
}
```    

스트림 버전으로 다시 작성한다면 Stream의 max 연산이 옵셔널을 생성해줄 것이다(비교자를 명시적으로 전달해야 하지만)

```java
public static <E extends Comparable<E>>  
Optional<E> max(Collection<E> c) {  
    return c.stream().max(Comparator.naturalOrder());  
}
```

옵셔널 활용 1 - 기본값을 정해둘 수 있다.

```java
String lastWordInLexicon = max(list).orElse("단어 없음..");
```

옵셔널 활용 2 - 원하는 예외를 던질 수 있다.

```java
max(list).orElseThrow(TemperTantrumException::new);
```

옵셔널 활용 3 - 항상 값이 채워져 있다고 가정한다.

```java
max(list).get();
```


`isPresent`는 옵셔널이 채워져 있으면 true, 비어 있으면 false를 반환한다. 그러나 앞서 언급한 활용방법으로 대체 가능하므로 신중히 사용해야 한다.

```java
// Inappropriate use of isPresent  
Optional<ProcessHandle> parentProcess = ph.parent();  
System.out.println("Parent PID: " + (parentProcess.isPresent() ?  
        String.valueOf(parentProcess.get().pid()) : "N/A"));
```

map을 사용하여 다음처럼 다듬을 수 있다.

```java
System.out.println("Parent PID: " + ph.parent().map(h -> String.valueOf(h.pid())).orElse("N/A"));
```

스트림을 사용한다면 옵셔널들을 `Stream<Optional<T>>`로 받아서, 그중 채워진 옵셔널들에서 값을 뽑아 `Stream<T>`에 담아 처리할 수 있다.

```java
StreamOfOptionals
	.filter(Optional::isPresent)
	.map(Optional::get)
```

자바 9에서는 Optional에 `stream()` 메서드가 추가되었는데 이는 Optional을 stream으로 변환해주는 어댑터다. 값이 있으면 원소를 담은 스트림으로, 없다면 빈 스트림으로 변환한다.

```java
streamOfOptionals.flatMap(Optional::stream)
```

반환값으로 컬렉션, 스트림, 배열, 옵셔널 같은 컨테이너 타입은 옵셔널로 감싸면 안 된다. 빈 `Optional<List<T>>`를 반환하기보다는 빈 `List<T>`를 반환하는 게 좋다(아이템 54). 빈 컨테이너를 그대로 반환하면 클라이언트에 옵셔널 처리 코드를 넣지 않아도 된다.

결과가 없을 수 있으며, 클라이언트가 이 상황을 특별히 처리해야 한다면 `Optional<t>`를 반환한다. 그러데 박싱된 기본타입을 담는 옵셔널은 무거울 수밖에 없다.

그럴 땐 `OptionalInt`, `OptionalLong`, `OptionalDouble`이용하자.

# 아이템 56. 공개된 API 요소에는 항상 문서화 주석을 작성하라  

- [How to Write Doc Comments for the Javadoc Tool](https://www.oracle.com/technical-resources/articles/java/javadoc-tool.html)

- [Doc Example](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter8/item56/DocExamples.java)


### HTML 태그
문서화 주석에 HTML 태그를 사용한다.

`@throws` 절에 사용한 `{@code}`태그는 첫째 태그로 감싼 내용을 폰트로 렌더링한다. 둘째 태그로 감싼 내용에 포함된 HTML 요소나 다른 자바독 태그를 무시한다.

문서화 주석에 여러 줄로 된 코드 예시를 넣으려면 `{@code}` 태그를 다시 `<pre>` 태그로 감싸면 된다. `<pre>{@code ... 코드 ... }</pre>`

`this list` 를 살펴보자면 관례상 "this"는 호출된 메서드가 자리하는 객체를 가리킨다.

API 설명에ㅔ `<, >, &` 등의 HTML 메타문자를 포함시키려면 `{@literal}` 태그로 감싸면 된다. 

### `@impleSpec`

클래스를 상속용으로 설계할 때는 자기사용 패턴(self-use pattern)을 `@implSpec`으로 문서화한다. 

일반 문서화 주석은 해당 메서드와 클라이언트 사이 계약을 설명하지만 `@ImpleSpec` 주석은 해당 메서드와 하위 클래스 사이의 계약을 설명한다. 하위 클래스들이 그 메서드를 상속하거나 super 키워드를 이용해 호출할 때 그 메서드가 어떻게 동작하는지를 명확히 인지하고 사용하도록 해줘야 한다.

자바 11까지도 자바독 명령줄에서 `-tag "impleSpec:a:Implementation Requirements:"`스위치를 켜주지 않으면 `@implSpec` 태그를 무시한다.

### 요약 설명

각 문서화 주석의 첫 번째 문장은 요약 설명으로 간주된다. 요약 설명에서는 마침표에 주의해야 한다. 만약 "머스터드 대령이나 Mrs. 피콕 같은 용의자."가 나온다면 "머스터드 대령이나 Mrs."까지만 요약 설명이 된다.

판단 기준은 마침표, 공백 (스페이스, 탭, 줄바꿈) , 다음 문장 시작(소문자가 아닌 문자)이다. 해결책은 의도치 않은 마침표를 포함한 텍스트를 `{@literal}`로 감싸주는 것이다.

자바 10부터는 `{@summary}` 라는 요약 설명 전용 태그가 추가됐다.

요약설명은 완전한 문장이 되는 경우가 드물고 (주어가 없는) 동사구여야 한다.

2인칭 문장(return the number)가 아닌 3인칭 문장(returns the number)로 써야 한다.

클래스, 인터페이스, 필드의 요약 설명은 명사절이어야 한다.

### `@index`

`@index` 태그를 사용하면 자바독이 생성한 HTML 문서에서 검색(색인)에 중요한 용어를 색인화할 수 있다.

### `@inheritDoc`

상위 타입 문서화 주석 일부를 상속할 수 있다. 클래스는 자신이 구현한 인터페이스 문서화 주석을 (복사해 붙여넣지 않고) 재사용할 수 있다는 뜻이다.

### ETC

패키지를 설명하는 문서화 주석은 `package-info.java` 파일에 작성한다. 모듈 관련 설명은 `module-info.java` 파일에 작성하면 된다.

클래스 혹은 정적 메서드가 스레드 안전하든 아니든 스레드 안전 수준을 반드시 API 설명에 포함해야 한다. 직렬화할 수 있는 클래스라면 직렬화 형태도 API 설명에 기술해야 한다.
 
# Source

- [이펙티브 자바 Effective Java 3/E](http://www.yes24.com/Product/Goods/65551284)

