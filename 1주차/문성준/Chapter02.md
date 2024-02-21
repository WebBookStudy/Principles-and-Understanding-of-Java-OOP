# ch02 자바와 절차적/구조적 프로그래밍

- 변수가 메모리에서 어떻게 저장되고 사용되는지, 메서드가 어떻게 호출되고 메모리에 어떤 변화를 일으키는지 살펴보면서 객체 지향 프로그래밍과 스프링으로 나아가기 위해 학습해 볼 예정이다.
- JVM : 자바 가상 기계 → 가상의 컴퓨터
- JDK : 자바 개발 도구
- JRE : 자바 실행 환경
- 객체지향 프로그램의 메모리 사용하는 방식
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/4b6dfdbf-c87c-4215-a69b-d44e7b7316d3/Untitled.png)
    
- 함수와 메서드는 차이가 거의 없다. 굳의 찾는다면 함수는 클래스나 객체와 아무 관계가 없지만 메서드는 반드시 클래스 안에서 존재해야 한다는 것이다. → 객체지향 언어에서 클래스 외부에 존재할 수 있는 것은 없다

### main() 메서드 : 메서드 스택 프레임

`main()` 메서드는 프로그램이 실행되는 시작점이다.
main() 메서드가 실행될 때 메모리, 특히 T 메모리엔 어떤 일이 일어날까?

```java
public class Start {
    public static void main(String[] args) {
        System.out.println("Hello OOP!!!");
    }
}
```

- 위 코드를 예시로 아래처럼 나눌 수 있다.
스태틱 영역 : 클래스들의 놀이터
스택 영역 : 메서드들의 놀이터
힙 영역 : 객체들의 놀이터

### **main() 메소드가 실행되기 전 JVM에서 수행하는 전처리 작업들**

- java.lang 패키지를 T 메모리의 스태틱 영역에 배치 (해당 패키지가 있기에 sout() 같은 메서드 사용가능)
- 프로그램 상의 모든 클래스 와 import된 패키지를 스태틱 영역에 배치

메소드가 실행되기 위해 스택 프레임이 스택영역에 할당된다.

메인 메소드가 실행되려면 메소드의 인자 args를 저장할 변수 공간을 스택 프레임 맨 밑에 확보해야 한다. 메소드의 인자의 변수 공간을 할당 한다는 것이다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/d2c3b445-c173-46b6-a40c-f78e7ae46de1/Untitled.png)

위처럼 T 메모리가 구성되면 main() 메소드가 실행되며 **“Hello OOP!!!”** 가 출력된다
단순 print문을 위해 JRE와 JVM은 많은 일을 처리했다.

JRE는 자바 가상 기계인 JVM을 부팅하고, JVM은 메모리 구조를 만들고 java.lang 패키지 로딩, 클래스 로딩,
main() 메소드 스택 프레임 배치, 변수 공간 배치 등의 일을 처리했다.

메소드의 끝을 나타내는 닫 는 중괄호를 만나면 해당 메소드의 스택 프레임이 소멸된다.
프로그램의 시작와 끝인 main() 메소드가 끝나면 JRE는 JVM을 종료하고 JRE 자체도 운영 체제 상의 메모리에서 사라진다. (main() 메서드는 프로그램의 시작점이자 끝이기 때문)

### 변수와 메모리 : T 메모리 구조 변화

```java
public class Start2 {
    public static void main(String[] args) {
        int i;
        i = 10;

        double d = 20.0;
    }
}
```

코드를 보면 우선 변수를 선언하고 이후 값을 할당하는데, `main()`메소드 내의 변수인 i 선언 시 JVM이 `main()` 메서드 스택 프레임 안에 밑에서부터 변수 공간을 마련하고 빈 값으로 설정된다. 
이후 값을 할당하면 스택 프레임의 변수공간에도 값이 할당된다.
d는 선언과 동시에 값을 할당했지만 i와 동일하게 2개의 명령어로 봐야한다.
d도 동일하게 변수공간을 확보한 후 값을 할당한다.

### 블록 구문과 메모리 : 블록 스택 프레임

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/4fe11f82-6556-4361-ad2a-0d9f9e2f21b2/Untitled.png)

```java
public class Start3 {
    public static void main(String[] args) {
        int i = 10;
        int k = 10;

        if (i == 10) {
            int m = k + 5;
            k = m;
        } else {
            int p = k + 10;
            k = p;
        }        
        //k = m + p;}}
```

변수 할당 T 메모리 구조
if ~ else 문 조건에 부합한 스택 프레임이 생성된다.
if 프레임 안의 변수 m을 할당하고 k를 연산에 참여시킨다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/a41fbb6a-5f44-4508-85d5-a71cdb41674c/Untitled.png)

마찬가지로 if 문을 종료하는 닫는 중괄호를 만나면 스택영역에서 사라진다
만약 주석되어 있는 14번째 줄을 주석해제 한다면 어떻게 될까?
“m cannot be resoleved to a variable”
“p cannot be resoleved to a variable”
if 스택 프레임은 스택영역에서 이미 삭제 되었기 때문에 해당 변수들을 찾을 수 없다.

**→ if 블록 스택 프레임이 존재한다는 사실을 꼭 기억하자.**

### 지역 변수와 메모리 : 변수는 어디 있을까?

T 메모리는 세 개의 영역이 있는데 변수는 스태틱 영역, 스택 영역, 힙 영역중 어디에 있는 걸까?
→ 세 군데 모두 있다. 그러나 세 군데 각각에 있는 변수는 각기 다른 목적을 가지고 있다.
그리고 각각의 이름도 지역 변수, 클래스 멤버 변수, 객체 멤버 변수로 다르다.

- **지역 변수** : 스택 영역, 스택 프레임 안에서만 있으며 스택 프레임이 사라지면 함께 사라진다.
- **클래스 멤버 변수** : 스태틱 영역, 스태틱 영역에 한번 자리 잡으면 JVM이 종료될 때까지 고정된(static) 상태로 그 자리를 지킨다.
- **객체 멤버 변수** : 힙 영역, 객체 멤버 변수들은 객체와 함께 가비지 컬렉터라고 하는 힙 메모리 회수기에 의해 일생을 마친다.

<aside>
❗ 외부 스택 프레임에서 내부 스택 프레임의 변수에 접근하는 것은 불가능하나 그 역은 가능하다.
→ 외부 블록에서 내부 블록의 변수에는 접근할 수 없지만 내부 블록에서 외부 블록의 변수에 접근하는 것은 가능하다는 말이다.

</aside>

### 메서드 호출에 따른 T 메모리 구조 변화

```java
public static void main(String[] args) {
        int k = 5;
        int m;

        m = square(k);
    }

    private static int square(int k) {
        int result;

        k = 25;

        result = k;
        return result;
    }
}
```

`square()`메서드 스택 프레임에는 반환값을 저장할 변수 공간이 맨 아래, 다음으로 인자를 저장할 변수 공간, 마지막으로 메서드의 지역 변수가 자리 잡는다.
- `square()`메서드 스택 프레임을 만든다.
- `main()` 메서드와 square()메소드의 k는 별도의 변수 공간으로 영향을 주지 않는다
- 돌려줄 값을 가져야할 반환값 변수에 `result` 변수에 담긴 값이 복사 된다.(`result` 변수는 스택 프레임이 삭제되면서 함께 삭제되기 때문)
- `main()`메서드와 `square()` 메서드는 서로의 지역변수에 접근할 수 없다.
→ 메서드 블랙박스화 입력 값들과 반환값에 의해서만 메소드 사이에서 값이 전달될 뿐 서로 내부의 지역변수를 볼 수 없다는 것을 의미한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/6b1f946a-83c0-4e47-b688-385cdbbc7fd1/Untitled.png)

### **메서드 간 변수 참조가 불가한 이유**

1. 고유 공간에 침범하면 문제 유발 가능성이 있다.
2. **포인터 문제** 자바가 메소드 내부의 지역 변수의 m에 접근하려면 포인터 값을 알아야한다. 자바가 가장 환영받은 이유는 포인터가 없기 떄문이다.
3. 호출하는 메소드 내부의 지역 변수를 호출당하는 쪽에서 제어할 수 있게 코드를 만들려면 결국 포인터를 주고받아야 한다.포인터가 없기 때문에 메서드 스펙 프레임 사이에 변수 참조는 불가능하다.

메서드를 호출하면서 인자로 전달되는 것은 변수 자체가 아니라 변수가 저장한 값만 복제하여 전달하는 Call By Value 방식을 사용하고 있다.

## 전역 변수와 메모리

Call By Value방법 말고도 메소드를 종료할 때 반환 값을 넘겨주는 것이 하나 더 있다.

“전역 변수”를 사용하는 방법이다.

```java
public class Start5 {
    static int share;

    public static void main(String[] args) {
        share = 55;

        int k = fun(5, 7);

        System.out.println(share);
    }

    private static int fun (int m, int p) {
        share = m + p;

        return m - p;
    }
}
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/05072a06-25e4-4333-ae55-5ba60b5b891b/Untitled.png)

클래스안에 정의 된 static 변수는 해당 클래스의 스태틱 영역 안에 변수 공간이 할당된다.

share 변수는 스태틱 영역에 변수 공간이 할당된다.

프로젝트 규모에 따라 코드가 커지면서 전역 변수의 값이 변경되면 코드를 추적해야만 전역변수의 값을 알 수 있다.

**결론적으로 전역변수를 필할 수 있다면 즐기지 말고 피해야 한다.**

## 멀티 스레스 / 멀티 프로세스

### 멀티 스레드

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/4ca6b26f-ebf0-4772-ba53-ece1ea9e99b6/Untitled.png)

하나의 T 메모리만 사용하고 스택 영역을 스레드 개수만큼 분할해서 사용 → **메모리 사용 적음**

멀티스레드에서의 전역 변수 사용 문제점

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/286110a1-a120-4e46-8b44-ff8f88386de9/Untitled.png)

스레드 1의 입장에서 갑자기 20이라는 값이 출력되는 문제가 있다.

쓰기 가능한 전역 변수를 사용하게 되면 스레드 안전성이 깨진다고 표현한다.
보완 방법으로는 락(lock)을 거는 방법이 있지만 락을 거는 순간 스레드의 장점은 버린 것과 같다.

### **멀티 프로세스**

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/e4fab498-646d-42a5-bae7-3e9ddca267b0/Untitled.png)

다수의 데이터 저장 영역, 다수의 T 메모리를 갖는 구조다.

각 프로세스마다 각자의 T 메모리가 있고 각자 고유의 공간으로 서로 참조할 수 없다. → 메**모리 사용 많음**
