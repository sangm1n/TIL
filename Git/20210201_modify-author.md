gitlab과 github 계정을 따로 쓰는 바람에 gitlab에 커밋한 내용을 미러링해서 깃허브로 가져와도 *contribution* 반영이 되지 않았다. 찾아보니 `rebase`를 이용하면 문제를 해결할 수 있다고 한다.

## 상황
![image](https://user-images.githubusercontent.com/46131688/106391109-ffee1b80-642e-11eb-8634-62ec63f2b05c.png)

소스트리에서 살펴본 상황은 이랬고, 계정 이메일을 모두 `naver.com`으로 바꾸는 것이 목표였다.

### step 1
- *rebase*

```sh
git rebase -i -p COMMIT_HASH
```

사진 맨 오른쪽에 있는 값들이 `commit id`이다. 깃은 가장 최근에 커밋된 번호를 확인하지 않고 바로 커밋하기 위해 이처럼 `hash` 값을 사용한다.

![image](https://user-images.githubusercontent.com/46131688/106391191-78ed7300-642f-11eb-8f8b-cdc24dffcc9c.png)

여튼 우리는 **수정하고 싶은 커밋의 바로 직전 hash 값**을 알아야 한다. 애초에 첫 커밋부터 다른 계정으로 설정되었기 때문에 가장 처음 커밋한 값을 가져왔다.

위 사진에서 맨 밑에 있는 *db73db7* 을 값으로 입력했다면 그 다음 커밋인 *7ec683c* 부터 rebase 대상으로 지정된다.

### step 2
- *pick* -> *edit*

vi 편집기가 열릴텐데 **i**를 눌러 입력 모드로 들어가 변경하고자 하는 커밋을 `edit`으로 바꿔주면 된다.

![image](https://user-images.githubusercontent.com/46131688/106391415-8fe09500-6430-11eb-96e2-e89d7471f393.png)

`:wq`을 입력하면 저장하고 vi 편집기를 빠져나온다.

### step 3
- *사용자 계정 변경*

```sh
git commit --amend --author="USER_NAME <USER_EMAIL>"
```

### step 4
- *continue*

```sh
git rebase --continue
```

rebase가 끝날 때까지 3~4 과정을 계속 반복해주면 된다. 

### step 5
- **push**

```sh
git push origin main
```

> Updates were rejected because the tip of your current branch is behind its remote

여기서 위와 같은 오류가 떴었는데 브랜치 앞에 **+**를 붙여 강제로 push 해줬다.


## 결과
![image](https://user-images.githubusercontent.com/46131688/106391524-1e551680-6431-11eb-93e2-a86964484349.png)

약 40여 개를 일일히 명령어 쳐주면서 rebase 하느라 꽤 시간이 걸렸다. 이전까지의 기록은 그냥 냅두고 `git config user.email`로 계정을 바꿀수도 있지만 커밋 히스토리를 봤을 때 계정이 뒤죽박죽이라 통일성이 없어보였다랄까...  

한가지 아쉬운 점은 push한 날짜가 기록된다는 점이다. ~~(내 히스토리)~~

---

#### 참고
- https://jojoldu.tistory.com/120