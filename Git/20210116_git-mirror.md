`gitlab`에서 작업한 프로젝트를 `github`에 끌어올 수 있는 방법이 있지 않을까 해서 찾아본 결과 **mirror**를 이용하면 된다고 한다. 커밋 히스토리까지 그대로 끌어오기 때문에 자주 사용할 것 같다.

## mirror
원래 있던 *ORIGIN_REPO* 레파지토리를 *NEW_REPO* 라는 신규 레파지토리로 가져오고 싶다고 가정해보자.

- https://github.com/ORIGIN_REPO.git
- https://github.com/NEW_REPO.git

간단하게 `mirror` 옵션을 이용해 클론한 후 푸쉬해주면 된다.

```sh
$ git clone --mirror https://github.com/ORIGIN_REPO.git
$ cd ORIGIN_REPO.git
$ git remote set-url --push origin https://github.com/NEW_REPO.git
$ git push --mirror
```
