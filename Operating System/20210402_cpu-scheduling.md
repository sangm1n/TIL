> *"KOCW - 반효경 교수님의 운영체제"* 를 듣고 정리한 내용입니다.

## CPU Scheduling
### CPU-burst Time의 분포
![image](https://user-images.githubusercontent.com/46131688/113381138-268deb80-93b9-11eb-8779-bb3c712d4d5e.png)

(사진 출처 - [티스토리](https://yoon1fe.tistory.com/34))

여러 종류의 job (process)이 섞여있어 CPU 스케줄링이 필요하다. CPU와 I/O 장치 등 시스템 자원을 골고루 사용해야 한다.

### 프로세스의 특성 분류
- *I/O bound process*
    - **many short CPU bursts**
    - 주로 사람과 interaction하는 프로세스
    - I/O 개입이 많아 CPU burst가 짧은 경우
- *CPU bound process*
    - **few very long CPU bursts**
    - 계산 위주의 프로세스
        - ex) 10,000 by 10,000 행렬의 곱셈 연산
    - I/O 개입이 적어 CPU burst가 긴 경우

### CPU scheduler & Dispatcher
- **CPU shceduler**
    - *Ready* 상태의 프로세스 중 CPU를 누구한테 줄 지 결정하는 것
- **Dispatcher**
    - CPU를 누구한테 줄 지 결정했으면 해당 프로세스에게 넘기는 역할
    - 이 과정을 `context switch`라고 함

- CPU 스케줄링이 필요한 경우
    1. Running → Blocked (ex. I/O 요청하는 시스템콜)
    2. Running → Ready (ex. 할당 시간 만료로 인한 타이머 인터럽트)
    3. Blocked → Ready (ex. I/O 완료 후 인터럽트)
    4. Terminated

1, 4에서의 스케줄링은 **강제로 빼앗지 않고 자진 반납**하는 `nonpreemptive` 방식이고, 그 외의 나머지 스케줄링은 **CPU를 강제로 빼앗는** `preemptive` 방식이다.

### Scheduling Criteria
- 시스템 입장
    - **CPU utilization** (이용률)
        - 전체 시간 중에서 CPU가 놀지 않고 일한 시간의 비율
        - keep the *CPU as busy as possible*
    - **Throughput** (처리량)
        - 주어진 시간 동안에 몇 개의 일을 처리했느냐
        - *number of processes* that *complete* their execution per time until
- 프로세스 입장 → **시간의 개념**
    - **Turnaround time** (소요시간, 반환시간)
        - CPU를 쓰러 들어와서 다 쓰고 나갈 때까지 걸린 시간
        - amount of time to *execute a particular process*
    - **Waiting time** (대기 시간)
        - CPU를 얻기까지 ready queue에 줄 서서 기다린 시간
        - amount of time a process has been *waiting in the ready queue*
    - **Response time** (응답 시간)
        - ready queue에 들어와서 **처음으로** CPU를 얻기까지 걸린 시간
        - amount of time it takes *from when a request was submitted until the first response is produced*, not output

## Scheduling Algorithms
### FCFS (First-Come First-Served)
- 먼저 온 순서대로 처리하는 방식 (**nonpreemptive**)
- CPU를 오래 쓰는 프로세스가 먼저 와서 CPU를 할당 받으면 나머지 프로세스들은 전부 기다려야 하기 때문에 효율적이지 않음

![image](https://user-images.githubusercontent.com/46131688/113419531-9a081b00-9402-11eb-9f33-aed8de791125.png)

(사진 출처 - [UIC](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/5_CPU_Scheduling.html))

- 사진 아래처럼 어떤 프로세스가 먼저 실행되냐에 따라 전체 대기 시간에 상당한 영향을 미침
- 문제점
    -**Convoy effect**
        - 긴 프로세스 하나 때문에 짧은 프로세스 여러 개가 기다리는 현상

### SJF (Shortest-Job-First)
- CPU burst가 가장 짧은 프로세스에게 CPU를 부여하는 방식
- *minimum average waiting time* 보장
- 두 가지 방식
    - *nonpreemptive*
        - 일단 CPU를 잡으면 더 짧은 프로세스가 들어와도 CPU burst가 완료될 때까지 CPU를 선점당하지 않음
    - *preemptive*
        - `SRTF` (Shortest-Remaining-Time-First)
        - CPU를 잡았다 하더라도 더 짧은 프로세스가 들어오면 CPU를 빼앗김
- 문제점
    1. **Starvation**
        - 짧은 프로세스들로 인해 긴 프로세스가 오랫동안 CPU를 잡지 못하는 현상
    2. **CPU burst time을 미리 알 수 없음**
        - exponential average : 과거의 CPU 사용 시간을 통해 추정 (estimate)만 가능

### Priority Scheduling
- 우선순위가 제일 높은 프로세스에게 CPU 할당 (**smallest integer = highest priority**)
- 두 가지 방식
    - *nonpreemptive*
        - 일단 CPU를 잡으면 더 높은 우선순위를 가진 프로세스가 들어와도 CPU burst가 완료될 때까지 CPU를 선점당하지 않음
    - *preemptive*
        - CPU를 잡았다 하더라도 더 높은 우선순위를 가진 프로세스가 들어오면 CPU를 빼앗김
- `SJF`는 일종의 priority scheduling (**priority = predicted next CPU burst time**)
- 문제점
    - **Starvation**
        - 우선순위가 낮은 프로세스는 오랫동안 CPU를 잡지 못하는 현상
- 해결
    - **Aging**
        - 아무리 우선순위가 낮은 프로세스라 하더라도 시간이 오래 지나면 우선순위를 높여주는 것

### Round Robin (RR)
- 각 프로세스는 동일한 크기의 할당 시간 (`time quantum`)을 가짐
- 할당 시간이 지나면 프로세스는 CPU를 빼앗기고 ready queue 맨 뒤에 가서 다시 줄을 섬 (**preemptive**)
- **short response time**
    - 조금씩 CPU 줬다 뺏었다를 반복하기 때문에 **CPU를 최초로 얻기까지 걸리는 시간이 짧음**
    - n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우 어떤 프로세스도 *(n-1) X q* time unit 이상 기다리지 않음
    - CPU 할당받으려고 기다리는 시간이 CPU burst time에 비례
- Performance
    - *q* large → FCFS
    - *q* small → context switch 오버헤드 증가
- 일반적으로 SJF보다 average turnaround time이 길지만 response time은 짧음

### Multi-Level Queue
- ready queue를 우선순위에 따라 여러 개로 분할
    - *foreground* (interactive)
    - *background* (batch - no human interaction)
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
    - *foreground* : Round Robin
    - *background* : FCFS
- 큐에 대한 스케줄링 필요
    - Fixed priority scheduling
        - serve all from foreground then from background
        - starvation 가능성 존재
    - Time slice
        - 각 큐에 CPU time을 적절한 비율로 할당
        - ex) 80% to foreground in RR, 20% to background in FCFS

### Multi-Level Feedback Queue
- 프로세스가 여러 개로 분할된 ready queue 내에서 다른 큐로 이동 가능
- MLFQ 방식으로 `aging` 구현 가능
- MLFQ를 정의하는 파라미터들
    - queue의 수
    - 각 큐의 스케줄링 알고리즘
    - process를 상위 큐로 보내는 기준
    - process를 하위 큐로 내쫓는 기준
    - 프로세스가 CPU 서비스를 받으려할 때 들어갈 큐를 결정하는 기준
        - 보통 처음 들어오는 프로세스는 우선순위 가장 높은 큐에 time quantum을 짧게 해서 배치
        - 밑에 큐로 갈수록 RR의 할당 시간을 길게, 맨 아래 큐는 FCFS 방식 사용

![image](https://user-images.githubusercontent.com/46131688/113425422-cb85e400-940c-11eb-9c14-354516e16c9b.png)

(사진 출처 - [UIC](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/5_CPU_Scheduling.html))

### Real-time Scheduling
정해진 시간 안에 반드시 실행이 되어야 하는, 즉 `deadline`이 있는 프로세스

- Hard real-time systems
    - hard real-time task는 정해진 시간 안에 반드시 끝내도록 스케줄링해야 함
- Soft real-time computing (최근)
    - soft real-time task는 일반 프로세스에 비해 높은 우선순위를 갖도록해야 함
    - deadline을 꼭 보장하지는 못함

### Thread Scheduling
- Local scheduling
    - user level thread의 경우 운영체제는 스레드의 존재를 모르기 때문에 사용자 수준의 스레드 라이브러리에 의해 어떤 스레드를 스케줄할 지 결정
    - OS가 아닌 **사용자 프로세스가 직접** 어느 스레드한테 CPU를 줄 것인가를 결정
- Global scheduling
    - kernel level thread의 경우 일반 프로세스처럼 커널의 단기 스케줄러가 어떤 스레드를 스케줄할 지 결정

---

#### 참고
- [KOCW - 운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
