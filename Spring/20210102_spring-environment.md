> 인프런 강의 - **스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술** 참조

## 프로젝트 환경설정
### 프로젝트 생성
[스프링부트 스타터 사이트](https://start.spring.io/)에서 쉽게 스프링 프로젝트 생성할 수 있다.

- Project
    - Maven : 설정 파일로 `xml` 사용, 쓰지 않는 추세
    - Gradle : `Groovy` 언어 사용, 훨씬 간결한 코드
- Language
    - Java
- Spring Boot
    - `2.4.1x`
- Project Metadata
    - Group : domain name
    - Artifact : project name
    - Packaging : Jar
    - Java : 11
- Dependency
    - 어떤 라이브러리를 가져와 쓸지

### 라이브러리
기존에 사용하기 어려웠던 스프링이 톰캣 웹 서버를 내장한 스프링 부트가 나오면서 굉장히 개발 친화적으로 변했다.

#### Spring boot library
- `spring-boot-starter-web`
    - spring-boot-starter-tomcat : 톰캣 웹서버
    - sprring-boot-webmvc : 스프링 웹 `MVC`
- `spring-boot-starter-thymeleaf` : 템플릿 엔진
- `spring-boot-starter` : 스프링 부트 + 스프링 코어 + 로깅
    - spring-boot
        - spring-core
    - spring-boot-starter-logging
        - logback
        - slf4j

#### Test library
- `spring-boot-starter-test`
    - junit : 테스트 프레임워크
    - mockito
    - assertj

### 디렉토리 구조
```
src
├── main
│   ├── java      // 실제 소스 파일
│   ├── resources // java 파일을 제외한 나머지 (XML, config 파일 등)
└── test
    └── java      // test code 관련 소스들
```