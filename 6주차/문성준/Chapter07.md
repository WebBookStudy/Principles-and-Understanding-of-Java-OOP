# ch07 스프링 삼각형과 설정 정보

# IoC/DI - 제어의 역전/의존성 주입

## 프로그래밍에서 의존성이란?

**의사 코드**

운전자가 자동차를 생성한다.

자동차는 내부적으로 타이어를 생산한다.

**자바로 표현**

```java
new Car();
Car 객체 생성자에서 new Tire();
```

의존성은 `new`이다. new를 실행하는 Car와 Tire 사이에서 Car가 Tire에 의존한다.

결론적으로 전체가 부분에 의존한다고 표현할 수 있다. 즉, 의존 관계는 new로 표현되는 것이다.

또한, 의존성이란, 외부에서 라는 뜻을 내재하고 있다.

## 1. 생성자를 통한 의존성 주입

```java
public class Driver {
		public static void main(String[] args) {
			Tire tire = new KoreaTire()：
			//Tire tire = new AmericaTire()；
			Car car = new Car(tire)；
			System.out.println(car.getTireBrand())；
}
}
```

이러한 방법을 통해서 얻는 이점은 tire가 미국산이든 국내산이든 Tire 인터페이스를 구현한 구현체라면 정상적으로 작동하게 된다. 확장성이 좋아지며, Car클래스의 수정이 불필요해진다.

즉, 코드가 한결 깔끔해진 것을 알 수 있다.

## 2. 속성을 통한 의존성 주입

```java
public class Driver {
		public static void main(String[] args) {
				Tire tire = new KoreaTire()；
				Car car = new Car()；
				car.setTire(tire)；
				System.out.println(car.getTireBrand())；
		}
}
```

### 스프링을 통한 의존성 주입 - XML 파일 사용

```java
public class Driver {
		public static void main(String[] args) {
				ApplicationContext context = new ClassPathXmlApplicationContext("expert002/expert002.xml")；
				Car car = context, getBean( "car", Car. class)；
				Tire tire = context. getBeanf'tire ", Tire, class)；
				car.setTire(tire)；
				System.out.printin(car.getTireBrand())；
		}
}
```

사실상 해당 챕터에서는 xml 파일과 상호작용하는 코드를 보여준다.

우리는 스프링부트를 사용함으로 xml 파일이 자동적으로 매핑이 되지만, 스프링을 쓰게되면 해당 책에서 설명하는 것처럼 되는 것이다. 즉, 스프링 부트만 사용해와서 xml이 어떻게 돌아가는 지에 대해 생각해본 적이 없는데, 어떻게 돌아가는 로직인지 이해하게 되어서 좋은 경험을 한 것 같다.

(참고로 maven은 xml을 통해서 의존성 주입을 함. gradle은 build.gradle에서 의존성 주입을 추가함)

```java
〈?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http：//www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">
〈bean id="tire" class-"expert002.KoreaTire"></bean>
〈bean id="america Tire" class="expertO02. America Tire"></bean〉
〈bean id="car" class="expert002.Car"></bean>
</beans>
```

빈에 직접 등록해서 사용하는 것을 확인할 수 있고, 버전 및 기타 빈의 id, class가 어디인지에 대한 정보들이 있다.

## 3. 스프링을 통한 의존성 주입 - 스프링 설정 파일(XML)에서 속성 주입

- 자바로 표현한다면 ?

```java
Applicationcontext context = new ClassPathXmlApplicationContext("expert0G3/expertO03.xml")；
Car car = context.getBeanC'car", Car.class)；
```

- xml

```java
<bean id="koreaTire" class="expertO03. KoreaTire"></bean>
<bean id=”americaTire" class="expert003.AmericaTire"></bean>
<bean id="car" class="expert003.Car">
<property name=”tire" ref="koreaTire"〉〈/property〉
</bean>
```

즉, 스프링과 xml은 떼어낼래야 떼어낼 수 없는 서로 공생하는 관계라고 이해해도 무방하다.

→ xml을 통해 편리하고 유지보수성 및 좋은 시너지를 내기 때문이다.

## 4. 스프링을 통한 의존성 주입 - @Autowired를 통한 속성 주입

사실사 이 어노테이션이 핵심이라고 봐도 무방하다.

왜냐하면 필자도 해당 어노테이션을 실제로 자주 사용하기 때문이다.

```java
Tire tire；
public void setTire(Tire tire) {
	this.tire = tire；
}
// ---------변경 후-----------
@Autowired
Tire tire;  
```

설정자 메서드를 이용하지 않고도 스프링 프레임워크가 설정 파일을 통해 설정자 메서드 대신 송성을 주입해주는 것을 확인할 수 있다.

(스프링을 사용하면)xml 파일을 확인해보면 property 태그가 사라진 것을 확인할 수 있다,.

해당 `@Autowired` 어노테이션을 통해 car의 property를 자동으로 엮어줄 수 있으므로 (자동 의존성 주입) 생략이 가능해진 것이다.

여기서 궁금해야할 점이 생겼을 것이다.

xml을 보면 bean에서 id 속성이 없는데 어떻게 매칭한 것일까???

→ 바로, 인터페이스의 구현 여부가 마법의 답이다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/d558b466-06a1-4b97-8d07-a8cd40babe3f/Untitled.heic)

***@Autowirde 어노테이션은 type 기준 매칭에 있기 때문이다.
만약 같은 타입을 구현한 클래스가 여러 개 있다면 그때 bean 태그의 id로 구분해서 매칭하게 되는 것이다.***

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/6c0162eb-5ba5-4de0-955e-57a2586abd3b/Untitled.heic)

## 5. 스프링을 통한 의존성 주입 - @Resource를 통한 속성 주입

```java
public class Car {
		@Resource
		Tire tire；
		public String getTireBrand() {
			return "장착된 타이어: " + tire.getBrand()；
		}
}
```

`@Resource`는 자바 표준 어노테이션이다. 스프링 프레임워크를 사용하지 않는다면
`@Antowired`는 사용할 수 없고 오직 `@Resource`만을 사용해야 한다. 그리고 이전 절에서 살펴봤듯
이 `@Autowired`의 경우 type과 id 가운데 매칭 우선순위는 type이 높다. `@Resource`의 경우 type
과 id 가운데 매칭 우선순위는 id가 높다. `@Resource`의 경우 id로 매칭할 빈을 찾지 못한 경우 type
으로 매칭할 빈을 찾게 된다.

## 6. 스프링을 통한 의존성 주입 一 @Autowired vs. @Resource vs. (property) 태그

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/99c9192d-d5d7-4a84-924b-e8dd0d68cc83/Untitled.png)

- `@Autowired`와 `@Resource` 중 에서는 `@Resource` 추천
- `@Resource`와 (property) 중에서는〈property〉 추천

## AOP - Aspect? 관점? 핵심 관심사? 횡단 관심사?

심오하고, 어려운 내용이다.

관점지향 프로그래밍이며, Aspect-Oriented Programming의 약자이다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/b5882442-fd4d-4617-83f9-9887e0001b2f/Untitled.png)

입금, 출금, 이체 모듈에서 로깅, 보안 , 트랜잭션 기능이 반복적으로 나타나는 것을 볼 수 있다. 이처럼 다수의 모듈에서 공통적으로 나타나는 부분이 존재하는데, 이것을 ***횡단 관심사***라고 한다. 

모듈에서 반복되어 나타나는 부분을 분리하여 처리할 수 있다. 그렇게 되면 중복된 코드를 줄일 수 있을 것이다.

사실 이것을 모듈을 분해해서 작은 모듈로 나눈 후 클래스로 정의해서 사용하면 더욱 응집도가 높고 결합성이 낮은 코드를 만들 수 있는 아주 객체지향적인 코드가 탄생하게 될 것이다.

### 예제 코드

```java
// 7-38
public class Boy implements Person {
	public void runSomething() {
		System.out.printin("컴퓨터로 게임을 한다.");
	}
}
```

```java
// 7-40
public class Start {
	public static void main(String[] args) {
		Applicationcontext context = new ClassPathXmlApplicationContext("aop002/aop002.xml");
		Person romeo = context.getBean("boy", Person.class)；
		romeo.runSomething()；
	}
}
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans
xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns ： aop="http ： //www. springframework, org/schema/aop"
xsi:schemaLocation="
http：//www. springframework.org/schema/aop
http：//www. springframework, org/schema/aop/spring-aop-3.1,xsd
http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">
<aop:aspectj-autoproxy />
〈bean id="myAspect" class="aop002.MyAspect" Z>
〈bean id="boy" class="aop002.Boy" Z>
</beans>
```

```java
// 7-45
@Aspect
public class MyAspect {
Before("execution(public void aop002.Boy.runSomething())")
public void before(JoinPoint joinPoint){
		System.out.println("얼굴 인식 확인: 문을 개방하라");
		//System.out.printin(“열쇠로 문을 열고 집에 들어간다.,,);
}
```

- @Aspect는 이 클래스를 이제 AOP에서 사용하겠다는 의미다.
- @Before는 대상 메서드 실행 전에 이 메서드를 실행하겠다는 의미다.
- JoinPoint는 @Before에서 선언된 메서드인 aop002.Boy.runSomething()을 의미한다

### 일단 덤벼 보자 - 설명편

이렇게 핵심 관심사와 횡단 관심사를 나누게 되면 각 역할을 적절히 분리 하였으므로(SRP) 추가 개발이나 유지보수 관점에서 보면 무척이나 편한 코드가 된다. 

CGLiB 라이브러리를 사용하게 되면 인터페이스 없이도 AOP를 적용할 수 있지만 추천하는 방법은 아니다. CGLiB 라이브러리를 사용해야 할 경우는 코드를 변경할 수 없는 서드파티 모듈 안에 final로 선언된 클래스에 AOP를 적용해야 하는 경우 정도다.

***AOP의  핵심을 3 개의 문장으로 요약한다면 ?***

- 스프링 AOP는 인터페이스(interlace) 기반이다
- 스프링 AOP는 프록시(proxy) 기반이다.
- 스프링 AOP는 런타임(runtime) 기반이다

참고로, 프록시가 개입할 수 있는 시점은 무려 5개나 된다. (자세한 설명은 287page 참고)

스프링 AOP를 적용할 수 있는시점은 Before, After, AfterReturning, AfterThrowing, Around.

### 용어편

| 용어 | 의미 |
| --- | --- |
| Aspect | 여러개의 Advice와 여러 개의 Pointcut의 결합체 |
| Advisor | 한개의 Advice + 한개의 Pointcut |
| Advice | pointcut에 적용할 로직, 즉 메서드를 의미한다. 언제 무엇을 적용할지 |
| JoinPoint | Aspect 적용이 가능한 지점, 빈의 모든 메서드에 해당한다. |
| Pointcut | Aspect 적용 위치 지정자, JoinPoint의 부분 집합이다. 어디에 해당하는 |

## 일단 덤벼 보자 - POJO와 XML 기반 AOP

```java
<aop: config>
<aop: aspect ref="myAspect ">
<aop:before method="before" pointcut="execution(* runSomethingf))" />
</aop: aspect〉
</aop: config〉
```

```java
public class MyAspect {
public void before（JoinPoint joinPoint）{
		System.out.printin（"얼굴 인식 확인: 문을 개방하라"）;
		//System.out.printin（"열쇠로 문을 열고 집에 들어간다."）;
	}
}
```

XML설정으로 인해 MyAspect 클래스는 스프링 프레임워크에 의존하지 않는 POJO가 된다.

## AOP 정리

```java
import org.aspectj.lang.JoinPoint；
import org.aspectj.lang.annotation.After；
import org.aspectj.lang.annotation.Aspect；
import org.aspectj.lang.annotation.Before；
import org.aspectj.lang.annotation.Pointcut；
import org.springframework.stereotype.Component；
@Component
@Aspect
public class MyAspect {
		@Pointcut("execution(* runSomething())")
		private void iampc() {}
		// 여긴 무엇을 작성해도 의미가 없어요.
		@Before("iampc()" )
		public void before(JoinPoint joinPoint){
			System.out.printin(“얼굴 인식 확인: 문을 개방하라");
		}
		@After("iampc()")
		public void lockDoor(JoinPoint joinPoint){
			System.out.printin("주인님 나갔다: 어이 문 잠가!!!");
		}
}
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans 생략… >
<aop：aspectj-autoproxy />
<bean id="myAspect" class="aop004.MyAspect" />
<bean id="boy" class="aop004.Boy" />
<bean id="girl" class="aop004.Girl" />
<aop：config>
<aop：pointcut expression="execution(* runSomething())" id="iampc"/>
<aop：aspect ref="myAspect"〉
<aop：before method="before" pointcut-ref="iampc" />
<aop：after method="lockDoor" pointcut-ref="iampc" />
</aop：aspect>
</aop：config>
</beans>
```

## PSA - 일관성 있는 서비스 추상화

일관성 있는 서비스 추상화다.

서비스 추상화의 예로 JDBC가 있다.

데이터 베이스 종류에 관계없이 같은 방식으로 제어할 수 있는 이유는 디자인 패턴에서 설명했던 어댑터(변환기) 패턴을 활용했기 때문이다.

이처럼 어댑터 패턴을 적용해 같은 일을 하는 다수의 기술을 공통의 인터페이스로 제어할 수 있게 한 것을 ***서비스
추상화***라고 한다.

이처럼 서비스 추상화를 해주면서 그것도 일관성 있는 방식을 제공한다고 해서 PSA라고 한다.
스프링은 OXM뿐만 아니라 ORM, 캐시, 트랜잭션 등 다양한 기술에 대한 PSA를 제공한다.
