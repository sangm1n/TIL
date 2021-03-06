> 서적 - **이것이 취업을 위한 코딩테스트다** 참조

코딩 테스트에서 어떤 문제를 풀든 코드를 작성하는 과정은 필수기 때문에 **구현**은 모든 문제 유형을 포함하는 개념이라고 볼 수 있다.

## Implementation Algorithm
특히 *풀이를 떠올리는 것은 쉽지만 소스코드로 옮기기 어려운 문제* 를 구현 유형의 문제라고 본다. 알고리즘은 간단한데 코드가 굉장히 길어지는 문제, 문자열을 입력받아 한 문자 단위로 끊어서 리스트에 넣어야 하는 문제 등이 까다로운 구현 유형의 문제라고 할 수 있다.  
저자는 이 책에서 **완전 탐색**과 **시뮬레이션** 두 개의 유형을 구현으로 묶었다. 
- 완전 탐색 : 모든 경우의 수를 다 계산하는 방법
- 시뮬레이션 : 문제에서 제시한 알고리즘을 한 단계식 차례대로 수행하는 방법

구현 알고리즘의 대표적인 예시인 상하좌우 문제를 살펴보자.

### 상하좌우
```sh
여행가 A는 N X N 크기의 정사각형 공간 위에 서 있다. 이 공간은 1 X 1 크기의 정사각형으로 나누어져 있다.
가장 왼쪽 위 좌표는 (1, 1)이며, 가장 오른쪽 아래 좌표는 (N, N)에 해당한다.
여행가 A는 상, 하, 좌, 우 방향으로 이동할 수 잇으며, 시작 좌표는 항상 (1, 1)이다.
계획서에는 하나의 줄에 띄어쓰기를 기준으로 하여 L, R, U, D 중 하나의 문자가 반복적으로 적혀 있다.
 - L : 왼쪽으로 한 칸 이동
 - R : 오른쪽으로 한 칸 이동
 - U : 위로 한 칸 이동
 - D : 아래로 한 칸 이동
이때 여행가 A가 N X N 크기의 정사각형 공간을 벗어나는 움직임은 무시된다.
계획서가 주어졌을 때 여행가 A가 최종적으로 도착할 지점의 좌표를 출력하는 프로그램을 작성하시오.
```

해당 문제는 일정한 명령에 따라 개체를 차례대로 이동시킨다는 점에서 시뮬레이션 유형으로 분류된다. 개인적으로 이러한 유형에 약한데 좌표를 잘 이용하는 것이 중요한 것 같다.

![](https://user-images.githubusercontent.com/46131688/100571190-e2f57900-3315-11eb-997e-eb85eeee6c4d.png)

