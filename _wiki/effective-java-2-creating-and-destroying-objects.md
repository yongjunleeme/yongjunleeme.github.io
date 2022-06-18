---
layout  : wiki
title   : 
summary : 
date    : 2022-06-18 17:38:03 +0900
updated : 2022-06-18 17:38:03 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

#  아이템 1. 생성자 대신 정적 팩터리 메서드를 고려하라  

정적팩터리 메서드
- 생성자와 별도로 객체를 생성하는 메서드

장점
- 1. 이름을 가질 수 있다.
- 2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.
	- 인스턴스 통제 클래스
		- 싱글턴 가능
		- 인스턴스화 불가 가능
		- 동치인 인스턴스가 단 하나임을 보장 가능
- 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
	- 굳이 클래스를 만들지 않고 인터페이스에서 정적 팩토리 메서드를 얻을 수 있다.
- 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
- 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다. 

- [예제 참고](https://coder-in-war.tistory.com/entry/Java-28-%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C)

# 아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라  

## [Java Beans](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter2/item2/javabeans/NutritionFacts.java)

## [Builder](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter2/item2/builder/NutritionFacts.java)

 ## [hierarchicalbuilder](https://github.com/jbloch/effective-java-3e-source-code/tree/master/src/effectivejava/chapter2/item2/hierarchicalbuilder "hierarchicalbuilder")

- 빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기 좋다.
- 하위 클래스의 메서드가 상위 클래스의 메서드가 정의한 반환 타입이 아닌, 그 하위 타입을 환하는 기능을 공변반환 타이핑(covariant return typing)이라 한다. 이 기능을 이용해 클라이언트가 형변환에 신경 쓰지 않고도 빌더를 사용할 수 있다.
- 생성자로는 누릴 수 없는 사소한 이점으로, 빌더를 이용하면 가변인수(vararags) 매개변수를 여러 개 사용할 수 있다.
- 단점
	- 성능 비용이 크지는 않지만 민감한 상황에서는 문제
	- 코드가 장황해서 매개변수 4개 이상은 되어야 값어치를 한다. 하지만 API는 시간이 지날수록 매개변수가 많아지므로 애초에 빌더로 시작하는 편이 나을 때가 많다.

핵심 정리

생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는 게 더 낫다. 매개변수 중 다수가 필수가 아니거나 같은 타입이면 특히 더 그렇다. 빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 훨씬 안전하다.
 
# 아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라
- [field 방식](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter2/item3/field/Elvis.java)
- [정적 팩터리 방식](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter2/item3/staticfactory/Elvis.java)
- [열거 타입 방식 - 바람직한 방법](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter2/item3/enumtype/Elvis.java)
	- 단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다(열거 타입이 다른 인터페이스를 구현하도록 선언할 수는 있다.)

# 아이템 4. 인스턴스화를 막으려거든 private 생성자를 사용하라 

[인스턴스를 만들 수 없는 유틸리티 클래스](https://github.com/jbloch/effective-java-3e-source-code/tree/master/src/effectivejava/chapter2/item4)

정적 유틸리티 클래스(예: 단순 정적 메서드와 정적 필드만을 담은 클래스 등)의 인스턴스화를 막으려면?
- 컴파일러가 기본 생성자를 만드는 경우는 오직 명시된 생성자가 없을 때 뿐이니 private 생성자를 추가하면 클래스의 인스턴스화를 막을 수 있다.

# 아이템 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

클래스가 내부적으로 하나 이상의 자원에 의존하고 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다. 이 자원들을 클래스가 직접 만들게 해서도 안 된다. 대신 필요한 자원(혹은 그 자원을 만들어주는 팩터리를)을 생성자에 (혹은 정적 팩터리나 빌더에) 넘겨주자. 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해준다.

```java
public class Spellchecker {
	private final Lexicon dictionary;
	public SpellChecker(Lexicon dictionary) {  // 의존 객체인 사전을 주입
		this.dictionary = Objects.requireNonNull(dictionary);
	}
}
```

이 패턴의 변형으로 생성자에 자원 팩터리를 넘겨주는 방식이 있다. 자바 8에서 소개한 `Supplier<T>` 인터페이스가 팩터리를 표현한 완벽한 예다. `Supplier<T>`를 입력으로 받는 메서드는 일반적으로 한정적 와일드카드 타입을 사용해 팩터리의 타입 매개변수를 제한해야 한다. 이 방식을 사용해 클라이언트는 자신이 명시한 타입의 하위 타입이라면 무엇이든 생성할 수 있는 팩터리를 넘길 수 있다. 예컨대 다음 코드는 클라이언트가 제공한 팩터리가 생성한 타일들로 구성된 모자이크를 만드는 메서드다.

```java
Mosaic create(Supplier<? extends Tile> tileFactory) {...}
```
# 아이템 6. 불필요한 객체 생성을 피하라  

정규표현식용 Pattern 인스턴스는 한 번 쓰고 버려져서 곧바로 가비지 컬렉션의 대상이 된다. Pattern을 입력받은 정규표현식에 해당하는 유한 상태 머신을 만들기 때문에 인스턴스 생성 비용이 높다.

성능을 개선하려면 필요한 정규표현식을 표현하는 (불변인) Pattern 인스턴스를 클래스 초기화(정적 초기화) 과정에서 직접 캐싱해두고, 나중에 isRomanNumeral 메서드가 호출될 때마다 이 인스턴스를 재사용한다.

[값비싼 개체를 재사용해 성능을 개선](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter2/item6/RomanNumerals.java)

불필요한 객체를 만들어내는 또 다른 예는 오토박싱(자동 형변환)이다. 박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.

# 아이템 7. 다 쓴 객체 참조를 해제하라  

메모리 누수 주의
- 자기 메모리를 직접관리하는 클래스(예 : [stack의 pop()](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter2/item7/Stack.java))
	- null 처리([stack의 pop()](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter2/item7/Stack.java))는 Stack과 같이 자기 메모리를 자신이 직접 관리하는 예외적인 경우에 사용하고 보통 다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위 밖으로 밀어내는 것이다.
- 캐시 
	- 엔트리가 살아 있는 캐시가 필요한 상황이라면 WeakHashMap을 사용하면 다 쓴 엔트리는 즉시 자동으로 제거될 것이다.
	- 쓰지 않는 엔트리는 ScheduledThreadPoolExecutor로 이따금 청소 
	- 캐시에 새 엔트리를 추가할 때 LinkedHashMap의 removeEldestEntry를 사용하면 부수 작업으로 쓰지 않는 엔트리 청소 수행
- 리스너, 콜백
	- 클라이언트가 콜백을 등록만 하고 명확히 해지하지 않는다면, 뭔가 조치해주지 않는 한 콜백은 계속 쌓여갈 것이다. 이럴 때 콜백을 약한 참조로 저장하면 가비지 컬렉터가 즉시 수거해간다. 예를 들어 WeakHashMap에 키로 저장하면 된다.

# 아이템 8. finalizer와 cleaner 사용을 피하라  

cleaner는 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자. 물론 이런 경우라도 불확실성과 성능 저하에 주의해야 한다.

대안은 AutoCloseable을 구현해주고 클라이언트에서 인스턴스를 다 쓰고 나면 close 메서드를 호출하면 된다(일반적으로 try-with-resources 사용).

[cleaner를 안전망으로 활용하는 AutoCloseable 클래스](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter2/item8/Room.java)

# 아이템 9. try-finally보다는 try-with-resources를 사용하라 

- finally 안에 있는 close()가 예외를 발생시킬 수도 있다. 
- try-with-resources문의 괄호()안에 객체를 생성하는 문장을 넣으면, 이 객체는 따로 close()를 호출하지 않아도 try 블럭을 벗어나면 자동으로 close()를 호출한다. 
	- 닫아야 하는 자원은 `AutoCloseable`인터페이스를 구현해서 close()를 호출
	- try 에서 예외 발생, close()에서도 예외가 발생하면 try는 일반 예외 형태로 출력, close() 예외는 Suppresed의 머리말을 달고 출력된다.
  
## [try finally](https://github.com/jbloch/effective-java-3e-source-code/tree/master/src/effectivejava/chapter2/item9/tryfinally)

## [try resource](https://github.com/jbloch/effective-java-3e-source-code/tree/master/src/effectivejava/chapter2/item9/trywithresources)


# Source

- [이펙티브 자바 Effective Java 3/E](http://www.yes24.com/Product/Goods/65551284)

