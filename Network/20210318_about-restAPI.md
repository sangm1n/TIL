---
title: ［서버 / Server］ RESTful한 API 설계하기
tags: ['Server', 'API', 'RestAPI']
categories: ['Server']
date: 2020-10-18
---

[이전 포스팅](https://sangminlog.tistory.com/entry/what-is-api)에서 `API`가 무엇인지 간략하게 알아봤다. 소프트 스퀘어드 4주차 과제는 이러한 API를 `REST`하게 설계하는, 즉 `RESTful API`를 만드는 것이다. 설계에 앞서 *REST API* 가 무엇인지 제대로 알고 싶어 정리했다. 

## REST API
*REpresentational State Transfer* 의 약자로 2000년도에 *Roy Fielding* 박사 논문에서 최초로 소개되었다. 로이 필딩은 HTTP 주요 저자 중 한 사람으로 웹의 장점을 최대한 활용할 수 있는 아키텍처로써 REST를 발표했다고 한다. 
### REST 구성
- 자원(Resource) : `URI`
- 행위(Verb) : `HTTP Method`
- 표현(Representations)

쉽게 말해 REST는 URI를 통해 자원을 표시하고, HTTP Method를 이용해 자원의 행위를 규정하여 그 결과를 받는 것을 말한다. 
#### HTTP Method
HTTP Method는 GET / POST / PUT / DELETE가 대표적이며 `CRUD`에서 주로 사용한다. CRUD는 Create / Read / Update / Delete를 묶어서 부르는 말이다.
- *GET* : 조회 (**Read**)
- *POST* : 생성 (**Create**)
- *PUT* : 수정 (**Update**)
- *DELETE* : 삭제 (**Delete**)

PUT과 PATCH의 차이점은 [링크](https://devuna.tistory.com/77?category=939368)에 자세히 나와있다.
### REST 특징
1. **Uniform Interface** 
    - URI로 지정한 리소스에 대한 조작을 통일되고 한정적인 인터페이스로 수행한다.
2. **Stateless** 
    - 무상태성, 즉 작업을 위한 상태 정보를 따로 저장하고 관리하지 않는다.
    - 세션이나 쿠키 정보를 별도로 저장하고 관리하지 않으므로 서버는 들어오는 요청만을 처리한다.
3. **Cacheable**
    - 캐시 사용을 통해 응답시간이 빨라지고 서버의 자원 이용률을 향상시킬 수 있다.
4. **Self-descriptiveness**
    - REST API 메시지만 보고도 이를 쉽게 이해할 수 있도록 자체 표현 구조로 되어 있다.
5. **Client-Server**
    - 자원 있는 쪽이 `server`, 자원을 요청하는 쪽이 `client`가 된다.
    - 각각의 역할이 확실히 구분되어 의존성이 줄어든다.
6. **Layered System**
    - REST 서버는 다중 계층으로 구성될 수 있으며 프록시나 게이트웨이처럼 네트워크 기반의 중간매체를 사용할 수 있게 한다.

## REST API 설계
REST API 설계 시 가장 중요한 2가지 항목은 아래와 같다.
- **URI는 정보의 자원을 표현해야 한다.**
- **자원에 대한 행위는 HTTP Method로 표현한다.**

### URI Rules
`URI`는 *Uniform Resource Identifier* 의 약자로 자원을 나타내는 유일한 주소이다.
#### Rule 1. 슬래시 구분자(/)는 계층 관계를 나타내는 데 사용한다.
```sh
/blog/sangminlog
```
#### Rule 2. URI 마지막 문자로 슬래시 구분자(/)를 사용하지 않는다.
```sh
/blog/sangminlog/  (X)
/blog/sangminlog   (O)
```
#### Rule 3. 가독성을 높이기 위해서는 _(언더바)가 아닌 -(하이픈)을 사용한다.
```sh
/blog/sangminlog_logo  (X)
/blog/sangminlog-logo  (O)
```
#### Rule 4. URI 경로에는 소문자가 적절하다.
```sh
/Blog/Sangminlog    (X)
/blog/sanmginlog    (O)
```
#### Rule 5. 파일 확장자는 URI에 포함하지 않는다.
`accept header`를 사용한다.
```sh
http://example.com/blog/sangminlog/logo.jpg  (X)
GET /blog/sangminlog/logo
HTTP/1.1 Host: example.com Accept: image/jpg (O)
```
#### Rule 6. 기본적으로 명사를 사용하지만, 컨트롤 자원을 의미하는 경우 동사를 허용한다.
```sh
/blog/sangminlog/duplicating  (X)
/blog/sangminlog/duplicate    (O)
```
#### Rule 7. 리소스 간 연관 관계가 있는 경우 아래와 같이 처리한다.
/리소스명/리소스ID/관계가 있는 다른 리소스명
```sh
/users/{userid}/devices   (일반적으로 소유 'has'의 관계를 표현할 때)
```

## HTTP 응답 상태 코드
REST API는 URI 설계하는 것에 그치지 않고 그 리소스에 대한 응답 또한 잘 내어주어야 한다.
### 2XX Success
- `200` : 클라이언트의 요청을 정상적으로 수행한 경우
- `201` : 클라이언트가 어떠한 리소스 생성을 요청, 해당 리소스가 성공적으로 생성된 경우 (POST)
### 4XX Client Errors
- `400` : 클라이언트의 요청이 부적절할 경우
- `401` : 클라이언트가 인증되지 않은 상태에서 보호된 리소스를 요청한 경우
- `404` : 클라이언트가 요청한 자원이 존재하지 않는 경우
- `405` : 클라이언트의 요청이 허용되지 않는 메소드인 경우
### 5XX Server Errors
서버 에러는 절대 사용자에게 나타내서는 안된다.
- `500` : 서버에 문제가 있을 경우

더 자세한 상태 코드는 [링크](https://sanghaklee.tistory.com/61)에 나와 있다.

이렇게 REST하게 작성한 API를 `RESTful`하다고 표현할 뿐이지 따로 규칙이 있는 것은 아니다. 단지 이해하기 쉽고 사용하기 쉬운 REST API를 만드는 것이 RESTful의 목적이다. 그렇기 때문에 성능이 중요한 상황에서는 굳이 RESTful한 API를 구현할 필요는 없다고 한다.

---

#### 참고
- https://sanghaklee.tistory.com/57
- https://meetup.toast.com/posts/92
- https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html