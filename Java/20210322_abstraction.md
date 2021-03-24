> *"스프링 입문을 위한 자바 객체 지향의 원리와 이해"* 를 읽고 정리한 내용입니다.

## 객체 지향은 인간 지향이다
객체 지향의 개념은 *"현실 세계처럼 프로그래밍할 수는 없을까?"* 라는 고민 속에서 탄생했다. 0과 1의 컴퓨터에 맞춰 사고하던 방식을 버리고 현실 세계를 인지하는 방식으로 프로그램을 만들자는 것이다. 따라서 **객체 지향은 직관적**이다.

- 세상에 존재하는 모든 것은 사물, 즉 객체다.
- 각각의 사물은 고유하다.
- 사물은 속성을 갖는다.
- 사물은 행위를 한다.

저자는 사물을 하나하나 이해하기보다는 클래스로 분류해서 이해하는 것이 인간의 인지법이라고 한다. 박지성(object), 김연아(object)라는 존재는 사람이라는 분류에 속한다. 사람이라는 분류 안의 객체(object)들은 나이, 몸무게, 키 등의 속성(property)과 먹다, 자다, 울다 등의 행위(method)를 갖고 있다.

객체 지향 이전에는 속성 따로, 행위 따로 분리된 형태로 프로그램을 작성했었다. 객체 지향에서는 우리가 주변에서 실제 사물을 인지하는 방식대로 객체 단위의 프로그래밍이 가능하다.

## 클래스 vs 객체 = 붕어빵틀 vs 붕어빵 ????
2학년 때 학교에서 객체지향 프로그래밍 수업을 들었는데, 그 때 교수님도 이런 비유를 하셨다. *"클래스는 붕어빵틀이고 객체는 붕어빵틀로 만들어낸 붕어빵이다."* 저자는 이것이 잘못된 비유라고 설명하고 있으며 책을 읽는 동안 그 설명에 동의할 수 밖에 없었다.

```java
클래스 객체명 = new 클래스();
붕어빵틀 붕어빵 = new 붕어빵틀();  // ????
```

붕어빵틀을 생산하는 기계가 있다고 가정해보자. 붕어빵틀이 붕어빵을 만들어내서 클래스라고 한다면 같은 논리로 붕어빵틀을 만들어내는 기계도 붕어빵틀을 찍어내는 클래스가 된다.

```java
붕어빵틀만드는기계 붕어빵틀 = new 붕어빵틀만드는기계();
```

이처럼 붕어빵틀만드는기계와 붕어빵틀이 클래스와 객체 관계가 아니듯 붕어빵틀과 붕어빵도 클래스와 객체 관계가 아니다. 붕어빵틀은 단순히 붕어빵을 만드는 팩토리다. 고로 클래스는 분류에 대한 개념이고, 객체는 실체다.

## 객체 지향의 4대 특성 - 1) 추상화
### 추상화는 모델링이다
객체 지향의 4대 특성 (추상화, 상속, 다형성, 캡슐화)은 클래스를 통해 구현된다. 앞에서 얘기한 클래스와 객체를 한 마디로 정의하면 아래와 같다.

- 객체는 유일무이한 사물이다.
- 클래스는 같은 특성을 지닌 여러 객체를 총칭하는 집합의 개념이다.

추상화란 **구체적인 것을 분해해서 관찰자가 관심 있는 특성만 가지고 재조합하는 것**을 뜻한다. 모델은 추상화를 통해 실제 사물을 단순하게 묘사하는 것이다. 즉, 객체 지향에서의 추상화는 모델링이다.

### 추상화와 T 메모리
[이전 포스팅](https://sangminlog.tistory.com/entry/oop-procedure-structured)에서 한 것처럼 T 메모리에 어떠한 변화가 생기는지 알아보자.

```java
public class Mouse {
    public String name;
    public int age;
    public int countOfTail;

    public void sing() {
        System.out.println(name + " 찍찍 !");
    }
}

public class MouseDriver {
    public static void main(String[] args) {
        Mouse mickey = new Mouse();
        mickey.name = "미키";
        mickey.age = 85;
        mickey.countOfTail = 1;
        mickey.sing();
        mickey = null;

        Mouse jerry = new Mouse();
        jerry.name = "제리";
        jerry.age = 73;
        jerry.countOfTail = 1;
        jerry.sing();
    }
}
```

1. main() 메소드 시작 전 `java.lang` 패키지와 모든 클래스들이 **스태틱 영역**에 배치된다. (JVM의 전처리 과정) 
    - Mouse 클래스의 name, age, countOfTail 변수들은 이름만 존재할 뿐 저장 공간이 없다. Mouse 객체에 속한 속성이기 때문에 객체가 생성돼야만 메모리 공간이 **힙 영역**에 할당된다.

![image](https://user-images.githubusercontent.com/46131688/112163285-39bdf000-8c30-11eb-810b-ae8c1f89bf50.png)

(사진 출처 - [벨로그](https://velog.io/@gaya309/TIL-%EC%B6%94%EC%83%81%ED%99%94%EC%99%80-T-%EB%A9%94%EB%AA%A8%EB%A6%AC))

2. `Mouse mickey = new Mouse()`
    - *Mouse mickey* : Mouse 객체에 대한 참조 변수 mickey를 만든다. 따라서 객체 참조 변수 mickey가 main() 메소드 스택 프레임 안에 생성된다.
    - *new Mouse()* : Mouse 클래스의 인스턴스를 하나 만들어 힙에 배치한다.
    - *'='* : Mouse 객체에 대한 주소를 참조 변수 mickey에 할당한다.

![image](https://user-images.githubusercontent.com/46131688/112164230-0fb8fd80-8c31-11eb-8e40-54a32425bd60.png)

(사진 출처 - [벨로그](https://velog.io/@gaya309/TIL-%EC%B6%94%EC%83%81%ED%99%94%EC%99%80-T-%EB%A9%94%EB%AA%A8%EB%A6%AC))

3. 객체 참조 변수 mickey와 참조 연산자(.)를 이용해 실제 힙 상의 객체에 접근하고, 각각의 속성에 값을 할당한다.

![image](https://user-images.githubusercontent.com/46131688/112166363-e7320300-8c32-11eb-8d3a-0fa5f163d259.png)

(사진 출처 - [벨로그](https://velog.io/@gaya309/TIL-%EC%B6%94%EC%83%81%ED%99%94%EC%99%80-T-%EB%A9%94%EB%AA%A8%EB%A6%AC))

4. `mickey = null`
    - 객체 참조 변수 mickey가 더 이상 힙 영역에 존재하는 Mouse 객체를 참조하지 않는다. 
    - 가비지 컬렉터가 Mouse 객체를 쓰레기로 인지하고 수거해 간다.

![image](https://user-images.githubusercontent.com/46131688/112166553-15174780-8c33-11eb-98a8-65a90b490568.png)

(사진 출처 - [벨로그](https://velog.io/@gaya309/TIL-%EC%B6%94%EC%83%81%ED%99%94%EC%99%80-T-%EB%A9%94%EB%AA%A8%EB%A6%AC))

5. jerry에 대해서도 2~4 과정이 일어난다. 이때 힙 영역에 새롭게 생성된 Mouse 객체는 mickey가 가리키던 Mouse 객체가 아니다.

main() 메소드에는 밑줄이 그어져 있는데 이는 UML 표기법에서 클래스 멤버는 밑줄을, 객체 멤버는 밑줄 없이 표현하기로 약속해서 그렇다고 한다. 자바에서는 `static` 키워드를 통해 클래스 멤버와 객체 멤버를 구분한다. 

### 클래스 멤버 vs 객체 멤버 = static 멤버 vs 인스턴스 멤버
객체는 유일무이하게 존재하는 실체이므로 속성에 값을 갖고 있고, 클래스는 분류 체계일 뿐이므로 속성에 값을 가질 수 없다. 그런데 다음과 같은 질문을 받았다고 가정해보자.

- 미키마우스의 꼬리는 몇 개인가?
- 제리의 꼬리는 몇 개인가?
- 쥐의 꼬리는 몇 개인가?

모든 질문에 대한 답은 **한 개**이다. 이를 표현한 T 메모리는 아래와 같이 형성될 것이다.

![image](https://user-images.githubusercontent.com/46131688/112167641-f2396300-8c33-11eb-961d-2cefd0207680.png)

(사진 출처 - [벨로그](https://velog.io/@gaya309/TIL-%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%A9%A4%EB%B2%84VS.-%EA%B0%9D%EC%B2%B4-%EB%A9%A4%EB%B2%84-static-%EB%A9%A4%EB%B2%84vs.-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-%EB%A9%A4%EB%B2%84))

모든 Mouse 객체의 *countOfTail* 값이 다 같음에도 불구하고 Mouse 객체 수만큼 아까운 메모리를 잡아먹고 있다. 속성 앞에 `static` 키워드를 붙여 해당 값을 클래스에 저장해보자.

```java
public class Mouse {
    public String name;
    public int age;
    public static int countOfTail = 1;

    public void sing() {
        System.out.println(name + "찍찍 !!");
    }
}

public class MouseDriver {
    public static void main(String[] args) {
        Mouse.countOfTail = 1;

        Mouse mickey = new Mouse();
        Mouse jerry = new Mouse();
        Mouse mightyMouse = new Mouse();

        // 객체명.countOfTail
        System.out.println(mickey.countOfTail);
        System.out.println(jerry.countOfTail);
        System.out.println(mightyMouse.countOfTail);

        // 클래스명.countOfTail
        System.out.println(Mouse.countOfTail);
    }
}
```

![image](https://user-images.githubusercontent.com/46131688/112168503-b2bf4680-8c34-11eb-91ea-3b820839be0d.png)

(사진 출처 - [벨로그](https://velog.io/@gaya309/TIL-%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%A9%A4%EB%B2%84VS.-%EA%B0%9D%EC%B2%B4-%EB%A9%A4%EB%B2%84-static-%EB%A9%A4%EB%B2%84vs.-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-%EB%A9%A4%EB%B2%84))

위의 그림처럼 countOfTail 속성은 스태틱 영역에 단 하나의 저장 공간을 갖게 됐다. 그러므로 `객체참조변수.countOfTail` 혹은 `클래스명.countOfTail`로 접근할 수 있다.

- 클래스 멤버 = static 멤버 = 정적 멤버
- 객체 멤버 = 인스턴스 멤버

이러한 정적 멤버 속성은 **해당 클래스의 모든 객체들이 같은 값을 가질 때 사용하는 것이 정석**이라고 한다. 위의 예시처럼 쥐의 꼬리는 항상 1개니까 *쥐 클래스의 꼬리 개수* 나 *고양이 클래스의 다리 개수* 처럼 말이다.

그렇다면 정적 메소드는 언제 사용하는 것이 좋을까? main() 메소드를 보면 알 수 있듯이 **정적 메소드는 객체들의 존재 여부에 관계없이 쓸 수 있다.** 참고로 정적 멤버들도 똑같다. 정적 멤버들은 객체가 아닌 클래스에 속해 있고, 클래스는 JVM 전처리 과정에서 스태틱 영역에 바로 배치되기 때문이다.

- 정적 속성 : 스태틱 영역에 클래스가 배치될 때 클래스 내부에 메모리 공간 확보
- 객체 속성 : 속성명만 있을 뿐 실제 메모리 공간 확보 X -> 힙 영역에 객체가 생성되야 메모리 공간 할당

---

#### 참고
- [스프링 입문을 위한 자바 객체 지향의 원리와 이해](http://www.yes24.com/Product/Goods/17350624)
- https://velog.io/@gaya309
