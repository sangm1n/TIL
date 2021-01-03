> 인프런 강의 - **스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술** 참조

## 스프링 웹 개발
스프링을 이용한 웹 개발은 크게 세 가지로 나뉜다.

1. [정적 컨텐츠](https://github.com/sangm1n/TIL/blob/main/Spring/20210103_static-contents.md)
2. MVC와 템플릿 엔진
3. [API](https://github.com/sangm1n/TIL/blob/main/Spring/20210103_api.md)

### MVC와 템플릿 엔진
템플릿 엔진을 이용하면 정적 컨텐츠와 달리 서버에서 컨텐츠를 동적으로 조금 수정할 수 있다.

`MVC`는 Model, View, Controller의 약자로 디자인패턴 중 하나이다. 사용자가 컨트롤러를 조작하면 컨트롤러는 모델을 통해 데이터를 가져오고, 그 정보를 뷰를 통해 사용자에게 보여준다고 생각하면 된다.

- Model
    - 백그라운드에서 동작하는 로직 처리
    - **일반적으로 데이터베이스 테이블과 대응**
- View
    - 사용자가 보게 될 결과 화면
    - html / css / javascript 등을 모아둔 컨테이너
- Controller
    - 모델과 뷰를 이어주는 역할
    - 사용자의 요청사항을 파악해 해당 요청사항에 맞는 데이터를 모델로부터 전달받아 뷰에 반영

![image](https://user-images.githubusercontent.com/46131688/103477980-19d41880-4e07-11eb-99d4-aa4c8aadc7ed.png)

따라서 스프링 컨테이너 내에 요청받은 자원에 대한 컨트롤러가 존재하면 `viewResolver`가 자체적으로 HTML 파일로 변환해 응답한다.

```java
@Controller
public class HelloController {
    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model) {
        ...
    }
}
```
