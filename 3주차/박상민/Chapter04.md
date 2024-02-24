# 📕 [자바 객체 지향의 원리와 이해] 4장. 자바가 확장한 객체 지향

## 📌 abdstract 키워드 - 추상 메서드와 추상 클래스
추상 메서드(Abstract Method)를 간단하게 설명하면 선언부는 있는데 구현부가 없는 메서드를 말한다. 추상 메서드를 하나라도 갖고 있는 클래스는 반드시 추상 클래스(Abstract Class)로 선언해야 한다. 

**도대체 왜 몸체가 없이 선언만 있는 메서드가 필요할까?**

**문제점**
- 동물 클래스는 어떻게 울어야 하는지 모른다.
    - 우는 메서드를 정의할 수 없다.</br> -> 정의하더라도 인스턴스로 생성해선 안된다.
- 그런데 동물 참조 변수를 통해 모든 동물을 울게하고 싶다
    - 하위 클래스에서도 오버라이드 할 상위 메서드가 필요하다.


**해결 방법**</br>
추상 클래스와 추상 메서드 사용하면 1번의 문제를 해결 가능하다.
우는 메서드는 하위 클래스에게 구현을 맡길 수 있고 추상 클래스는 인스턴스가 될 수 없다.

2번 문제도 추상 메서드로 하위 클래스에게 메서드의 구현을 강제할 수 있다.</br>
또한, 상위 클래스의 참조 변수로 하위 클래스에서 오버라이드한 메서드 호출이 가능해진다.

- 추상 클래스는 인스턴스를 만들 수 없다.
- 추상 메서드는 하위 클래스에게 메서드의 구현을 강제한다.
- 추상 메서드를 포함하는 클래스는 반드시 추상 클래스여야 한다.

## 📌 생성자
new 클래스명()을 자세히 보면 열고 닫는 소괄호가 보인다. 이전에 사용했던 열고 닫는 소괄호는 메서드를 의미하는 것이었다. 클래스명에 붙은 소괄호는 뭔가 다른 뜻이 있을까? </br></br>
아니다. 클래스명()도 메서드다. 반환값이 없고 클래스명과 같은 이름을 가진 메서드를 객체를 생성하는 메서드라고 해서 객체 생성자 메서드라 한다.
그리고 줄여서 **생성자**라는 이름으로 더 많이 불린다.

**생성자에 대한 자바의 특징**
- 개발자가 아무런 생성자도 만들지 않으면 기본 생성자를 자동으로 만들어준다.
- 인자가 있는 생성자를 하나라도 만들면 자바는 기본 생성자를 만들지 않는다.

## 📌 클래스 생성 시의 실행 블록, static 블록
객체 생성자가 있다면 클래스 생성자도 있을 것이다. 자바는 그 기대의 절반만 부응한다. **클래스 생성자는 존재하지 않는다.** 그러나 클래스가 스태틱 영역에 배치될 때 실행되는 코드 블록이 있다. 바로 static 블록이다.
```java
public class 동물 {
	static {
    	System.out.println("동물 클래스 레디 온!")
    }
}
```
```java
public class Driver05 {
	public static void main(String[] args) {
		동물 뽀로로 = new 동물();
	}
}
```
실행해 보면 아래와 같은 메시지가 나온다.
```
동물 클래스 레디 온!
```
- - -
```java
public class Driver05 {
	public static void main(String[] args) {
		System.out.println("main 메서드 시작!");
		System.out.println(Animal.age);
	}
}

class Animal {
	static int age = 0;

	static {
		System.out.println("Animal class ready on!");
	}
}
```
위 코드에서 Animal의 스태틱 블록은 언제 처음 실행될까?
정답은 Animal.age로 정적 속성을 사용할 때이다.

**해당 클래스가 코드에서 맨 처음 사용될 때 T 메모리 스태틱 영역에 로딩**되며, 이 때 단 한번만 static 블록이 실행된다.

**클래스가 제일 처음 사용되는 경우**
- 클래스의 정적 속성 사용할 때
- 클래스의 정적 메서드 사용할 때
- 클래스의 인스턴스 만들 때

## 📌 final 키워드
final 클래스는 상속을 허락하지 않는다. -> 하위 클래스를 만들 수 없다.

**final과 변수**
```java
public class 고양이 {
	final static int 정적상수1 = 1;
	final static int 정적상수2;

	final int 객체상수1 = 1;
	final int 객체상수2;

	static {
		정적상수2 = 2;

		// 상수는 한번 초기화 되면 값을 변경할 수 없다.
		// 정적상수2 = 4;
	}

	고양이() {
		객체상수2 = 2;

		// 상수는 한번 초기화 되면 값을 변경할 수 없다.
		// 객체상수2 = 4;

		final int 지역상수1 = 1;
		final int 지역상수2;

		지역상수2 = 2;
	}
}
```
변수에 final이 붙었다면 그 의미는 무엇일까?
바로 변경 불가능한 상수가 된다. 정적 상수는 선언 시에, 또는 정적 생성자에 해당하는 static 블록 내부에서 초기화가 가능하다. 객체 상수 역시 선언 시에, 또는 객체 생성자 또는 인스턴스 블록에서 초기화할 수 있다.

**final과 메서드**
```java
public class 동물 {
	final void 숨쉬다() {
		System.out.println("호흡 중");
	}
}

class 포유류 extends 동물 {
	// 에러 발생: Cannot override the final method from 동물
	/*
	 * void 숨쉬다() { System.out.println("호흡 중"); }
	 */
}
```
메서드가 final이면 최종 메서드이므로 오버라이딩을 금지한다.

## 📌 Instaceof 연산자
```java
class 동물 {

}

class 조류 extends 동물 {

}

class 펭귄 extends 조류 {

}

public class Driver {
	public static void main(String[] args) {
		동물 동물객체 = new 동물();
		동물 조류객체 = new 조류();
		동물 펭귄객체 = new 펭귄();

		System.out.println(동물객체 instanceof 동물);

		System.out.println(조류객체 instanceof 동물);
		System.out.println(조류객체 instanceof 조류);

		System.out.println(펭귄객체 instanceof 동물);
		System.out.println(펭귄객체 instanceof 조류);
		System.out.println(펭귄객체 instanceof 펭귄);

		System.out.println(펭귄객체 instanceof Object);
	}
}
```
실행하면 모든 경우에 true가 출력된다.

`펭귄 객체 instanceof 동물`이 참인 이유는 "객체 참조 변수 타입"이 아닌 "실제 객체의 타입"에 의해 처리되기 때문이다.

> `instanceof 연산자`가 강력하기는 하지만 객체 지향 설계 5원칙 가운데 LSP(리스코프 치환 원칙)을 어기는 코드에서 주로 나타나는 연산자이기에 리팩토링의 대상이 아닌지 점검해야 한다.

## 📌 this 키워드
this: 객체가 자기 자신을 지칭할 때 쓰는 키워드
```java
class 펭귄 {
	int var = 10;

	void test() {
		int var = 20;

		System.out.println(var);
		System.out.println(this.var);
	}
}

public class Driver {
	public static void main(String[] args) {
		펭귄 뽀로로 = new 펭귄();
		뽀로로.test();
	}
}
```

![](https://velog.velcdn.com/images/pp8817/post/3ef4208b-6cad-4a14-8009-a29dd8b6122c/image.png)

위 코드를 실행하게 되면
- `System.out.println(var);`: 뽀로로.test() 지역변수가 있는 스택프레임의 var를 출력
- `System.out.println(this.var);`: 펭귄 객체 변수는 지역변수와 이름이 중복되므로 this.var라고 지정해줘야 한다.

- 지역 변수와 속성의 이름이 같은 경우 지역 변수가 우선한다.
- 객체 변수와 이름이 같은 지역 변수가 있는 경우 객체 변수를 사용하려면 this 접두사로 사용한다.
- 정적 변수와 이름이 같은 지역 변수가 있는 경우 정적 변수를 사용하려면 클래스명을 접두사로 사용한다.

## 📌 super 키워드
super: 바로 위 상위 클래스의 인스턴스를 지칭한다.
```java
class 동물 {
	void method() {
		System.out.println("동물");
	}
}

class 조류 extends 동물 {
	void method() {
		super.method();
		System.out.println("조류");
	}
}

class 펭귄 extends 조류 {
	void method() {
		super.method();
		System.out.println("펭귄");

		// Syntax error on token "super", Identifier expected
		// super.super.method();
	}
}

public class Driver {
	public static void main(String[] args) {
		펭귄 뽀로로 = new 펭귄();
		뽀로로.method();
	}
}
```
위 코드처럼 super 키워드를 통해 상위 클래스의 인스턴스 메서드를 호출할 수 있다.

> super.super 형태의 상위의 상위 클래스의 인스턴스에는 접근이 불가능하다.

- - -
**출처**</br>
[스프링 입문을 위한 자바 객체 지향의 원리와 이해](https://search.shopping.naver.com/book/catalog/32462919817)
https://github.com/expert0226/oopinspring