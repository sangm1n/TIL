> *"KOCW - 반효경 교수님의 운영체제"* 를 듣고 정리한 내용입니다.

## Process Synchronization
### 데이터의 접근
컴퓨터 시스템에서 데이터 연산은 저장 공간과 실행 공간이 아래와 같은 시퀀스로 동작하면서 이루어진다. 

1. 저장 공간에 데이터가 있다.
2. 연산할 데이터를 실행 공간으로 가져온다.
3. 실행 공간에서 연산한다.
4. 연산 결과를 저장 공간에 반영한다.

추상적인 표현으로 저장 공간, 실행 공간이라는 말을 썼는데 실행 공간은 CPU나 프로세스, 컴퓨터 내부 등이 있고, 저장 공간은 메모리나 해당 프로세스의 주소 공간, 디스크 등이 있다.

### Race Condition
저장 공간을 공유하는 실행 공간이 여러 개 있는 경우 `race condition` 가능성이 있다. 예를 들어 메모리에 *count* 변수가 있고 서로 다른 CPU가 각각 증가 연산, 감소 연산을 한다고 가정해보자.

정상적인 동작을 한다면 CPU A가 count 변수를 가져와 1 증가시키고 다시 메모리에 반영하고, CPU B가 count 변수를 가져와 1 감소시키고 다시 메모리에 반영하여 count 변수 값의 변화가 없을 것이다. 하지만 CPU A에서 증가 연산을 하는 동안 CPU B가 메모리에 있는 count 변수를 가져가서 연산한다면 결과는 *count - 1* 이 저장될 것이다.

이처럼 공유하는 하나의 주체에 여러 주체가 마치 경쟁하듯 접근하려 하는 것을 경쟁 상태, 즉 **race condition**이라고 한다.

- 운영체제에서 race condition이 발생하는 상황
    1. kernel 수행 중 인터럽트 발생 시
    2. 프로세스가 시스템콜을 호출하여 kernel mode로 수행 중인 가운데 context switch가 일어나는 경우
    3. multi-processor에서 공유 메모리 내의 커널 데이터

### OS에서의 race condition
1. interrupt handler vs kernel

![image](https://user-images.githubusercontent.com/46131688/113478486-ff701080-94c3-11eb-9453-ec84a48907c6.png)

(사진 출처 - [KOCW 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323))

위에서 설명했던 예제와 같은 상황이다. kernel address space를 공유하기 때문에 증감 연산 후 *count* 값이 그대로 유지되는 것이 아니라 1 증가한다. 이와 같이 중요한 변수의 값을 건드리는 동안에는 **인터럽트가 발생해도 연산이 끝나고 수행될 수 있게끔 disable** 해준다.

2. preempt a process running in kernel ?

![image](https://user-images.githubusercontent.com/46131688/113478720-a1442d00-94c5-11eb-8e1b-4d0bf2922578.png)

(사진 출처 - [KOCW 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323))

프로세스 A가 시스템콜을 호출하여 kernel mode에서 작업을 처리하는 도중에 CPU 할당 시간이 끝나 *context switch* 가 발생했다. 이후 프로세스 B가 CPU를 할당받아 A와 동일하게 시스템콜을 호출했고, count 변수를 1 증가시켰다. 그리고 다시 context switch가 발생하여 프로세스 A가 CPU를 잡아 count 변수를 1 증가시켰다. 

count 변수 값이 2 증가해야 하는데 결과는 그렇지 않다. 프로세스 A는 프로세스 B가 증가 연산을 하기 전의 count 값을 갖고 있었고, CPU를 다시 할당받았을 때 그 시점의 context를 가지고 값을 증가시켰기 때문에 1만 증가한 것처럼 보인다.

이를 해결하기 위해 **커널 모드에서 수행 중일 때는 CPU 할당 시간이 끝나도 선점하지 않고** 다시 사용자 모드로 돌아갔을 때 선점한다.

3. multi-processor

![image](https://user-images.githubusercontent.com/46131688/113478751-cdf84480-94c5-11eb-8887-cbe8f1dd6f23.png)

(사진 출처 - [KOCW 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323))

CPU가 메모리에서 데이터를 가져오기 전에 `lock`을 걸어 다른 CPU가 같은 데이터에 접근하는 것을 막아준다. 연산이 끝난 후 데이터를 다시 메모리에 저장할 때 lock을 풀어 줌으로써 그제서야 다른 CPU에서 접근할 수 있게 해준다.

- 방법 1
    - 한 번에 하나의 CPU만 커널에 들어갈 수 있게 하는 방법
        - 커널 전체에 lock을 걸기 때문에 비효율적
- 방법 2
    - 커널 내부에 있는 각 공유 데이터에 접근할 때마다 해당 데이터에 대한 lock을 거는 방법

### Process Synchronization 문제
공유 데이터의 동시 접근은 데이터의 불일치 문제를 발생시킬 수 있다. 그래서 일관성을 유지하기 위해 협력 프로세스 간의 실행 순서를 정해주는 메커니즘이 필요하다.

### Critical Section
- 공유 데이터를 접근하는 코드
- Problem
    - 하나의 프로세스가 *critical section* 에 있을 때 다른 모든 프로세스는 *critical section* 에 들어갈 수 없어야 함

### Critical Section 문제를 해결하기 위한 충족 조건
- **Mutual Exclusion**
    - 어떤 프로세스가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section에 들어가면 안됨
- **Progress**
    - 아무도 critical section에 있지 않은 상태에서 들어가고자 하는 프로세스가 있으면 critical section에 들어가게 해주어야 함
- **Bounded Waiting**
    - 프로세스가 critical section에 들어가려고 요청한 후부터 해당 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수는 한계가 있어야 함
    - ex) 세 개의 프로세스가 있을 때 두 개의 프로세스만 번갈아가며 critical section에 들어가는 것은 *bounded waiting* 조건을 만족하지 못한 것

## Initial Attempts to Solve Problem
```c
do {
    entry section
    critical section
    exit section
    remainder section
} while (1);
```

### Algorithm 1
- Synchronization variable
```c
int turn;
initially turn = 0;   // 몇 번 프로세스가 들어갈 수 있는지를 알려주는 turn 변수
```
- Process *P<sub>0</sub>*
    - *turn* 변수가 0이 아닌 동안 while 문을 계속 돌면서 자기 차례를 기다린다.

```c
do {
    while (turn != 0);
    critical section
    turn = 1;
    remainder section
} while (1);
```

**mutual exclusion은 만족하나 progress는 만족하지 않는다.** 반드시 한 번씩 교대로 들어가야 한다. 만약 프로세스 0이 critical section에 빈번히 들어가야 할지라도 프로세스 1이 들어갔다 나오지 않는다면 계속해서 기다려야 한다. 

### Algorithm 2
- Synchronization variable
```c
boolean flag[2];
initially flag[모두] = false;   // critical section에 들어가고자 하는 의사 표시
```
- Process *P<sub>i</sub>*
```c
do {
    flag[i] = true;
    while (flag[j]);
    critical section
    flag[i] = false;
    remainder section
} while (1);
```

상대방이 critical section에서 빠져나왔을 때 내가 들어간다.

**mutual exclusion은 만족하나 progress는 만족하지 않는다.** 프로세스 A가 critical section에 들어가려고 flag를 *true* 로 바꾼 상황에서 context switch가 발생해 프로세스 B에게 CPU가 넘어갔다고 생각해보자. 이때 프로세스 B도 flag를 *true* 로 바꿨는데 다시 프로세스 A가 CPU를 잡는다면 그 누구도 critical section에 들어갈 수 없다. 

### Algorithm 3 (Peterson's Algorithm)
- Combined synchronization variables of algorithms 1 and 2
- Process *P<sub>i</sub>*
```c
do {
    flag[i] = true;
    turn = j;
    while (flag[i] && turn == j);
    critical section
    flag[i] = false;
    remainder section
} while (1);
```

상대방이 critical section에 들어가있지 않고, 들어갈 준비도 하지 않는다면 내가 들어간다.

**세 조건을 모두 만족**하지만 계속 CPU와 메모리를 쓰면서 기다리기 때문에 `spin lock` (= busy waiting)이 발생한다. 쉽게 말해 critical section에 들어가려면 상대방이 CPU를 잡고 flag 변수를 *false* 로 바꿔줘야 하는데, 내가 CPU를 잡고 있는 상황에서 의미 없이 while 문을 돌며 CPU 할당 시간을 흘려보내는 것을 뜻한다.

### Synchronization Hardware
앞서 말한 문제들이 발생한 근본적인 이유는 데이터를 읽고 쓰는 동작을 하나의 instruction으로 수행할 수 없기 때문이다. 따라서 instruction 하나만으로 데이터를 읽는 작업과 쓰는 작업을 `atomic`하게 수행하도록 지원하는 경우 앞의 문제는 간단히 해결할 수 있다.

- Mutual Exclusion with Test & Set
```c
Synchronization variable:
    boolean lock = false;

Process P
    do {
        while (Test_and_Set(lock));
        critical section
        lock = false;
        remainder section
    }
```

*Test_and_Set(a)* instruction은 a라는 데이터를 읽어내고, a를 1로 바꿔준다. 

### Semaphores
- 위의 방식들을 추상화시킴
- Semaphore *S*
    - integer variable
    - 아래 두 가지 atomic 연산에 의해서만 접근 가능
        1. P(S) : 공유 데이터를 획득하는 과정 (**lock**)
        ```c
        while (S ≤ 0) do no-op;
        S--;
        ```

        2. V(S) : 다 사용하고 반납하는 과정 (**unlock**)
        ```c
        S++;
        ```

### Critical Section of n Processes
- Synchronization variable
```c
semaphore mutex;   // initially 1: 1개가 critical section에 들어갈 수 있다.
```
- Process *P<sub>i</sub>*
```c
do {
    P(mutex);
    critical section
    V(mutex);
    remainder section
} while (1);
```

- **spin lock** : *busy waiting* 에는 효율적이지 못함
- **sleep lock** : Block & Wakeup 방식의 구현

### Block / Wakeup Implementation
- Semaphore를 아래와 같이 정의
```c
typedef struct {
    int value;          // semaphore
    struct process *L;  // process wait queue
} semaphore;
```
- Block / Wakeup
    - **block** : 커널은 block을 호출한 프로세스를 suspend 시키고, 해당 프로세스의 PCB를 wait queue에 넣음
    - P(S)
    ```c
    S.value--;
    if (S.value < 0) {
        add this process to S.L;
        block();
    }
    ```
    - **wakeup(P)** : block된 프로세스를 wakeup시키고, 해당 프로세스의 PCB를 ready queue로 옮김
    - V(S)
    ```c
    S.value++;
    if (S.value <= 0) {
        remove a process P from S.L;
        wakeup(P);
    }
    ```

### Which is better ?
- 일반적으로 Block/wakeup 방식이 더 좋음
- critical section 길이가 긴 경우 Block/wakeup이 적당
- critical section 길이가 매우 짧은 경우 Block/wakeup 오버헤드가 busy-wait 오버헤드보다 커질 수 있음

### Two Types of Semaphores
- **Counting semaphore**
    - 도메인이 0 이상인 임의의 정수값
    - 주로 resource counting에 사용
- **Binary semaphore** (= mutex)
    - 0 또는 1 값만 가질 수 있는 semaphore
    - 주로 mutual exclusion (lock/unlock)에 사용

### Deadlock and Starvation
- **Deadlock**
    - 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
    
    S와 Q가 1로 초기화된 semaphore에서 프로세스 A와 프로세스 B 둘 다 S, Q를 획득해야 한다고 가정해보자. 프로세스 A가 S를 획득하고 CPU를 뺏겨 프로세스 B가 Q를 획득하면 프로세스 A 입장에서는 Q를, 프로세스 B 입장에서는 S를 영원히 기다려야 한다. 
    
    자원의 획득 순서를 정해준다면 이를 해결할 수 있다. S를 획득해야만 Q를 획득할 수 있게끔 순서를 정해주면 프로세스 A가 S를 획득했을 때 프로세스 B가 Q를 획득할 일이 없다. S를 먼저 획득해두지 않았기 때문이다.
- **Starvation**
    - indefinite blocking
    - 프로세스가 자원을 얻지 못하고 무한히 기다리는 현상

---

#### 참고
- [KOCW - 운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
