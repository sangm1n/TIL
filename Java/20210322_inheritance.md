> *"스프링 입문을 위한 자바 객체 지향의 원리와 이해"* 를 읽고 정리한 내용입니다.

## 객체 지향의 4대 특성 - 2) 상속
### 상속: 재사용 + 확장
흔히 상속이라고 하면 아래와 같은 가족 관계도를 떠올리기 쉽다. 

![image](https://user-images.githubusercontent.com/46131688/112171213-fa46d200-8c36-11eb-89a9-8c83d082a0d9.png)

(사진 출처 - [네이버 블로그](https://blog.naver.com/PostView.nhn?blogId=ghaisk1820&logNo=220710771428&proxyReferer=https:%2F%2Fwww.google.com%2F))

하지만 객체 지향의 상속은 계층도나 조직도가 아닌 **분류도**로 이해해야 한다.

![image](https://user-images.githubusercontent.com/46131688/112171617-54e02e00-8c37-11eb-93ac-5103cd76efdf.png)

(사진 출처 - [티스토리](https://not-to-be-reset.tistory.com/147))

위 사진은 상속 관계가 아니다. 동물은 포유류의 부모가 아니고, 포유류 또한 고래의 부모가 아니기 때문이다. 하지만 객체 지향에서 말하는 상속 관계는 이게 맞다. 동물이라는 분류를 세분화해서 포유류와 조류가 나오고, 포유류를 세분화해서 고래와 박쥐가 나오는 것이다. 한 마디로 정의하면 상속이란

- **상위 클래스의 특성을 하위 클래스에서 상속하고 거기에 필요한 특성을 추가**

즉, 확장해서 사용할 수 있다는 의미이다. 고로 부모 클래스-자식 클래스라는 표현보다 *상위 클래스-하위 클래스* 또는 *슈퍼 클래스-서브 클래스* 라는 표현이 더 맞다고 한다.

상속 관계에서는 반드시 `하위 클래스 = 상위 클래스`를 만족해야 한다. 앞서 나온 분류도를 보자.

- 포유류 = 동물
- 고래 = 포유류
- 고래 = 동물

이는 객체 지향 설계 5원칙 중 `LSP` (리스코프 치환 원칙)를 나타내는 말이다.

### 상속의 강력함
- *동물.java*
```java
public class 동물 {
    String myClass;

    동물() {
        myClass = "동물";
    }

    void showMe() {
        System.out.println(myClass);
    }
}
```

- *포유류.java*
```java
public class 포유류 extends 동물 {
    포유류() {
        myClass = "포유류";
    }
}
```

- *조류.java*
```java
public class 조류 extends 동물 {
    포유류() {
        myClass = "조류";
    }
}
```

- *고래.java*
```java
public class 고래 extends 포유류 {
    고래() {
        myClass = "고래";
    }
}
```

- *박쥐.java*
```java
public class 박쥐 extends 포유류 {
    박쥐() {
        myClass = "박쥐";
    }
}
```

- *참새.java*
```java
public class 참새 extends 조류 {
    참새() {
        myClass = "참새";
    }
}
```

- *펭귄.java*
```java
public class 펭귄 extends 조류 {
    펭귄() {
        myClass = "펭귄";
    }
}
```

- *Driver01.java*
```java
public class Driver01 {
    public static void main(String[] args) {
        동물 animal = new 동물();
        포유류 mammalia = new 포유류();
        조류 bird = new 조류();
        고래 whale = new 고래();
        박쥐 bat = new 박쥐();
        참새 sparrow = new 참새();
        펭귄 penguin = new 펭귄();

        animal.showMe();
        mammalia.showMe();
        bird.showMe();
        whale.showMe();
        bat.showMe();
        sparrow.showMe();
        penguin.showMe();
    }
}
```

상위 클래스에서만 *showMe()* 메소드를 구현했지만 모든 하위 클래스의 객체에서 사용할 수 있다. 즉, 상속으로 인해 같은 메소드를 여러 번 구현할 필요 없이 재사용할 수 있다는 것이다.

- *Driver02.java*
```java
public class Driver02 {
    public static void main(String[] args) {
        동물 animal = new 동물();
        동물 mammalia = new 포유류();
        동물 bird = new 조류();
        동물 whale = new 고래();
        동물 bat = new 박쥐();
        동물 sparrow = new 참새();
        동물 penguin = new 펭귄();

        animal.showMe();
        mammalia.showMe();
        bird.showMe();
        whale.showMe();
        bat.showMe();
        sparrow.showMe();
        penguin.showMe();
    }
}
```

`하위 클래스 = 상위 클래스`이기 때문에 위의 코드와 직전 코드는 실행 결과가 같다. 

### 상속은 is a 관계를 만족해야 한다?
- 펭귄 is a 동물

*"펭귄은 한 마리 동물이다.* 번역도 되고 논리적으로도 맞는 말 같지만 펭귄과 동물 모두 클래스이다. 분류의 개념으로 표현하면,

- 하위 클래스 is a 상위 클래스

가 될 것이다. 하지만 **한 마리 동물**이나 **하나의 상위 클래스**라는 표현은 하나의 객체를 뜻한다. 삼단 논법에 의하면 *하위 클래스는 하나의 상위 객체다.* 라는 결론에 도달하여 모순이 생긴다. 결국 상속 관계는 is a 관계보다 `is a kind of` 관계로 표현하는 것이 맞다.

- 하위 클래스 is a kind of 상위 클래스
- 펭귄 is a kind of 조류 -> 펭귄은 조류의 한 분류이다.
- 펭귄 is a kind of 동물 -> 펭귄은 동물의 한 분류이다.
- 고래 is a kind of 동물 -> 고래는 동물의 한 분류이다.

### 다중 상속과 자바
그렇다면 자바는 왜 다중 상속을 지원하지 않을까? 저자는 인어공주 이야기를 예로 들며 그 이유를 설명하고 있다.

![image](https://user-images.githubusercontent.com/46131688/112177705-80b1e280-8c3c-11eb-96f6-b6b181a5c0d9.png)

(사진 출처 - [티스토리](https://not-to-be-reset.tistory.com/149))

위와 같은 경우 인어가 수영하려면 사람처럼 팔다리를 저어 수영해야 할까 아니면 물고기처럼 꼬리 지느러미로 헤엄쳐야 할까? 이러한 문제를 **다중 상속의 다이아몬드 문제**라고 한다. 결국 다중 상속은 득실 관계에서 실이 더 많아 자바는 과감히 다중 상속을 포기하고 인터페이스를 도입했다.

### 상속과 인터페이스
상속을 `is a kind of` 관계로 표현한 것처럼 인터페이스는 `is able to` 관계로 표현할 수 있다.

- 구현 클래스 is able to 인터페이스

상위 클래스는 하위 클래스에게 특성을 상속해주고, 인터페이스는 클래스가 *'무엇을 할 수 있다'* 라는 기능을 구현하도록 강제한다. **상위 클래스가 하위 클래스에게 물려줄 특성이 많을수록, 인터페이스가 구현을 강제할 메소드 개수가 적을수록 좋다.**

![image](https://user-images.githubusercontent.com/46131688/112178856-76dcaf00-8c3d-11eb-9881-db889f38819a.png)

(사진 출처 - [티스토리](https://not-to-be-reset.tistory.com/149))

위의 분류도를 참고하여 만든 인터페이스 예제를 살펴보자.

- *동물.java*
```java
public class 동물 {
    String myClass;

    동물() {
        myClass = "동물";
    }

    void showMe() {
        System.out.println(myClass);
    }
}
```

- *날수있는.java*
```java
public interface 날수있는 {
    void fly();
}
```

- *헤엄칠수있는.java*
```java
public interface 헤엄칠수있는 {
    void swim();
}
```

- *포유류.java*
```java
public class 포유류 extends 동물 {
    포유류() {
        myClass = "포유류";
    }
}
```

- *조류.java*
```java
public class 조류 extends 동물 {
    포유류() {
        myClass = "조류";
    }
}
```

- *고래.java*
```java
public class 고래 extends 포유류 implements 헤엄칠수있는 {
    고래() {
        myClass = "고래";
    }

    @Override
    public void swim() {
        System.out.println(myClass + " 수영 중. 어프!! 어프!!");
    }
}
```

- *박쥐.java*
```java
public class 박쥐 extends 포유류 implements 날수있는 {
    박쥐() {
        myClass = "박쥐";
    }

    @Override
    public void fly() {
        System.out.println(myClass + " 나는 중. 슈웅!! 슈웅!!");
    }
}
```

- *참새.java*
```java
public class 참새 extends 조류 implements 날수있는 {
    참새() {
        myClass = "참새";
    }

    @Override
    public void fly() {
        System.out.println(myClass + " 나는 중. 허우적!! 허우적!!");
    }
}
```

- *펭귄.java*
```java
public class 펭귄 extends 조류 implements 헤엄칠수있는 {
    펭귄() {
        myClass = "펭귄";
    }

    @Override
    public void swim() {
        System.out.println(myClass + " 수영 중. 푸드덕!! 푸드덕!!");
    }
}
```

- *Driver.java*
```java
public class Driver {
    public static void main(String[] args) {
        날수있는 날라리1 = new 박쥐();
        날라리1.fly();

        날수있는 날라리2 = new 참새();
        날라리2.fly();

        헤엄칠수있는[] 맥주병들 = new 헤엄칠수있는[2];
        맥주병들[0] = new 고래();
        맥주병들[1] = new 펭귄();

        for (헤엄칠수있는 맥주병 : 맥주병들) {
            맥주병.swim();
        }
    }
}
```

실행 결과는 아래와 같다.

```java
// 박쥐 나는 중. 슈웅!! 슈웅!!
// 참새 나는 중. 허우적!! 허우적!!
// 고래 수영 중. 어프!! 어프!!
// 펭귄 수영 중. 푸드덕!! 푸드덕!!
```

### 상속과 T 메모리
```java
public class Animal {
    public String name;

    public void showName() {
        System.out.println("안녕 나는 %s야. 반가워\n", name);
    }
}

public class Penguin extends Animal {
    public String habitat;

    public void showHabitat() {
        System.out.println("%s는 %s에 살아\n", name, habitat);
    }
}

public class Driver {
    public static void main(String[] args) {
        Penguin pororo = new Penguin();
        pororo.name = "뽀로로";
        pororo.habitat = "남극";

        pororo.showName();
        pororo.showHabitat();

        Animal pingu = new Penguin();
        pingu.name = "핑구";
        // pingu.habitat = "EBS";

        pingu.showName();
        // pingu.showHabitat();
    }
}
```

1. main() 메소드 시작 전 `java.lang` 패키지와 모든 클래스들이 **스태틱 영역**에 배치된다. (JVM의 전처리 과정) 
2. `Penguin pororo = new Penguin();`
    - Penguin 클래스의 인스턴스뿐만 아니라 Animal 클래스의 인스턴스도 함께 **힙 영역**에 생겼다.
    - **하위 클래스의 인스턴스가 생성될 때 상위 클래스의 인스턴스도 함께 생성된다**
    - 그림에서 생략됐지만 모든 클래스의 최상위 클래스인 *Object* 클래스의 인스턴스도 함께 생성된다.

![image](https://user-images.githubusercontent.com/46131688/112181992-40546380-8c40-11eb-84ed-d81b85359ad4.png)

(사진 출처 - [티스토리](https://slenderankle.tistory.com/archive/20150630))

3. `Animal pingu = new Penguin();`
    - pororo와는 다르게 pingu 객체 참조 변수는 Animal 인스턴스를 바로 가리킨다.
    - 따라서 주석으로 처리한 *habitat* 속성과 *showHabitat()* 메소드를 사용할 수 없다.


---

#### 참고
- [스프링 입문을 위한 자바 객체 지향의 원리와 이해](http://www.yes24.com/Product/Goods/17350624)
- https://not-to-be-reset.tistory.com
