> *"KOCW - 반효경 교수님의 운영체제"* 를 듣고 정리한 내용입니다.

## Virtual Memory
### Demand Paging
- 페이지가 요청됐을 때 메모리에 올리는 것
    - I/O 양의 감소
    - 메모리 사용량 감소
    - 빠른 응답 시간
    - 더 많은 사용자 수용
- Valid / Invalid bit 사용
    - *invalid*
        - 사용되지 않는 주소 영역인 경우
        - 페이지가 메모리에 올라와있지 않고 **backing store**에 있는 경우
    - 처음에는 모든 페이지 엔트리가 invalid로 초기화
    - **page fault**
        - 요청한 페이지가 메모리에 없는 경우 = 페이지 테이블에 invalid로 세팅되어 있는 경우
        - CPU가 운영체제에게 넘어감 (일종의 SW interrupt)

### Page Fault
- invalid page를 접근하면 `MMU`가 trap을 발생시킴 (**page fault trap**)
- 커널 모드로 들어가 *page fault handler* 실행
- sequence
    1. 해당 페이지에 대한 접근이 적합한지 체크
    2. get an empty page frame (빈 공간이 없다면 쫓아냄, **replacement**)
    3. 해당 페이지를 디스크에서 메모리로 읽어옴
        1. disk I/O가 끝나기까지 이 프로세스는 CPU를 선점당함 (`blocked`)
        2. disk read가 끝나면 페이지 테이블 엔트리 기록 (valid-invalid bit = **"valid"**)
        3. 프로세스를 ready queue에 배치
    4. 이 프로세스가 CPU를 잡고 다시 *running*
    5. 이전에 중단되었던 instruction 재개

### Free frame이 없는 경우
- Page replacement
    - 어떤 프레임을 빼앗아올지 결정해야 함
    - 바로 사용되지 않을 페이지를 쫓아내는 것이 좋음
- Replacement Algorithm
    - 목표 : *page-fault rate* 최소화
    - 알고리즘 평가
        - 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사

### Offline Optimal Algorithm
- MIN (OPT) : 가장 먼 미래에 참조되는 페이지를 교체
    
    사실 미래에 어떤 페이지가 참조될 지 아는 것은 불가능하다. 실제 시스템에서 사용될 수는 없고, 단지 미래에 참조되는 페이지를 전부 알고 있다고 가정한다.

![image](https://user-images.githubusercontent.com/46131688/114261080-992f4480-9a13-11eb-8ed8-b78f06e6b0b1.png)

(사진 출처 - [KOCW 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323))

- 다른 알고리즘의 성능에 대한 *upper bound* 제공
    - 아무리 좋은 알고리즘을 만들어도 offline optimal algorithm보다 좋을 수는 없음

### FIFO (First In First Out) Algorithm
- 먼저 들어온 것을 먼저 내쫓음
- **FIFO Anomaly**
    - 프레임 수를 늘렸음에도 불구하고 페이지 부재가 증가하는 현상

![image](https://user-images.githubusercontent.com/46131688/114261161-1a86d700-9a14-11eb-92c0-cde37a48da4d.png)

(사진 출처 - [KOCW 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323))

### LRU (Least Recently Used) Algorithm
- 가장 오래 전에 참조된 페이지를 내쫓음

![image](https://user-images.githubusercontent.com/46131688/114261195-486c1b80-9a14-11eb-97cc-a48a137ab34d.png)

(사진 출처 - [KOCW 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323))

### LFU (Least Frequently User) Algorithm
- 참조 횟수가 가장 적은 페이지를 내쫓음
- 최저 참조 횟수인 페이지가 여러 개인 경우
    - LFU 알고리즘 자체에서는 여러 페이지 중 임의로 선정
    - 성능 향상을 위해 가장 오래 전에 참조된 페이지를 지우도록 구현할 수도 있음
- 장단점
    - LRU와 달리 장기적인 시간 규모를 보기 때문에 **페이지의 인기도를 더 정확히 반영할 수 있음**
    - 하지만 참조 시점의 최근성을 반영하지 못하고, LRU보다 구현이 복잡함

### LRU와 LFU 알고리즘의 구현
![image](https://user-images.githubusercontent.com/46131688/114261719-127c6680-9a17-11eb-808f-598749c77f76.png)

(사진 출처 - [KOCW 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323))

**LRU** 알고리즘은 연결리스트로 구현한다. 참조 시간 순서로 줄을 세워 아래로 갈수록 최근에 참조된 페이지라고 보면 된다. 새로운 페이지를 참조할 경우 맨 아래로 보내면 되고, 프레임이 꽉 차 페이지 하나를 빼야할 경우 맨 위 (가장 오래 전에 참조된) 페이지를 제거하면 된다. 따라서 시간복잡도는 *O(1)* 이다.

**LFU** 알고리즘도 연결리스트로 구현할 수 있다. 하지만 **참조 횟수 순서**로 줄을 세울 경우 새로운 페이지를 참조했을 때 맨 아래로 보내는 것이 아닌 다음 노드와의 횟수를 계속 비교해야 한다. 이와 같이 구현하면 *O(n)* 의 시간복잡도를 갖기 때문에 LFU 알고리즘은 **힙**으로 구현한다. 이로써 시간복잡도를 *O(log n)* 까지 줄일 수 있다.

### 다양한 캐싱 환경
- 캐싱 기법
    - `cache` (한정된 빠른 공간)에 데이터를 저장해 두었다가 나중에 같은 데이터에 대한 요청이 왔을 때 느린 저장 장치까지 가지 않고 캐시로부터 바로 서비스하는 방식
    - paging system 외에도 cache memory, buffer caching, web caching 등 다양한 분야에서 사용
- 캐시 운영의 시간 제약
    - 페이지 교체 알고리즘에서 삭제할 항목을 결정하는 일에 너무 많은 시간이 걸리면 실제 시스템에서 사용할 수 없음
    - buffer caching / web caching
        - *O(1)* 에서 *O(log n)* 정도까지 허용
    - paging system

        운영체제는 `page fault`인 경우에만 관여한다. 즉, 페이지가 이미 메모리에 존재하는 경우 페이지 참조 시각이나 페이지 참조 횟수 등의 정보를 운영체제가 알 수 없다. 따라서 LRU나 LFU와 같은 알고리즘을 페이징 시스템에서 사용할 수 없다.

### Clock Algorithm
결국 페이징 시스템에서는 LRU, LFU 알고리즘을 사용할 수 없고, LRU의 근사 알고리즘인 `clock algorithm`을 사용한다.

- 여러 명칭으로 불림
    - *Second chance algorithm*
    - **NRU** (Not Recently Used) 또는 **NUR** (Not Used Recently)
- reference bit을 사용해서 교체 대상 페이지 선정 (**circular list**)
- *reference bit = 0* 인 것을 찾을 때까지 포인터를 하나씩 이동
    - 포인터 이동 중에 reference bit이 1인 값은 모두 0으로 바꿈
    - **reference bit이 0이라는 것은 페이지에 대한 참조가 없었다는 의미**
- reference bit이 0인 페이지를 찾으면 해당 페이지를 교체
- 한 바퀴 되돌아와도 (= second chance) 0이면 *replace*

![image](https://user-images.githubusercontent.com/46131688/114262538-7dc83780-9a1b-11eb-921c-53fa93eb7544.png)

(사진 출처 - [티스토리](https://asfirstalways.tistory.com/140))

LRU와 완전히 같지는 않지만 시계 방향으로 한 바퀴 도는 동안 참조되지 않는 페이지를 내쫓는 것이기 때문에 최근에 참조되지 않은 페이지를 교체한다고 볼 수 있다.

- Clock algorithm의 개선
    - reference bit과 modified bit (dirty bit) 함께 사용
    - *reference bit = 1* : 최근에 참조된 페이지
    - *modified bit = 1* : 최근에 변경된 페이지
        - 해당 페이지는 메모리에 올라온 이후 적어도 한 번은 CPU에 의해 *write* 가 발생한 것이므로 backing store에 수정된 내용을 반영해야 함
    
    따라서 가능한 modified bit이 0인 값을 쫓아낸다.

### Page Frame의 Allocation
- Allocation problem    
    - 각 프로세스에 얼마만큼의 페이지 프레임을 할당할 것인가 ?
- Allocation 필요성
    - 메모리 참조 명령어 수행 시 intruction, data 등 여러 페이지 동시 참조
        - 명령어 수행을 위해 최소한 할당되어야 하는 프레임 수가 있음
    - *loop* 를 구성하는 페이지들은 한꺼번에 할당되는 것이 유리
        - 최소한의 allocation이 없으면 매 반복마다 *page fault*
- Allocation Shceme
    - *Equal allocation*
        - 모든 프로세스에 똑같은 개수 할당
    - *Proportional allocation*
        - 프로세스 크기에 비례하여 할당
    - *Priority allocation*
        - 프로세스 우선순위에 따라 다르게 할당

### Global Replacement vs Local Replacement
- *Global Replacement*
    - replace 시 다른 프로세스에 할당된 프레임을 빼앗아 올 수 있음
    - FIFO, LRU, LFU 등의 알고리즘을 global replacement로 사용하는 경우
    - working set, PFF 알고리즘 사용
- *Local Replacement*
    - 자신에게 할당된 프레임 내에서만 replacement
    - FIFO, LRU, LFU 등의 알고리즘을 프로세스 별로 운영하는 경우

### Thrashing
![image](https://user-images.githubusercontent.com/46131688/114263305-a9e5b780-9a1f-11eb-979c-dcbf6e756d5c.png)

(사진 출처 - [티스토리](https://rain-bow.tistory.com/entry/Thrashing%EC%8A%A4%EB%9E%98%EC%8B%B1))

- 프로세스의 원활한 수행에 필요한 최소한의 페이지 프레임을 할당 받지 못해 *page fault* 가 빈번히 발생하는 경우
- page fault rate이 매우 높아짐
    - CPU가 명령어를 실행하려고 할 때마다 해당 페이지가 메모리에 존재하지 않아 I/O 작업이 잦아지고, 이에 따라 **CPU 이용률 감소**
- CPU utilization이 낮아지면 OS는 `MPD` (Multi-programming degree)를 높여야 한다고 판단
- 또 다른 프로세스가 시스템에 추가됨 (higher MPD)
- 프로세스 당 할당된 프레임 수가 더욱 감소
- 프로세스는 swap in /swap out으로 매우 바빠 결과적으로 **low throughput**

### Working-set Model
- Locality of reference
    - 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조
    - *locality set* : 집중적으로 참조되는 해당 페이지들의 집합
- Working-set Model
    - working set algorithm에서 **working set = locality set**
    - 프로세스의 working set 전체가 메모리에 올라와 있어야 수행되고, 그렇지 않을 경우 모든 프레임을 반납한 후 swap out (`suspended`)
- thrashing을 방지하고 multi-programming degree 결정
- working set을 미리 알 수 없으므로 과거를 통해 추정
    - window size를 조절하며 **working set에 속한 페이지는 메모리에 유지, 속하지 않은 것은 버림**

### PFF (Page-Fault Frequency) Scheme
![image](https://user-images.githubusercontent.com/46131688/114263654-e4505400-9a21-11eb-97b6-10323a3fd901.png)

(사진 출처 - [pling](https://www.pling.org.uk/cs/ops.html))

- 직접 **page fault rate** 를 확인하는 방식
    - *page fault rate > 상한값* : 프레임을 더 할당
    - *page fault rate < 하한값* : 프레임 수를 줄임
- 빈 프레임이 없으면 일부 프로세스를 *swap out*
- thrashing을 방지하고 multi-programming degree 결정

### Page Size의 결정
- page size를 감소시키면
    - 페이지 수 증가
    - 페이지 테이블 크기 증가
    - internal fragmentation 감소
    - disk transfer 효율성 감소
        - seek/rotation vs transfer
        - seek는 원래 오래 걸리는데 시간이 더 길어져서 비효율적
    - 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
        - *loc+ality* 활용 측면에서는 좋지 않음
- Trend
    - 메모리 크기가 커짐에 따라 **larger page size**

---

#### 참고
- [KOCW - 운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
