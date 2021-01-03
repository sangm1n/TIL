> 인프런 강의 - **스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술** 참조

## 스프링 웹 개발
스프링을 이용한 웹 개발은 크게 세 가지로 나뉜다.

1. 정적 컨텐츠
2. [MVC와 템플릿 엔진](https://github.com/sangm1n/TIL/blob/main/Spring/20210103_mvc-template.md)
3. [API](https://github.com/sangm1n/TIL/blob/main/Spring/20210103_api.md)

### 정적 컨텐츠
`HTML` 파일을 그대로 웹 브라우저에게 쏴주는 방식이다.

![image](https://user-images.githubusercontent.com/46131688/103477791-6159a500-4e05-11eb-91e8-a8eefa42fec9.png)

스프링 컨테이너는 스프링 부트 내 `injection`을 이용하여 객체를 관리하는 컨테이너다. 

클라이언트(웹 브라우저)가 요청한 내용과 관련된 `Controller`가 스프링 컨테이너에 존재하지 않는다면 톰캣 서버는 자체적으로 정적 컨텐츠를 찾아 반환해준다.

그래서 아래 도메인처럼 `html` 확장자가 따라 붙는다.
- https://localhost:8080/index.html
