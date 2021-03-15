예전에 프로젝트 할 때 시간이 부족해서 소셜 로그인을 다루지 못했었다. 이론은 알고 있었기에 직접 구현해보고 싶었는데 하지 못해서 많이 아쉬웠다.  
이번에 [소프트 스퀘어드](https://apply.softsquared.com/)에서 외주를 진행하며 다시 소셜 로그인을 다룰 수 있는 기회과 왔다. *Node.js* 를 통해 개발하고 있기 때문에 `passport`, `passport-kakao` 모듈을 어떻게 사용하는지 알아보자.

## 소셜 로그인 인증 시퀀스
기본적으로 소셜 로그인은 아래와 같은 방식으로 진행된다.

![image](https://user-images.githubusercontent.com/46131688/105577722-a2b6f200-5dbe-11eb-9d03-a4ee0513dc5d.png)

클라이언트가 카카오로부터 발급받은 `access token`을 서버에 전달하고, 서버는 그것을 다시 카카오에 검증 요청한다. 카카오 서버는 자기가 **클라이언트에게 부여한 토큰**과 **서버로부터 받은 토큰**이 같을 경우 사용자 정보를 내준다.  
이것이 우리가 흔히 알고 있는 소셜 로그인의 동작 방식이다. 이렇게 사용자 정보를 받은 서버는 데이터베이스에 저장하고, `JWT token`을 클라이언트에게 제공한다. JWT가 무엇인지는 예전에 작성한 [인증 관련 포스팅](https://sangminlog.tistory.com/entry/what-is-certification)에서 설명하고 있다.

### 토큰 발급
[Kakao Developers](https://developers.kakao.com)에서 애플리케이션을 등록한다.

- *내 애플리케이션* -> *애플리케이션 추가하기*

![image](https://user-images.githubusercontent.com/46131688/105577843-528c5f80-5dbf-11eb-8d36-d4b448fdc258.png)

- *플랫폼* -> *Web 플랫폼 등록*

![image](https://user-images.githubusercontent.com/46131688/105577872-a139f980-5dbf-11eb-83d4-fb962640cf74.png)

사이트 도메인을 등록하면 `Redirect URI`를 등록해야 한다고 나온다. 카카오 인증 후에 콜백받을 URI를 지정해주면 된다.

![image](https://user-images.githubusercontent.com/46131688/105577906-d6dee280-5dbf-11eb-90a7-acf5e198b22c.png)

마지막으로 그 위에 있는 활성화 상태를 **ON**으로 설정해준다.

![image](https://user-images.githubusercontent.com/46131688/105577933-fece4600-5dbf-11eb-8e3d-42dc34d3692e.png)

> 요약 정보에 있는 **REST API Key**를 메모해두자 !

### 모듈 적용
아래 명령어를 통해 모듈을 설치해준다.
```js
npm install passport
npm install passport-kakao
```

- *Controller*

```js
const passport = require('passport')
const KakaoStrategy = require('passport-kakao').Strategy

passport.use('kakao-login', new KakaoStrategy({
    clientID: '[REST API Key]',
    callbackURL: '[등록한 Redirect URI]',
}, async (accessToken, refreshToken, profile, done) => {
    console.log(accessToken);
    console.log(profile);
}));
```

- *Router*

```js
router.get('/kakao', passport.authenticate('kakao-login'));
router.get('/auth/kakao/callback', passport.authenticate('kakao-login', {
    failureRedirect: '/',
}), (req, res) => {
    res.redirect('/');
});
```

![image](https://user-images.githubusercontent.com/46131688/105601333-65f7f480-5dd8-11eb-8fab-ca19acfc0d91.png)

서버를 구동하고 로그인하면 아래 사진처럼 콘솔에 `access token`과 사용자 정보가 뜨는 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/46131688/105602099-9cce0a80-5dd8-11eb-9c19-7b6befcd43a4.png)
