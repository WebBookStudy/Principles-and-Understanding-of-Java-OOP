# 📕 [자바 객체 지향의 원리와 이해] 7장. 객체 지향 설계 5원칙

스프링을 이해하려면 _POJO를 기반_으로 스프링 삼각형이라는 애칭을 가진 IoC/DI, AOP, PSA라고 하는 스프링의 3대 프로그래밍 모델에 대해 이해가 필수다.

![](https://velog.velcdn.com/images/pp8817/post/27ade0f4-01f4-45fb-85ce-f3d93ca17819/image.png)
스프링 삼각형을 이해하면 그 조합으로 이루어진 수많은 복잡한 부가 프레임워크를 이해할 수 있다.

# ⭐️ IoC/DI - 제어의 역전/의존성 주입
**프로그래밍에서 의존성이란 무엇일까?**
ex) 운전자가 자동차를 생산한다. -> 자동차가 내부적으로 타이어를 생산한다. (운전자 -> 자동차 -> 타이어) 의존
-> 전체(의존하는 객체)가 부분(의존되는 객체)에 의존한다.

- **집합(Aggregation) 관계**: 부분이 전체와 다른 생명 주기를 가질 수 있다.
- **구성(Compostion) 관계**: 부분은 전체와 같은 생명 주기를 갖는다.

**원하는 클래스 다이어 그램**
![](https://velog.velcdn.com/images/pp8817/post/d7d42492-9221-48cd-a60b-31f0a82a9a32/image.png)

## 📌 자바만으로 의존관계를 직접 해결하는 코드
**Car 객체 생성자에서 new Car()**
```java
interface Tire {
	String getBrand();
}

public class KoreaTire implements Tire {
	public String getBrand() {
		return "코리아 타이어";
	}
}

public class AmericaTire implements Tire {
	public String getBrand() {
		return "미국 타이어";
	}
}
```
**Car 클래스에서 tire의 의존성을 직접 생성 후 연결 실행**
```java
public class Car {
	Tire tire;

	public Car() {
		tire = new KoreaTire();
		// tire = new AmericaTire();
	}

	public String getTireBrand() {
		return "장착된 타이어: " + tire.getBrand();
	}
}
```
자동차의 생성자 코드에서 직접 타이어를 생성해 참조변수와 연결한다.
-> Car 클래스는 인터페이스 뿐만 아니라 구체화된 KoreaTire에도 의존하게 된다.

## 📌 스프링 없이 의존성 주입하기 1 - 생성자를 통한 주입
**말로 풀어서 표현**
- 운전자가 타이어를 생산한다.
- 운전자가 자동차를 생산하면서 타이어를 장착시킨다.

**자바 표현**
- `Tire tire = new KoreaTire();`
- `Car car = new Car(tire);` -> 생성자 주입을 통해 외부에서 주입되는 tire 의존성

```java
public class Car {
	Tire tire;

	public Car(Tire tire) { // 생성자 주입
		this.tire = tire;
	}

	public String getTireBrand() {
		return "장착된 타이어: " + tire.getBrand();
	}
}
public class Driver {
	public static void main(String[] args) {
		Tire tire = new KoreaTire();
		//Tire tire = new AmericaTire();
		Car car = new Car(tire);

		System.out.println(car.getTireBrand());
	}
}
```
**무엇이 개선됐을까?**
기존의 직접 Car가 tire를 생산하고 연결하는 것은 tire 교체 시 Car의 수정이 불가피하므로 코드의 **유연성이 떨어지고 확장성도 떨어졌다.**

의존성 주입을 이용함으로서 자동차가 구체적인 객체를 생성하고 연결하는 것이 아니라 운전자가 대신 원하는 의존성을 생성하고 연결해준다.

따라서, Car객체는 의존성 주입을 통해 추상적인 인터페이스에 의존하면서 실행이 가능해진다.
그리고 Car객체는 수정 없이 확장이 가능해진다.

**생성자 주입 방식의 장점**
- 의존관계 설정이 되지 않으면 객체생성 불가 -> 컴파일 타임에 인지 가능, NPE 방지
- 의존성 주입이 필요한 필드를 final 로 선언가능 -> Immutable
- (스프링에서) **순환참조 감지가능** -> 순환참조시 앱구동 실패
- 테스트 코드 작성 용이
    - DI의 핵심은 관리되는 클래스가 DI 컨테이너에 의존성이 없어야 한다는 것이다.
      -> 즉, **독립적으로 인스턴스화가 가능한 POJO(Plain Old Java Ojbect)** 여야 한다는 것이다. DI 컨테이너를 사용하지 않고서도 단위 테스트에서 인스턴스화할 수 있어야 한다.


>
[생성자 주입을 @Autowired를 사용하는 필드 주입보다 권장하는 하는 이유](https://madplay.github.io/post/why-constructor-injection-is-better-than-field-injection)

## 📌 스프링 없이 의존성 주입하기 2 - 속성을 통한 의존성 주입
- ex) `car.setTire(tire);`

생성자 주입을 하면 더 이상 타이어를 교체할 수 없다.
사용자가 원할 때 타이어를 교체하려면 생성자 주입이 아닌 속성을 통한 의존성 주입이 필요하다.
- 타이어 상시 교체를 위한 속성 주입


## 📌 스프링을 통한 의존성 주입 - @Autowired를 통한 의존성 주입

반드시 설정자 메서드를 통해 tire 값을 주입해야할까?
`@Autowired Trie tire` 를 사용해보자.

이를 이용하면 설정자 메서드를 사용하지 않고도 _스프링 프레임워크가 설정 파일을 통해 설정자 메서드 대신 속성을 주입해준다._

- 기존 설정 파일
```java
<bean id="car" class="expert003.Car">
	<property name="tire" ref="koreaTire"></property>
</bean>
```
- @Autowired 후 새로운 설정 파일
```java
<bean id="car" class="expert003.Car"></bean>
```
property 태그가 사라졌다.
@Autowired를 tire에 설정해줌으로서 car의 property를 자동으로 엮을 수 있게 되었다.
-> XML 파일 설정을 @Autowired 설정으로 대체함

**@Autowired를 통한 속성 매칭 규격**
![](https://velog.velcdn.com/images/pp8817/post/d7592cd5-84c1-42ca-b46a-fabc0f13898f/image.png)
@Autowired 설정된 속성들은 위의 규칙을 통해서 등록된 빈을 선택하게 된다.

# ⭐️ AOP - Aspect?
- **AOP: 관점 지향 프로그래밍**

스프링 DI가 의존성에 대한 주입이라면 스프링 AOP는 로직(code)에 대한 주입이다.

**여러 모듈에서 발생하는 공통적인 부분**
![](https://velog.velcdn.com/images/pp8817/post/d61e16d1-0394-40ff-8bb9-c2e9c89c42c1/image.png)

입금, 출금, 이체 모듈 모두 <로깅, 보안, 트랜잭션> 기능이 반복적으로 나타나는 것을 볼 수 있다.
이처럼 다수의 모듈에 공통적으로 나타나는 부분이 존재하는 것을 "**횡단 관심사**"라고 한다.

DB 커넥션을 생각해보자.
쿼리(핵심관심사)를 날리기 전 DB 커넥션을 연결(횡단 관심사)해야만 한다.
(코드 = 핵심관심사 + 횡단 관심사)

'핵심 관심사'는 모듈별로 다르지만 '횡단 관심사'는 모듈별로 반복되어 중복해서 나타난다.
-> "반복/중복은 분리해서 한 곳에서 관리하라"

![](https://velog.velcdn.com/images/pp8817/post/b74fdf19-b21b-42f1-a13f-8b01e0090d3e/image.png)
Around, Before, After, AfterReturning, AfterThrowing -> 총 5개 위치에 주입 로직(코드) 주입 가능

**기존 코드**
```java
public class Boy {
	public void runSomething() {
		System.out.println("열쇠로 문을 열고 집에 들어간다.");

		try {
			System.out.println("컴퓨터로 게임을 한다.");
		} catch (Exception ex) {
			if (ex.getMessage().equals("집에 불남")) {
				System.out.println("119 에 신고한다.");
			}
		} finally {
			System.out.println("소등하고 잔다.");
		}

		System.out.println("자물쇠를 잠그고 집을 나선다.");
	}
}
```
핵심 관심사와 횡단 관심사가 한 메서드에 같이 사용되고 있다.

**개선**
```java
public interface Person {
	void runSomething();
}

public class Boy implements Person {
	public void runSomething() {
		System.out.println("컴퓨터로 게임을 한다.");
	}
}
```
```java
@Aspect
public class MyAspect {
	@Before("execution(* runSomething())")
	public void before(JoinPoint joinPoint) {
		System.out.println("얼굴 인식 확인: 문을 개방하라");
		// System.out.println("열쇠로 문을 열고 집에 들어간다.");
	}
}
```
- @Aspect는 이 클래스를 이제 AOP에서 사용하겠다는 의미
- @Before는 대상 메서드 실행 전에 이 메서드를 실행하겠다는 의미

메서드의 joinPoint는 Boy.runSomething()을 의미한다. 이제 사용자가 얼굴 인식하고 문을 여는 것이 아니라 스프링 프레임워크가 사용자를 인식해 자동으로 문을 열어준다.

사실 Boy의 핵심관심사만 남기기 위해 4개의 파일(인터페이스, Boy, XML, Aspect파일)의 수정이 필요했다.
하지만 이로 인해 Boy는 핵심 관심사만 가지게 되었고 추가 개발과 유지보수가 굉장히 편리해졌다.
또한, AOP를 Boy.java에 적용함으로써 단일책임원칙도 자연스레 적용할 수 있게 되었다.

![](https://velog.velcdn.com/images/pp8817/post/2d0e91d1-ad15-42ff-b2fa-5595df5c2a5a/image.png)
- Boy가 Person이라는 인터페이스가 필요한 이유
    - 스프링 AOP가 인터페이스 기반이기 때문이다.
- MyAspect가 추가된 이유
    - 공통적으로 나타나는 횡단 관심사를 누군가는 처리해야 하기 때문 (MyAspect가 횡단 관심사를 처리함)
    - XML에서 빈을 설정해 객체의 생성과 의존성 주입을 스프링 프레임워크에 위임할 수 있다.

## 📌 프록시 패턴을 이용해 횡단 관심자를 핵심 관심사에 주입하기

**프록시를 이용한 메서드 간접 호출**
![](https://velog.velcdn.com/images/pp8817/post/3fa95ba8-aa81-4727-9a46-6643530512f0/image.png)
메서드를 호출하면 "프록시"가 그 요청을 받아서 진짜 객체에게 전달한다.
이때 "**프록시**"는 그냥 전달만 하지 않고 **주고 받는 내용을 감사하거나 조작할 수 있다.**

메서드 Caller와 Callee는 프록시에 존재를 알지 못한다. 오직 스프링 프레임워크만이 프록시의 존재를 알고 있다.

그렇기 때문에 메서드 Caller, Callee 프록시가 무슨 일을 하든 전혀 상관없이 자신의 로직을 수행할 수 있다.

스프링프레임워크는 XML파일의 <aop:aspectj-autoproxy />지시자로 프록시의 존재를 알 수 있다.

**스프링 AOP의 핵심**
- 스프링 AOP는 인터페이스 기반
- 스프링 AOP는 프록시 기반
- 스프링 AOP는 런타임 기반


# ⭐️ PSA - 일관성 있는 추상화
서비스 추상화의 예로 JDBC를 들 수 있다. JDBC라고 하는 표준 스펙이 있기에 오라클을 사용하든 MySQL을 사용하든 MS-SQL을 사용하든 Connection, Statement, ResultSet을 이용해 공통된 방식으로 코드 작성이 가능하다.

이처럼 데이터베이스 종류와 관계없이 같은 방식으로 제어할 수 있는 이유는 디자인 패턴에서 설명했던 어댑터 패턴을 활용했기 때문이다.

다수의 기술을 공통의 인터페이스로 제어할 수 있게 한 것 -> **서비스 추상화**

스프링 프레임워크는 서비스 추상화를 위해 다양한 어댑터를 제공한다.

예를 들면 OXM(Object XML Mapping)기술에는 Castor, XMLBeans 등 다양한 기술이 있는데 이 기술들의 API는 모두 제각각이다.

이때 제각각인 API를 위한 어댑터를 제공함으로써 어떤 기술을 쓰던 일관된 방식으로 코드를 작성할 수 있게 해준다.
다른 기술로의 교체 또한 쉽게 할 수 있다.
> PSA 예) **ORM**, 캐시, 트랜잭션, OXM 등

**이처럼 서비스 추상화를 해주면서 일관성 있는 방식을 제공하기 때문에 PSA(일관성 있는 추상화)라고 한다.**


- - -
**출처** <br>
[스프링 입문을 위한 자바 객체 지향의 원리와 이해](https://search.shopping.naver.com/book/catalog/32462919817)
https://github.com/expert0226/oopinspring


