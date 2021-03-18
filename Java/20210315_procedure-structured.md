> *"스프링 입문을 위한 자바 객체 지향의 원리와 이해"* 를 읽고 정리한 내용입니다.

## 자바 프로그램의 개발과 구동
자바 개발 환경을 이해하려면 `JVM` (Java Virtual Machine)의 역할을 필수적으로 알아야 한다. 현실 세계에 빗대어 자바 세계를 표현하면 아래와 같다.

| 현실 세계 | 자바 세계 |
| -- | -- |
| 소프트웨어 개발 도구 | **JDK** (자바 개발 도구) |
| 운영체제 | **JRE** (자바 실행 환경) |
| 하드웨어 (컴퓨터) | **JVM** (자바 기상 기계) |

자바에서는 JDK를 이용해 개발된 프로그램이 JRE에 의해 가상 컴퓨터인 JVM 상에서 구동된다. JDK는 자바 소스 컴파일러인 *javac.exe* 를, JRE는 자바 프로그램 실행기인 *java.exe* 를 포함하고 있다. 

![image](https://user-images.githubusercontent.com/46131688/110326702-5c5edf00-805c-11eb-8cd9-e6f5267cafcf.png)

(사진 출처 - [블로그](https://kunhee.kim/programming/basic-java-0/))

이러한 구조 덕분에 자바의 대표적인 특징을 **Write Once Run Anywhere**라고도 한다. 즉 Windows, MacOS, Linux 등 특정 OS에 종속되지 않고 각 플랫폼에 맞는 JVM이 알아서 실행해준다.

![image](https://user-images.githubusercontent.com/46131688/110327285-1eae8600-805d-11eb-8365-4cbfd4e036f8.png)

(사진 출처 - [티스토리](https://siyoon210.tistory.com/124))

자바를 비롯한 언어들이 어떻게 메모리를 사용하는지에 대해 알아보자. 모든 프로그래밍 언어들은 메모리를 **코드 실행 영역**과 **데이터 저장 영역**으로 나누고, 객체 지향 프로그램에서는 데이터 저장 영역을 다시 세 가지로 나눈다.

- Static 영역 : 클래스들의 놀이터
- Stack 영역 : 메소드들의 놀이터
- Heap 영역 : 객체들의 놀이터

## 다시 보는 main() 메소드: 메소드 스택 프레임
`main()` 메소드는 프로그램의 시작점이다. 아래 코드가 실행될 때 메모리가 어떻게 변화하는지 순서대로 살펴보자.

```java
public class Study {
    public static void main(String[] args) {
        System.out.println("Hello wolrd !");
    }
}
```

1. JRE는 Example 클래스에서 main() 메소드를 발견하고, 가상 기계인 JVM에 전원을 넣어 부팅한다.
2. 부팅된 JVM은 목적 파일을 받아 실행시키는데, 가장 먼저 스태틱 영역에 `java.lang` 패키지를 갖다 놓는다.
3. 그 후 모든 클래스와 *import* 한 패키지 역시 스태틱 영역에 갖다 놓는다. 

![image](https://user-images.githubusercontent.com/46131688/110328884-40a90800-805f-11eb-97e0-b2e0a94a7aed.png)

(사진 출처 - [벨로그](https://velog.io/@gaya309/TIL-%EB%8B%A4%EC%8B%9C-%EB%B3%B4%EB%8A%94-main-%EB%A9%94%EC%84%9C%EB%93%9C-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%8A%A4%ED%83%9D-%ED%94%84%EB%A0%88%EC%9E%84))

여기까지가 JVM이 맨 먼저 하는 일, 즉 **전처리 과정**이다.

4. 위 코드의 2번째 줄이 실행될 때 비로소 스택 영역에 main() 메소드를 위한 **스택 프레임**이 할당된다. 그러면서 *args* 인자를 저장할 변수 공간을 스택 프레임 가장 아래에 확보해둔다. 여는 중괄호를 만날 대마다 스택 프레임이 하나씩 생긴다고 보면 된다. (클래스 제외)
5. `System.out.println()` 구문이 실행되어 화면에 출력되고,
6. main() 메소드의 끝을 알리는 닫힌 중괄호를 만나면 스택 프레임이 소멸된다.
7. main() 메소드는 프로그램의 시작점이기도 하면서 끝을 알리기도 한다. 따라서 JRE는 부팅시킨 JVM을 다시 종료하고, JRE 자체도 메모리에서 사라진다.

## 변수와 메모리: 변수! 너 어디 있니?
그렇다면 main() 메소드 내에 있는 변수들은 메모리 어느 부분에 쌓이게 되는 걸까?

```java
public class Study2 {
    public static void main(String[] args) {
        int i;
        i = 10;
        double d = 20.0;
    }
}
```

앞서 1 ~ 4까지의 과정이 끝나고 double 형 변수 *d* 까지 초기화 됐다면 스태틱 영역이 아래와 같이 구성된다.

![image](https://user-images.githubusercontent.com/46131688/110330148-c9747380-8060-11eb-9128-3dbfb5dd2b99.png)

(사진 출처 - [벨로그](https://velog.io/@gaya309/TIL-%EB%B3%80%EC%88%98%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%B3%80%EC%88%98-%EB%84%88-%EC%96%B4%EB%94%94-%EC%9E%88%EB%8B%88))

먼저 생성된 변수 순서대로 스택 프레임에 아래부터 쌓이게 된다. 참고로 변수 *i* 를 선언만 하고 초기화시키지 않으면 쓰레기값이 들어가 있다.

## 블록 구문과 메모리: 블록 스택 프레임
if문이 실행될 때는 메모리가 어떻게 변할까?

```java
public class Study3 {
    public static void main(String[] args) {
        int i = 10;
        int k = 20;

        if (i == 10) {
            int m = k + 5;
            k = m;
        } else {
            int p = k + 10;
            k = p;
        }
        // k = m + p;
    }
}
```

if문은 main() 메소드 안에 있으므로 main() 메소드가 실행될 때 만들어진 스택 프레임 안에 *if(true)* 의 스택 프레임이 중첩되어 생긴다. if문이 끝나면 해당 스택 프레임이 사라지고 else문은 그냥 지나친다. 그래서 주석 처리된 부분을 주석 해제해서 실행시키면 컴파일러 경고가 뜬다.

- *m cannot be resolved to a variable*
- *p cannot be resolved to a variable*

변수 *m* 은 if문 안에 있고, if문은 잘 실행됐는데 왜 m이라는 변수를 찾을 수 없는지 궁금해할 수도 있다. 이유는 굉장히 간단하다. if문이 끝나면서 스택 프레임이 사라졌기 때문에 메모리 그 어느 곳에도 변수 *m* 은 존재하지 않기 때문이다.

## 지역 변수와 메모리: 스택 프레임에 갇혔어요!
변수는 데이터 저장 영역 세 곳 중 어디에든 있을 수 있다.

- Static 영역 : **클래스 멤버 변수**, JVM이 종료될 때까지 존재한다.
- Stack 영역 : **지역 변수**, 스택 프레임이 사라지면 함께 사라진다.
- Heap 영역 : **객체 멤버 변수**, 객체와 함께 가비지 컬렉터에 의해 사라진다.

위의 코드를 다시 떠올려 보자. 변수 *m* 은 if문이 끝나고 찾을 수 없었지만 main() 메소드에서 초기화시킨 변수 *k* 는 if문 안에서 접근이 가능했다. **"외부 스택 프레임에서 내부 스택 프레임의 변수에 접근하는 것은 불가능하나 그 역은 가능하다."** 쉽게 말해 if문에서 변수 *k* 에 접근할 수 있었던 이유는 main() 메소드 안에 if문의 스택 프레임이 존재했기 때문이다.

## 메소드 호출과 메모리: 메소드 스택 프레임2
```java
public class Study4 {
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

*square()* 메소드가 호출되면 main() 스택 프레임에 중첩되지 않고 별도의 square() 스택 프레임이 생성된다.

![image](https://user-images.githubusercontent.com/46131688/110332241-718b3c00-8063-11eb-9897-7f653b919822.png)

(사진 출처 - [벨로그](https://velog.io/@gaya309/TIL-%EB%A9%94%EC%84%9C%EB%93%9C-%ED%98%B8%EC%B6%9C%EA%B3%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%8A%A4%ED%83%9D-%ED%94%84%EB%A0%88%EC%9E%842))

그렇다면 여기서도 main()에서 square()에 있는, 혹은 그 반대 경우에 있는 지역 변수에 접근할 수 있을까? 그에 대한 답은 **NO**다. **메소드를 블랙박스화한다**라는 말이 있다. 입력값이나 반환값에 의해서만 메소드 간의 값이 전달될 뿐 서로 내부 지역 변수를 절대 볼 수 없다는 뜻이다.  
메소드를 호출하면서 변수를 인자로 전달하는 것 같지만, 실제로는 변수가 저장한 **값**만을 복제해서 전달한다. 이러한 전달 방식을 값에 의한 전달, `call by value`라고 한다.

## 전역 변수와 메모리: 전역 변수 쓰지 말라니까요!
메소드 사이 값을 공유하기 위해서는 `static` 변수를 이용하면 된다.

```java
public class Study5 {
    static int share;

    public static void main(String[] args) {
        share = 55;
        int k = fun(5, 7);

        System.out.println(share);
    }

    private static int fun(int m, int p) {
        share = m + p;

        return m - p;
    }
}
```

*share* 변수는 앞에 static 키워드를 붙여줬기 때문에 스택 영역이 아닌 스태틱 영역에 만들어진다. 그렇기 때문에 출력문의 결과는 12이다. 이로써 **전역 변수는 스택 프레임에 독립적이고, 지역 변수는 스택 프레임에 종속적**이라는 것을 알 수 있다.  
하지만 실무에서는 전역 변수의 사용을 지양한다. 규모가 큰 프로젝트일수록 코드 양이 방대할 것이고, 여러 메소드에서 전역 변수의 값을 변경한다면 저장되어 있는 값을 쉽게 파악하기 힘들기 때문이다. 저자도 "피할 수 있다면 즐기지 말고 무조건 피해야 한다"고 서술하고 있다.

## 멀티 스레드 / 멀티 프로세스의 이해
멀티 스레드 (Multi Thread)는 데이터 저장 영역 중 **스택 영역을 스레드 개수만큼 분할해서 사용**한다. 

![image](https://user-images.githubusercontent.com/46131688/110333817-689b6a00-8065-11eb-9b70-9ad95e3151a7.png)

(사진 출처 - [블로그](https://juneyr.dev/java-things))

- 하나의 메모리 안에서 스택 영역만 분할한 것이기 때문에 스태틱 영역과 힙 영역은 공유해서 사용한다.
- 멀티 프로세스 대비 메모리를 적게 사용한다.
- **전역 변수의 사용으로 스레드 안정성이 깨진다.**

멀티 프로세스 (Multi Process)는 **다수의 데이터 저장 영역을 갖는 구조**이다.

![image](https://user-images.githubusercontent.com/46131688/110333873-77821c80-8065-11eb-9202-ae2f661ee2c2.png)

(사진 출처 - [블로그](https://juneyr.dev/java-things))

- 각 프로세스마다 각자의 메모리 공간을 사용하기 때문에 서로 참조할 수 없다.
- 멀티 스레드에 비해 많은 메모리를 사용한다.

---

#### 참고
- [스프링 입문을 위한 자바 객체 지향의 원리와 이해](http://www.yes24.com/Product/Goods/17350624)
- https://velog.io/@gaya309

