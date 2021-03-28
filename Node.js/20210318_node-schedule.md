특정 시간이나 주기로 작업을 처리해야 하는 경우에 리눅스의 `Cron`을 사용한다. 핸드폰으로 알람을 설정하는 경우를 떠올리면 될 것 같다. 매일 오전 8시에 알람을 설정해두면, 해당 시간마다 *알람이 울리는 이벤트* 를 발생시킨다.

## Cron
Cron에 대해서 조금 더 알아보자. [위키백과](https://ko.wikipedia.org/wiki/Cron)에 따르면 Cron은 **유닉스 계열 컴퓨터 운영 체제의 시간 기반 스케줄러**이다. 소프트웨어 환경을 설정하고 관리하는 사람들은 고정된 시간, 날짜, 간격에 주기적인 스케줄링 처리를 하기 위해 Cron을 사용한다. 

### Cron 표현식
Cron 표현식은 6개의 단위 표현식으로 이루어진 문자열이다.

```sh
┌───────────── second (0 - 59)
│ ┌───────────── min (0 - 59)
│ │ ┌────────────── hour (0 - 23)
│ │ │ ┌─────────────── day of month (1 - 31)
│ │ │ │ ┌──────────────── month (1 - 12)
│ │ │ │ │ ┌───────────────── day of week (0 - 6)
│ │ │ │ │ │
│ │ │ │ │ │
* * * * * * 
```

1. 초 (Seconds) : 0 ~ 59
2. 분 (Minutes) : 0 ~ 59
3. 시 (Hours) : 0 ~ 23
4. 일 (Day) : 1 ~ 31
5. 월 (Month) : 1 ~ 12 or JAN ~ DEC
6. 요일 (Week) : 0 ~ 6 or SUN ~ SAT

각 자리마다 특수문자와 숫자를 조합해서 사용할 수 있는데, 사용하는 특수문자의 의미는 아래와 같다.

- '*' : 모든 값
- '?' : 특정한 값이 없음
- '-' : 범위
    - *ex) MON-FRI*
- ',' : 특정한 값일 때만 동작
    - *ex) MON, WED, FRI*
- '/' : 시작시간 / 단위
    - *ex) 0분부터 매 30분인 경우, 0/30*
- 'L' : 마지막 일 또는 마지막 요일
- 'W' : 가장 가까운 평일
    - *ex) 10W는 10일에서 가장 가까운 평일 (MON ~ FRI)*
- '#' : 요일#주차
    - *ex) 3#2, 둘째 주 수요일*

## node-schedule
node에서는 `node-cron`이나 `node-schedule` 모듈을 이용하여 스케줄러를 구현할 수 있다. 이번에 외주를 하면서 스케줄러를 통한 **푸쉬 알림**을 구현해야 했다. 그래서 해당 모듈을 어떻게 사용하는지에 대해 알아봤는데, 위에서 소개한 Cron 표현식을 이용하면 굉장히 쉽게 구현할 수 있었다.

### install
```sh
$ npm install node-schedule
```

### example
`scheduleJob` 메소드에 첫 번째 인자로 *시간*, 두 번째 인자로 *콜백함수* 를 넣어주면 된다.
#### 5분마다 실행
```js
const schedule = require('node-schedule');

const job = schedule.scheduleJob('0 0/5 * * * *', fucntion() {
    console.log(new Date());
});
```

#### 월, 수, 금 18시 30분마다 실행
```js
const schedule = require('node-schedule');

const job = schedule.scheduleJob('0 30 16 * * MON,WED,FRI', fucntion() {
    console.log(new Date());
});
```

#### 평일 오전 10시에 실행
```js
const schedule = require('node-schedule');

const job = schedule.scheduleJob('0 0 10 * * MON-FRI', fucntion() {
    console.log(new Date());
});
```

---

#### 참고
- https://zamezzz.tistory.com/197
- https://velog.io/@filoscoder/%EC%8A%A4%EC%BC%80%EC%A4%84-%EC%97%85%EB%AC%B4-%EC%9E%90%EB%8F%99%ED%99%94-Node-cron-vs-Node-schedule-%EB%B9%84%EA%B5%90-clk4dyynve
