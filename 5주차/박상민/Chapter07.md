# 📕 [자바 객체 지향의 원리와 이해] 6장. 스프링이 사랑한 디자인 패턴

'객체지향의 4대 특성'은 객체지향을 잘 사용하기위한 '도구'이다.
'객체지향의 5대 원칙'은 이러한 도구를 올바르게 사용하는 원칙으로 볼 수 있다.
그렇다면 디자인패턴은 무엇에 비유할 수 있을까?

'디자인 패턴'은 레시피이다.

실제 개발 현장에서 비즈니스 요구 사항을 처리하면서 만들어진 다양한 해결책 중 많은 사람들이 인정한 '베스트  프렉티스'를 정리한 것이다. (디자인 패턴은 당연히 객체 지향 특성과 설계 원칙을 기반으로 구성)

스프링 역시 다양한 디자인 패턴을 활용하고 있다.
# ⭐️ 스프링이 사랑한 디자인 패턴들

## 📌 어댑터 패턴 (Adapter Pattern)
![](https://velog.velcdn.com/images/pp8817/post/7273814a-c8cd-4790-838a-8150220690e3/image.png)

```
📌 동작 순서

1. 핸들러 조회: 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(Controller)를 조회한다.
2. 핸들러 어댑터 조회: 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.
3. 핸들러 어탭터 실행: 핸들러 어탭터를 실행한다.
4. 핸들러 실행: 핸들러 어댑터가 실제 핸들러를 실행한다.
5. ModelAndView 반환: 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서 반환한다.
6. viewResolver 호출: 뷰 리졸버를 찾고 실행한다.
7. View반환:뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환한다.
8. 뷰 렌더링: 뷰를 통해서 뷰를 렌더링한다.
```

`Adapter` 패턴은 `Front Controller`의 유연하지 못한 단점을 해결한다. `Adapter` 패턴은 여러 `Controller interface`와 호환 가능하도록 `Front Controller`와 `Controller` 사이에 `Adapter`를 배치하는 것이다.
> 이는 돼지코로 비유할 수 있다. Controller A, B interface가 있을 때 각각을 110V, 220V 전기 콘센트로 생각하자. A, B interface를 모두 수용하기 이해서는 110V에 220V 돼지코(Adapter)를 추가해야 한다. 이때 220V 돼지코가 Adapter의 역할을 한다.

## 📌 프록시 패턴 (Proxy Pattern)
프록시는 대리자라는 의미이다. 대리자라고 하면 **다른 누군가를 대신해 그 역할을 수행하는 존재를 말한다.**

**프록시 패턴 사용**
- 클래스
```java
public interface IService {
	String runSomething();
}

public class Proxy implements IService {
	IService service1;

	public String runSomething() {
		System.out.println("호출에 대한 흐름 제어가 주목적, 반환 결과를 그대로 전달");

		service1 = new Service();
		return service1.runSomething();
	}
}

public class Service implements IService {
	public String runSomething() {
		return "서비스 짱!!!";
	}
}
```
- 메인 메서드
```java
public class ClientWithProxy {
	public static void main(String[] args) {
		// 프록시를 이용한 호출
		IService proxy = new Proxy();
		System.out.println(proxy.runSomething());
	}
}
```
![](https://velog.velcdn.com/images/pp8817/post/992d41e2-db86-4077-b4b0-73cf32fad00c/image.png)
서비스 객체가 들어갈 자리에 대리자 객체를 대신 투입한다.

**프록시 패턴의 중요 포인트**
- 대리자(프록시)는 실제 서비스와 같은 이름의 메서드를 구현
- 대리자는 실제 서비스에 대한 참조 변수를 갖는다. (합성)
- 대리자는 실제 서비스의 같은 이름을 가진 메서드를 호출하고 그 값을 클라이언트에게 반환한다.
- 대리자는 실제 서비스의 메서드 호출 전후에 별도의 로직을 수행할 수도 있다.

> 프록시 패턴은 실제 서비스의 반환값은 변경하지 않고 제어의 흐름을 변경하거나 다른 로직을 수행하기 위해 사용한다.

## 📌 데코레이터 패턴 (Decorator Pattern)
데코레이터는 도장/도배업자를 의미한다. 여기서는 장식자라는 뜻을 가지고 논리를 풀어 보자. 데코레이터 패턴이 원본에 장식을 더하는 패턴이라는 것이 이름에 잘 드러나 있다. 데코레이터 패턴은 프록시 패턴과 구현 방법이 같다.

**프록시 패턴과 구현 방법이 같으나 최종적으로 반환하는 반환값에 장식을 덧입힌다.**
```java
public class Decoreator implements IService {
	IService service;

	public String runSomething() {
		System.out.println("호출에 대한 장식 주목적, 클라이언트에게 반환 결과에 장식을 더하여 전달");

		service = new Service();
		return "정말" + service.runSomething();
	}
}
```
반환 값에 다른 값이 추가된 것을 확인할 수 있다.

데코레이터 패턴의 중요포인트는 프록시 패턴의 중요 포인트에 '반환값에 변화를 줄 수 있다'는 점이다.

## 📌 싱글톤 패턴(Singleton Pattern)
싱글톤 패턴이란 인스턴스를 하나만 만들어서 사용하기 위한 패턴이다. 싱글톤 패턴은 오직 인스턴스를 하나만 마들고 그것을 계속해서 **재사용**한다.

'커넥션 풀', '스레드 풀', '디바이스 설정 객체' 등과 같은 경우 인스턴스를 여러 개 만들게 되면 **불필요한 자원을 사용하게 되고, 프로그램이 예상치 못한 결과를 만들 수 있다.**

싱글톤 패턴을 적용하면 두 개의 객체가 존재할 수 없으므로

- 객체 생성을 위한 new에 제약을 걸어야 하고
- 만들어진 단일 객체를 반환할 수 있는 메서드가 필요하다.
    - new를 실행할 수 없도록 생성자에 private 접근 제어자 설정
    - 유일한 단일 객체를 반환할 수 있는 "정적 메서드" 필요
    - 유일한 단일 객체를 참조할 "정적 참조 변수" 필요

**싱클톤 패턴 적용을 위한 클래스 구성**
```
public class Singleton {
	static Singleton singletonObject; // 정적 참조 변수

	private Singleton() {
	}; // private 생성자

	// 객체 반환 정적 메서드
	public static Singleton getInstance() {
		if (singletonObject == null) {
			singletonObject = new Singleton();
		}

		return singletonObject;
	}
}
```
생성자를 사용하지 못하도록 설정했고 "정적 메서드"를 실행해 정적 참조 변수에 객체가 할당되어 있지않으면 할당해서 반환하고 할당되어 있으면 참조 변수를 반환하도록 만들었다.

**테스트 코드**
```java
public class Client {
	public static void main(String[] args) {
		// private 생성자임으로 new 할 수 없다.
		// Singleton s = new Singleton();

		Singleton s1 = Singleton.getInstance();
		Singleton s2 = Singleton.getInstance();
		Singleton s3 = Singleton.getInstance();

		System.out.println(s1);
		System.out.println(s2);
		System.out.println(s3);

		s1 = null;
		s2 = null;
		s3 = null;
	}
}
```
위 테스트 코드는 s1, s2, s3 모두 같은 인스턴스를 참조하게 된다.
![](https://velog.velcdn.com/images/pp8817/post/2d59aacf-f164-4050-9ef5-1f4930099e4d/image.png)
4개의 참조 변수가 모두 하나의 단일 객체를 참조하는 것을 확인할 수 있다.'

**싱글톤 패턴의 특징**
- private 생성자를 갖는다.
- 단일 객체 참조 변수를 "정적 속성"으로 갖는다.
- 단일 객체 참조 변수가 참조하는 "단일 객체"를 반환하는 getInstance() 정적 메서드를 갖는다.
- 단일 객체는 쓰기 가능한 속성을 갖지 않는 것이 정석이다. (읽기 전용은 가능)

## 📌 템플릿 메서드 패턴 (Template Method Pattern)
공통적으로 사용하는 메서드는 상위클래스에서 구현하고, 하위클래스마다 달라지는 것은 추상클래스로 구현 강제화하는 디자인 패턴이다.

하위클래스마다 달라질 수도 있는 것은 오버라이드 가능한 훅 메서드로 만든다.

개와 고양이를 놀아주는 playWithOwner라는 메서드가 있을 때 하나의 상위 클래스로 실제 인스턴스에 맞는 메서드로 실행하고 싶을 때 사용한다.

**Animal 추상 클래스**
```java
public abstract class Animal {
	// 템플릿 메서드
	public void playWithOwner() {
		System.out.println("귀염둥이 이리온...");
		play();
		runSomething();
		System.out.println("잘했어");
	}

	// 추상 메서드
	abstract void play();

	// Hook(갈고리) 메서드
	void runSomething() {
		System.out.println("꼬리 살랑 살랑~");
	}
}
```
상위클래스에서 템플릿을 제공하는 playWithOwner() 템플릿 메서드를 제공한다.
그리고 템플릿 메서드 안에 있는 play() 추상 메서드와 runSomething() 메서드가 있다.
- 추상 메서드 하위 클래스에서 구현 강제함
- Hook 메서드 오버라이드 자유

이 때 템플릿 메서드의 구성요소를 알아 보자.

- 템플릿 메서드 : 공통 로직 수행, 로직 수행 중 추상메서드/훅 메서드 호출
- 템플릿 메서드에서 호출하는 추상메서드 -> 반드시 하위클래스가 오버라이딩 해야한다.
- 템플릿 메서드에서 호출하는 훅 메서드 -> 하위 클래스가 선택적으로 오버라이딩 한다.

![](https://velog.velcdn.com/images/pp8817/post/5c743bb7-a6d8-466c-9d82-ac36434833b0/image.png)


## 📌 팩터리 메서드 패턴 (Factory Method Pattern)

팩터리 메서드는 객체를 생성 반환하는 메서드를 말한다.
팩터리 메서드 패턴은 하위 클래스에서 팩터리 메서드를 오버라이딩 해서 객체를 반환하는 것을 의미한다.

**추상 팩토리 메서드**
```
public abstract class Animal {
	// 추상 팩터리 메서드
	abstract AnimalToy getToy();
}
```

**추상 팩토리 메서드 오버라이딩**
```
public class Dog extends Animal {
	// 추상 팩터리 메서드 오버라이딩
	@Override
	AnimalToy getToy() {
		return new DogToy();
	}
}
```

**메인 메서드**
```
public class Driver {
	public static void main(String[] args) {
		// 팩터리 메서드를 보유한 객체들 생성
		Animal bolt = new Dog();
		Animal kitty = new Cat();

		// 팩터리 메서드가 반환하는 객체들
		AnimalToy boltBall = bolt.getToy();
		AnimalToy kittyTower = kitty.getToy();

		// 팩터리 메서드가 반환한 객체들을 사용
		boltBall.identify();
		kittyTower.identify();
	}
}
```


팩터리 메서드 패턴을 요약하면 '오버라이드된 메서드가 객체를 반환하는 패턴'이다.
팩터리 메서드 패턴이 의존 역전 원칙(DIP)을 활용하고 있음을 알 수 있다.

## 📌 전략 패턴 (Strategy Pattern)
전략 패턴은 다지인 패턴의 꽃이다.

**전략 패턴 구성요소 3가지**
- 전략 메서드를 가진 전략 객체
- 전략 객체를 사용하는 컨텍스트 (전략 객체의 사용자/소비자)
- 전략 객체를 생성해 컨테스트에 주입하는 클라이언트 (전략 객체의 공급자)

![](https://velog.velcdn.com/images/pp8817/post/0914291a-8408-499c-ab3f-b3f8829ec273/image.png)

군인이 있다고 상상해 보자. 그리고 그 군인이 사용할 무기가 있다고 하자. 보급 장교가 무기를 군인에게 지급해 주면 군인은 주어진 무기에 따라 전투를 수행하게 된다. 이 이야기를 전략 패턴에 따라 구분해 보면 무기는 전략이 되고, 군인은 컨텍스트, 보급 장교는 제 3자, 즉 클라이언트가 된다.

**전략 인터페이스**
```java
public interface Strategy {
	public abstract void runStrategy();
}
```
**전략 인터페이스 구현**
```java
public class StrategyGun implements Strategy {
	@Override
	public void runStrategy() {
		System.out.println("탕, 타당, 타다당");
	}
}

public class StrategySword implements Strategy {
	@Override
	public void runStrategy() {
		System.out.println("챙.. 채쟁챙 챙챙");
	}
}
```
**전략을 사용하는 컨텍스트**
```java
public class Soldier {
	void runContext(Strategy strategy) {
		System.out.println("전투 시작");
		strategy.runStrategy();
		System.out.println("전투 종료");
	}
}
```
**전략 패턴의 클라이언트**
```java
public class Client {
	public static void main(String[] args) {
		Strategy strategy = null;
		Soldier rambo = new Soldier();

		// 총을 람보에게 전달해서 전투를 수행하게 한다.
		strategy = new StrategyGun();
		rambo.runContext(strategy);

		System.out.println();

		// 검을 람보에게 전달해서 전투를 수행하게 한다.
		strategy = new StrategySword();
		rambo.runContext(strategy);

		System.out.println();

		// 활을 람보에게 전달해서 전투를 수행하게 한다.
		strategy = new StrategyBow();
		rambo.runContext(strategy);
	}
}
```

클라이언트는 전략을 다양하게 변경하면서 컨텍스트를 실행할 수 있다.

전략 패턴을 한 문장으로 요약하면 `클라이언트가 전략을 생성해 전략을 실행할 컨텍스트에 주입하는 패턴`이라고 할 수 있다.
>전략 패턴에는 OCP, DIP가 적용된다.

## 📌 템플릿 콜백 패턴 (Template Callback Pattern)
템플릿 콜백 패턴은 전략 패턴의 변형으로, 스프링 3대 프로그래밍 모델 중 하나인 **DI에서 사용하는 특별한 형태의 전략 패턴**이다.

전략패턴과 모든 것이 동일한데 **전략을 익명 내부 클래스로 정의해서 사용한다.**

따라서, 전략패턴에서 사용했던 StrategyGun, StrategySword는 필요가 없다.

```java
public class Client {
	public static void main(String[] args) {
		Soldier rambo = new Soldier();

		rambo.runContext(new Strategy() {
			@Override
			public void runStrategy() {
				System.out.println("총! 총초종총 총! 총!");
			}
		});

		System.out.println();

		rambo.runContext(new Strategy() {
			@Override
			public void runStrategy() {
				System.out.println("칼! 카가갈 칼! 칼!");
			}
		});

		System.out.println();

		rambo.runContext(new Strategy() {
			@Override
			public void runStrategy() {
				System.out.println("도끼! 독독..도도독 독끼!");
			}
		});
	}
}
```
익명 내부 클래스를 사용해서 오버라이드해서 사용하는 것을 볼 수 있다. 따로 클래스를 구현하지 않고 사용한다.

그러나 위 코드는 중복 코드가 발생한다. 리팩토링의 여지가 넘쳐난다.

**리팩터링 해보기**
```java
public class Soldier {
	void runContext(String weaponSound) {
		System.out.println("전투 시작");
		executeWeapon(weaponSound).runStrategy();
		System.out.println("전투 종료");
	}

	private Strategy executeWeapon(final String weaponSound) {
		return new Strategy() {
			@Override
			public void runStrategy() {
				System.out.println(weaponSound);
			}
		};
	}
}
```
**클라이언트**
```java
public class Client {
	public static void main(String[] args) {
		Soldier rambo = new Soldier();
		
		rambo.runContext("총! 총초종총 총! 총!");
		
		System.out.println();
		
		rambo.runContext("칼! 카가갈 칼! 칼!");
		
		System.out.println();
		
		rambo.runContext("도끼! 독독..도도독 독끼!");
	}
}
```
중복되는 전략을 생성하는 코드가 컨텍스트 내부로 들어왔다. (중복되는 부분을 컨텍스트로 이관)

스프링은 이런 형식으로 리팩터링된 템플릿 콜백 패턴을 DI에 적극 활용하고 있다.


- - -
**출처** <br>
[스프링 입문을 위한 자바 객체 지향의 원리와 이해](https://search.shopping.naver.com/book/catalog/32462919817)
https://github.com/expert0226/oopinspring