---
layout  : wiki
title   : 
summary : 
date    : 2022-06-18 17:38:55 +0900
updated : 2022-06-18 17:42:12 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}
# 아이템 15. 클래스와 멤버의 접근 권한을 최소화하라  

## 모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 한다.

(가장 바깥이라는 의미의) 톱레벨 클래스와 인터페이스에 부여할 수 있는 접근 수준은 package-private과 public 두 가지다.

package-private으로 선언하면 해당 패키지 안에서만 이용할 수 있다. package-private으로 선언하면 API가 아닌 내부 구현이 되어 언제든 수정할 수 있다. 반면 public으로 선언한다면 API가 되므로 하위 호환을 위해 영원히 관리해줘야만 한다.

한 클래스에서만 사용하는 package-private 톱레벨 클래스나 인터페이스는 이를 사용하는 클래스 안에 private static으로 중첩시켜 보자. 톱 레벨로 두면 같은 패키지의 모든 클래스가 접근할 수 있지만, private static으로 중첩시키면 바깥 클래스 하나에서만 접근할 수 있다.

- private: 멤버를 선언한 톱레벨 클래스에서만 접근할 수 있다.
- package-private: 멤버가 소속된 패키지 안의 모든 클래스에서 접근할 수 있다. 접근 제한자를 명시하지 않았을 때 적용되는 패키지 접근 수준이다(단 , 인터페이스의 멤버는 기본적으로 public이 적용된다).
- protected: package-private의 접근 범위를 포함하며, 이 멤버를 선언한 클래스의 하위 클래스에서도 접근할 수 있다(제약이 조금 따른다).

public 클래스의 protected 멤버는 공개 API이므로 영원히 지원돼야 한다. 또한 내부 동작 방식을 API 문서에 적어 사용자에게 공개해야 할 수도 있다. 따라서 protected 멤버의 수는 적을수록 좋다.

## public 클래스의 인스턴스 필드는 되도록 public이 아니어야 한다.

필드가 가변 객체를 참조하거나, final이 아닌 인스턴스 필드를 public으로 선언하면 그 필드에 담을 수 있는 값을 제한할 힘을 잃게 된다.

여기에 더해, 필드가 수정될 때 (락 획득 같은) 다른 작업을 할 수 없게 되므로 public 가변 필드를 갖는 클래스는 일반적으로 스레드 안전하지 않다.

## 길이가 0이 아닌 배열은 모두  변경 가능하니 주의하자.
클래스에서 public static final 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공해서는 안 된다. 이런 필드나 접근자를 제공한다면 클라이언트에서 그 배열의 내용을 수정할 수 있게 된다. 

```java
public static final Thing[] VALUES = {...};
```
- 해결책1: public 배열을 private으로 만들고 public 불변 리스트를 추가

```java
private static final Thing[] PRIVATE_VALUE = {...};
public static final List<Thing> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
```

- 해결책2: 배열을 private으로 만들고 그 복사본을 반환하는 public 메서드를 추가(방어적 복사)

```java
private static final Thing[] PRIVATE_VALUES = {...};
public static final Thing[] values() {
	return PRIVATE_VALUES.clone();
}
```
# 아이템 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라   
public 클래스는 절대 가변 필드를 직접 노출해서는 안 된다. 불변 필드라면 노출해도 덜 위험하지만 완전히 안심할 수는 없다. 하지만 package-private 클래스나 private 중첩 클래스에서는 종종 (불변이든 가변이든) 필드를 노출하는 편이 나을 때도 있다.
 
# 아이템 17. 변경 가능성을 최소화하라  
## 클래스를 불변으로 만들려면 다음 다섯 가지 규칙을 따르면 된다.
- 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않는다.
- 클래스를 확장할 수 없도록 한다.
	- 클래스를 final로 선언
	- 모든 생성자를 private 혹은 package-private으로 만들고 public 정적 팩터리를 제공하는 방법
- 모든 필드를 final로 선언한다.
- 모든 필드를 private으로 선언한다.
- 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다.

[불변 복소수 클래스](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item17/Complex.java)

피연산자에 함수를 적용해 그 결과를 번환하지만, 피연산자 자체는 그대로인 자체는 그대로인 프로그래밍 패턴을  함수형 프로그래밍이라 한다. 또한 메서드 이름으로 (add 같은) 동사 대신 (plus 같은) 전치사를 사용한 점에도 주목하자. 이는 해당 메서드가 객체의 값을 변경하지 않는다는 사실을 강조하려는 의도다.

## 장, 단점
### 불변 객체는 단순하다.

모든 생성자가 클래스 불변식(invariant)을 보장한다면 그 클래스를 사용하는 프로그래머가 다른 노력을 들이지 않아도 된다. 반면 가변 객체는 임의의 복잡한 상태에 놓일 수 있다. 변경자 메서드가 일으키는 상태 전이를 정밀하게 문서로 남겨놓지 않은 가변 클래스는 믿고 사용하기 어려울 수도 있다.

### 불변 객체는 근본적으로 스레드 안전하여 따로 동기화할 필요 없다.
불변 객체에 대해서는 그 어떤 스레드도 다른 스레드에 영향을 줄 수 없으니 불변 객체는 안심하고 공유할 수 있다. 따라서 불변클래스라면 한 번 만든 인스턴스를 최대한 재활용하기를 권한다.

```java
public static final Complex ZERO = new Complex(0, 0);
```
불변 클래스는 자주 사용되는 인스턴스를 캐싱하여  같은 인스턴스를 중복 생성하지 않게 해주는 정적 팩터리를 제공할 수 있다.

불변 객체를 자유롭게 공유할 수 있다는 점은 방어적 복사도 필요 없다는 결론으로 자연스럽게 이어진다. 아무리 복사해봐야 원본과 똑같으니 복사 자체가 의미가 없다.

### 불변 객체는 자유롭게 공유할 수 있음은 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있다.

### 객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많다.
불변 객체는 맵의 키와 집합(Set)의 원소로 쓰기에 안성맞춤이다. 맵이나 집합은 안에 담긴 값이 바뀌면 불변식이 허물어지는데, 불변 객체를 사용하면 그런 걱정은 하지 않아도 된다.

### 불변 객체는 그 자체로 실패 원자성을 제공한다
상태가 절대 변하지 않으니 잠깐이라도 불일치 상태에 빠질 가능성이 없다.

### 단점: 값이 다르면 반드시 독립된 객체로 만들어야 한다.
예컨대 백만 비트짜리 BigInteger에서 비트 하나만 바꿔야 한다고 해보자. 원본과 단지 한 비트만 다른 백만 비트짜리 인스턴스를 생성해야 한다.

이 문제에 대처하는 방법은 흔히 쓰일 다단계 연산들을 예측하여 기본 기능으로 제공하는 것이다.

클라이언트들이 원하는 복잡한 연산들을 정확히 예측할 수 있다면 package-private의 가변 동반 클래스만으로 충분하다. 그렇지 않다면 이 클래스를 public으로 제공하는 게 최선이다. 자바 플랫폼 라이브러리에서 이에 해당하는 대표적인 예가 바로 String 클래스다. 그렇다면 String의 가변 동반 클래스는? 바로 StringBuilder(와 구닥다리 전임자 StringBuffer)다.

## 불변 클래스를 만드는 다른 설계 방법

### 모든 생성자를 private 혹은 package-private으로 만들고 public 정적 팩터리를 제공하는 방법

```java
public class Complex {
	private final double re;
	private final double im;

	private Complex(double re, double im) {
		this.re = re;
		this.im = im;
	}

	public static Complex valueOf(double re, double im) {
		return new Complex(re, im);
	}
}
```
이 방식이 최선일 때가 많다. 바깥에서 볼 수 없는 package-private 구현 클래스를 원하는 만큼 만들어 활용할 수 있으니 훨씬 유연하다. 패키지 바깥의 클라이언트에서 바라본 이 불변 객체는 사실상 final이다. public이나 protected 생성자가 없으니 다른 패키지에서는 이클래스를 확장하는 게 불가능하기 때문이다.

# 아이템 18. 상속보다는 컴포지션을 사용하라  
메서드 호출과 달리 상속은 캡슐화를 깨뜨린다.
- 메서드 재정의 시 문제 발생
- 새로운 메서드를 추가하더라도 다음 릴리스에서 상위 클래스에 새 메서드 추가됐는데 하위 클래스에 추가한 메서드와 시그니처가 같고 반환 타입은 다르다면 컴파일조차 되지 않는다.

기존 클래스를 확장하는 대신 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 하자. 기존 클래스가 새로운 클래스의 구성요소로 쓰인다는 뜻에서 이러한 설계를 컴포지션(composition; 구성)이라 한다.

새 클래스의 인스턴스 메서드들은 (private 필드로 참조하는) 기존 클래스의 대응하는 메서드를 호출해 그 결과를 반환한다. 이 방식을 전달(forwarding)이라 하며, 새 클래스는 기존 클래스의 내부 구현 방식의 영향에서 벗어나며, 심지어 기존 클래스에 새로운 메서드가 추가되더라도 전혀 영향받지 않는다.

하나는 집합 클래스, 다른 하나는 전달 메서드만으로 이뤄진 재사용 가능한 전달 클래스

[래퍼 클래스 - 상속 대신 컴포지션 사용](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item18/InstrumentedSet.java)
[재사용할 수 있는 전달 클래스](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item18/ForwardingSet.java)

임의의 Set에 계측 기능을 덧씌워 새로운 Set으로 만드는 것이 이 클래스의 핵심이다. 상속 방식은 구체 클래스 각각을 따로 확장해야 하며, 지원하고 싶은 상위 클래스의 생성자 각각에 대응하는 생성자를 별도로 정의해줘야 한다.

하지만 지금 선보인 컴포지션 방식은 한 번만 구현해두면 어떠한 Set 구현체라도 계측할 수 있으며, 기존 생성자들과도 함께 사용할 수 있다.

```java
Set<Instant> times = new InstrumentedSet<>(new TreeSet<>(cmp));
Set<E> s = new InstrumentedSet<>(new HashSet<>(INIT_CAPACITY));
```

InstrumentedSet을 이용하면 대상 Set 인스턴스를 특정 조건하에서만 임시로 계측할 수 있다.

```java
static void walk(Set<Dog> dogs) {
	InstrumentedSet<Dog> iDogs = new InstrumentedSet<>(dogs);
	// 이 메서드에서는 dogs 대신 iDog를 사용한다.
}
```

다른 Set 인스턴스를 감싸고(wrap) 있다는 뜻에서 InstrumentedSet 같은 클래스를 래퍼 클래스라 하며, 다른 Set에 계측 기능을 덧씌운다는 뜻에서 데코레이터 패턴이라고 한다. 컴포지션과 전달의 조합은 넓은 의미로 위임(delegation)이라고 부른다. 단, 엄밀히 따지면 래퍼 객체가 내부 객체에 자기 자신의 참조를 넘기는 경우만 위임에 해당한다.
 

# 아이템 19. 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라  

상속용 클래스는 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지(자기사용) 문서로 남겨야 한다. ('재정의 가능'이란 public과 protected 메서드 중 final이 아닌 모든 메서드를 뜻한다.) API 문서의 메서드 설명 끝에 "Implementation Requirements"로 시작하는 절이 메서드 내부 동작 방식을 설명하는 곳이다. 이 절은 메서드 주석에 `@implSpec` 태그를 붙여주면 자바독 도구가 생성해준다.

상속용 클래스를 설계하기란 결코 만만치 않다. 클래스 내부에서 스스로를 어떻게 사용하는지(자기사용 패턴) 모두 문서로 남겨야 한다. 호출되는 메서드가 재정의 가능 메서드라면 어떤 순서로 호출하는지 각각의 결과에 이어지는 처리에 어떤 영향을 주는지도 담아야 한다(재정의 가능이란 public과 protected 메서드 중 final이 아닌 모든 메서드를 뜻한다).

하지만 이런 식은 "좋은 API 문서란 '어떻게'가 아닌 '무엇'을 하는지를 설명해야 한다"는 격언과 대치되지 않나? 그렇다. 상속이 캡슐화를 해치기 때문에 일어나는 안타까운 현실이다.


일단 문서화한 것은 그 클래스가 쓰이는 한 반드시 지켜야 한다. 그러지 않으면 그 내부 구현 방식을 믿고 활용하던 하위 클래스를 오동작하게 만들 수 있따. 다른 이가 효율 좋은 하위 클래스를 만들 수 있도록 일부 메서드를 protected로 제공해야 할 수도 있다.

그러니 클래스를 확장해야 할 명확한 이유가 떠오르지 않으면 상속을 금지하는 편이 나을 것이다. 상속을 금지하려면 클래스를 final로 선언하거나 생성자 모두를 외부에서 접근할 수 없도록 만들면 된다.

# 아이템 20. 추상 클래스보다는 인터페이스를 우선하라  

추상 클래스는 인터페이스와 달리 정의한 타입을 구현하는 클래스는 반드시 추상 클래스의 하위 클래스가 되어야 한다. 자바는 단일 상속만 지원하니, 추상 클래스 방식은 새로운 타입을 정의하는 데 커다란 제약을 안게 되는 셈이다.

두 클래스가 같은 추상 클래스를 확장하길 원한다면, 그 추상 클래스는 계층구조상 두 클래스의 공통 조상이어야 한다. 이는 계층구조에 혼란을 일으키는데 새로 추가된 추상 클래스의 모든 자손이 적절하지 않은 상황에서도 이를 상속하게 된다.

인터페이스는 믹스인 정의에 안성맞춤이다. 믹스인이란 클래스가 구현할 수 있는 타입으로, 믹스인을 구현한 클래스에 원래의 '주된 타입' 외에 특정 선택적 행위를 제공한다고 선언하는 효과를 준다.

한편, 인터페이스와 추상 골격 구현 클래스를 함께 제공하는 식으로 인퍼페이스와 추상 클래스의 장점을 모두 취하는 방법도 있다. 인터페이스로는 타입을 정의하고, 필요하면 디폴트 메서드 몇 개도 함께 제공한다. 그리고 골격 구현 클래스는 나머지 메서드들까지 구현한다.

(디폴트 메서드는 추상 메서드의 기본적인 구현을 제공하는 메서드로, 추상 메서드가 아니기 때문에 디폴트 메서드가 새로 추가되어도 해당 인터페이스를 구현한 클래스를 변경하지 않아도 된다.)

이렇게 해두면 단순히 골격을 확장하는 것만으로 이 인터페이스를 구현하는 데 필요한 일이 대부분 완료된다. 바로 템플릿 메서드 패턴이다.

관례상 골격 구현 클래스의 이름은 `AbstractInterface`로 짓는다.

[골격 구현을 사용해 완성한 구체 클래스](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item20/IntArrays.java)

골격 구현 작성은 가장 먼저, 인터페이스를 잘 살펴 다른 메서드들의 구현에 사용되는 기반 메서드들을 선정한다. 이 기반 메서드들은 골격 구현에서는 추상 메서드가 될 것이다. 그 다음으로, 기반 메서드들을 사용해 직접 구현할 수 있는 메서드를 모두 디폴트 메서드로 제공한다.

만약 인터페이스의 메서드 모두가 기반 메서드와 디폴트 메서드가 된다면 골격 구현 클래스를 별도로 만들 이유는 없다. 기반 메서드나 디폴트 메서드로 만들지 못한 메서드가 남아 있다면, 이 인터페이스를 구현하는 골격 구현 클래스를 하나 만들어 남은 메서드들을 작성해 넣는다. 골격 구현 클래스에는 필요하면 public이 아닌 필드와 메서드를 추가해도 된다.

[골격 구현 클래스](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item20/AbstractMapEntry.java)

# 아이템 21. 인터페이스는 구현하는 쪽을 생각해 설계하라  

디폴트 메서드를 선언하면, 그 인터페이스를 구현한 후 디폴트 메서드를 재정의하지 않은 모든 클래스에서 디폴트 구현이 쓰이게 된다.

디폴트 메서드는 (컴파일에 성공하더라도) 기존 구현체에 런타임 오류를 일으킬 수 있다. 

# 아이템 22. 인터페이스는 타입을 정의하는 용도로만 사용하라

인터페이스는 자신을 구현한 클래스의 인스턴스를 참조할 수 있는 타입  역할을 한다. 인터페이스는 오직 이 용도로만 사용해야 한다.

[상수 인터페이스 안티패턴 - 사용금지](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item22/constantinterface/PhysicalConstants.java)

상수 인터페이스 안티패턴에서 클래스 내부에서 사용하는 상수는 외부 인터페이스가 아니라 내부 구현에 해당한다.

상수를 공개할 목적이라면 더 합당한 선택지가 몇 가지 있다.

특정 클래스나 인터페이스와 강하게 연관된 상수라면 그 클래스나 인터페이스 자체에 추가해야 한다.

열거 타입으로 나타내기 적합한 상수라면 열거 타입으로 만들어 공개하면 된다(아이템 34).

그것도 아니라면, 인스턴스화할 수 없는 유틸리티 클래스(아이템 4)에 담아 공개하자.

[상수 유틸리티 클래스](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item22/constantutilityclass/PhysicalConstants.java)

# 아이템 23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라  

자바와 같은 객체 지향 언어는 타입 하나로 다양한 의미의 객체를 표현하는 수단을 제공한다. 바로 클래스 계층구조를 활용하는 서브타이핑(subtyping)이다.

가장 먼저 계층구조의 루트가 될 추상 클래스를 정의하고 태그 값에 따라 동작이 달라지는 메서드들을 루트 클래스의 추상 메서드로 선언한다. 코드의 `Figure` 클래스에서는 `area`가 이런 메서드에 해당한다.

[태그 달린 클래스를 클래스 계층구조로 변환 - Figure](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item23/hierarchy/Circle.java)

그런 다음 태그 값에 상관없이 동작이 일정한 메서드들을 루트 클래스에 일반 메서드로 추가한다. 모든 하위 클래스에서 공통으로 사용하는 데이터 필드들도 전부 루트 클래스로 올린다.

Figure 클래스에서는 태그 값에 상관없는 메서드가 하나도 없고, 모든 하위 클래스에서 사용하는 공통 데이터 필드도 없다. 그 결과 루트 클래스에서는 추상 메서드인 area 하나만 남게 된다.

다음으로 루트 클래스를 확장한 구체 클래스를 의미별로 하나씩 정의한다. Figure를 확장한 원(Circle) 클래스와 사각형(Rectangle) 클래스를 만들면 된다.

[태그 달린 클래스를 클래스 계층구조로 변환 - Circle](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item23/hierarchy/Circle.java)
[태그 달린 클래스를 클래스 계층구조로 변환 - Rectangle](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item23/hierarchy/Rectangle.java)

각 하위 클래스에는 각자의 의미에 해당하는 데이터 필드들을 넣는다. 그런 다음 루트 클래스가 정의한 추상 메서드를 각자의 의미에 맞게 구현한다.

[태그 달린 클래스를 클래스 계층구조로 변환 - Rectangle](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item23/hierarchy/Square.java)

# 아이템 24. 멤버 클래스는 되도록 static으로 만들라  

정적 멤버 클래스는 다른 정적 멤버와 똑같은 접근 규칙을 적용받는다. 예컨대 private으로 선언하면 바깥 클래스에서만 접근할 수 있는 식이다.

비정적 멤버 클래스의 인스턴스는 바깥 클래스의 인스턴스와 암묵적으로 연결된다. 그래서 비정적 멤버 클래스의 인스턴스 메서드에서 정규화된 this(클래스명.this 형태)를 사용해 바깥 인스턴스의 메서드를 호출하거나 바깥 인스턴스의 참조를 가져올 수 있다.

따라서 개념상 중첩 클래스의 인스턴스가 바깥 인스턴스와 독립적으로 존재할  수 있다면 정적 멤버 클래스로 만들어야 한다. 비정적 멤버 클래스는 바깥 인스턴스 없이는 생성할 수 없기 때문이다.

멤버 클래스에서 바깥 인스턴스에 접근할 일이 없다면 무조건 static을 붙여서 정적 멤버 클래스로 만들자. static을 생략하면 바깥 인스턴스로의 숨은 외부 참조를 갖게 된다. 이 참조를 저장하려면 시간과 공간이 소비된다. 더 심각한 문제는 가비지 컬렉션이 바깥 클래스의 인스턴스를 수거하지 못하는 메모리 누수가 생길 수 있다는 점이다.

중첩 클래스에는 네 가지가 있으며, 각각의 쓰임이 다르다. 메서드 밖에서도 사용해야 하거나 메서드 안에 정의하기엔 너무 길다면 멤버 클래스로 만든다. 멤버 클래스의 인스턴스 각각이 바깥 인스턴스를 참조한다면 비정적으로, 그렇지 않으면 정적으로 만들자. 중첩 클래스가 한 메서드 안에서만 쓰이면서 그 인스턴스를 생성하는 지점이 단 한 곳이고 해당 타입으로 쓰기에 적합한 인터페이스가 이미 있다면 익명 클래스로 만들고, 그렇지 않다면 지역 클래스로 만들자.

# 아이템 25. 톱레벨 클래스는 한 파일에 하나만 담으라  
소스 파일 하나에는 반드시 톱레벨 클래스(혹은 롭레벨 인터페이스)를 하나만 담자. 굳이 여러 톱레벨 클래스를 한 파일에 담고 싶다면 정적 멤버 클래스를 사용하는 방법을 고민해볼 수 있다. 다른 클래스에 딸린 부차적인 클래스라면 정적 멤버 클래스로 만드는 쪾이 일반적으로 더 나을 것이다. 읽기 좋고, privae으로 선언하면 접근 범위도 최소로 관리할 수 있기 때문이다.

[톱레벨 클래스들을 정적 멤버 클래스로 바꿔본 모습](https://github.com/jbloch/effective-java-3e-source-code/blob/master/src/effectivejava/chapter4/item25/Test.java)

# Source

- [이펙티브 자바 Effective Java 3/E](http://www.yes24.com/Product/Goods/65551284)

