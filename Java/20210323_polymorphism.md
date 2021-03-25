> *"스프링 입문을 위한 자바 객체 지향의 원리와 이해"* 를 읽고 정리한 내용입니다.

## 객체 지향의 4대 특성 - 3) 다형성
### 다형성: 사용편의성
객체 지향에서 다형성은 오버라이딩과 오버로딩이라고 할 수 있다. 

### 오버라이딩? 오버로딩?
- 오버라이딩 (Overriding)
    - 같은 메소드 이름, 같은 인자 목록으로 상위 클래스의 메소드를 **재정의**
- 오버로딩 (Overloading)
    - 같은 메소드 이름, 다른 인자 목록으로 다수의 메소드를 **중복 정의**

- *Animal.java*
```java
public class Animal {
    public String name;

    public void showName() {
        System.out.println("안녕 나는 %s야. 반가워\n", name);
    }
}
```

- *Penguin.java*
```java
public class Penguin extends Animal {
    public String habitat;

    public void showHabitat() {
        System.out.println("%s는 %s에 살아\n", name, habitat);
    }

    // 오버라이딩
    public void showName() {
        System.out.println("어머 내 이름은 알아서 뭐하게요?");
    }

    // 오버로딩
    public void showName(String yourName) {
        System.out.println("%s 안녕, 나는 %s라고 해\n", yourName, name);
    }
}
```

- *Driver.java*
```java
public class Driver {
    public static void main(String[] args) {
        Penguin pororo = new Penguin();
        pororo.name = "뽀로로";
        pororo.habitat = "남극";

        pororo.showName();
        pororo.showName("초보람보");
        pororo.showHabitat();

        Animal pingu = new Penguin();
        pingu.name = "핑구";

        pingu.showName();
    }
}
```

실행 결과는 아래와 같다.

```java
// 어머 내 이름은 알아서 뭐하게요?
// 초보람보 안녕, 나는 뽀로로라고 해
// 뽀로로는 남극에 살아
// 어머 내 이름은 알아서 뭐하게요?
```

### 다형성과 T 메모리
1. main() 메소드 시작 전 `java.lang` 패키지와 모든 클래스들이 **스태틱 영역**에 배치된다. (JVM의 전처리 과정) 
2. `Penguin pororo = new Penguin();`
    - Penguin 클래스가 상위 클래스인 Animal 클래스의 showName() 메소드를 오버라이딩, showName(yourName: String) 메소드를 오버로딩했다.

![image](https://user-images.githubusercontent.com/46131688/112186496-86abc180-8c44-11eb-8439-0745816ecc16.png)

(사진 출처 - [벨로그](https://velog.io/@gaya309/TIL-%EB%8B%A4%ED%98%95%EC%84%B1-%EC%82%AC%EC%9A%A9%ED%8E%B8%EC%9D%98%EC%84%B1))

3. *pororo.showName();*
    - Animal 객체에 있는 showName() 메소드는 Penguin 객체에 있는 showName() 메소드에 의해 재정의, 즉 가려졌다.
    - 따라서 Penguin 객체에 있는 showName() 메소드가 호출된다.

4. *pororo.showName("초보람보");*
    - 문자열을 인자로 받는 showName(yourName) 메소드가 호출된다.

5. `Animal pingu = new Penguin();`
    - pingu 객체 참조 변수는 Animal 타입이다.

6. *pingu.showName();*
    - **상위 클래스 타입의 객체 참조 변수를 사용하더라도 하위 클래스에서 오버라이딩한 메소드가 호출된다.**


---

#### 참고
- [스프링 입문을 위한 자바 객체 지향의 원리와 이해](http://www.yes24.com/Product/Goods/17350624)
- https://velog.io/@gaya309
