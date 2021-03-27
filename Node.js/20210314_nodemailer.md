## nodemailer
로그인 시 비밀번호를 까먹어 임시 비밀번호를 발급받은 경험이 한 번쯤은 있을 것이다. node.js에서는 `nodemailer` 모듈을 통해 이메일을 쉽게 보낼 수 있다. 해당 모듈을 사용해 임시 비밀번호를 메일로 전송해주는 방법에 대해 알아보자.

### 설치
```sh
npm install nodemailer
```

### google 계정 보안 설정 변경
gmail을 통해 이메일을 보내게끔 했는데, 아래 두 설정을 해줘야 구글 계정에 정상적으로 접근하여 이메일을 보낼 수 있다.

- https://myaccount.google.com/lesssecureapps
    - 보안 수준이 낮은 앱 허용 : **사용**
- https://accounts.google.com/DisplayUnlockCaptcha
    - 내 Google 계정에 대한 액세스 **허용**

### 사용
- *config/email.js*

```js
const nodemailer = require('nodemailer');

const smtpTransport = nodemailer.createTransport({
    service: "Gmail",
    auth: {
        user: "USER@gmail.com",
        pass: "USER_PASSWORD"
    },
    tls: {
        rejectUnauthorized: false
    }
});

module.exports = smtpTransport;
```

`service`에는 Gmail뿐만 아니라 Naver, Hotmail 등 다양하게 가능하다.

- *router.js*

```js
router.post('/user/password', controller.findPassword);
```

- *controller.js*

```js
const smtpTransport = require('../config/email');

const findPassword = async function (req, res) {
    const emailOptions = {
        from: "USER@gmail.com",
        to: "보내고자 하는 이메일",
        subject: "이메일 제목",
        text: "이메일 본문"
    };

    await stmpTransport.sendMail(mailOptions, (err, response) => {
        if (err) {
            // 에러 처리
        } else {
            // 정상 동작
        }
        smtpTransport.close();
    })
};
```

로컬에서 정상 동작하는 것을 확인하고 AWS 서버에 올렸더니 `request timeout`이 떠서 한참동안 고생했다. 알고보니 인바운드 규칙에 25번 포트를 허용시켜주지 않아서 발생한 오류였다. **SMTP**의 기본 포트 번호가 25번이라는 것을 잊지말자 !
