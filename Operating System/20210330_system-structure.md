> *"KOCW - 반효경 교수님의 운영체제"* 를 듣고 정리한 내용입니다.

## System Structure & Program Execution
### 컴퓨터 시스템 구조
![image](https://user-images.githubusercontent.com/46131688/113013708-e5c68480-91b6-11eb-9232-bf11fd176e4f.png)

(사진 출처 - [KOCW 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323))

CPU는 평생 메모리에 접근하여 명령어를 실행하는 일만 한다. 명령어 하나를 처리하면 Program Counter를 1 증가시켜 다음 명령어를 실행한다.

#### Mode bit
- 사용자 모드 (1) : 사용자 프로그램 수행
- 커널 모드 (0) : OS 코드 수행
    - 운영체제가 CPU를 갖고 있으므로 **모든 명령어 수행 가능** (메모리 접근, I/O 접근 등)

*interrupt* 나 *exception* 발생 시 하드웨어가 mode bit을 0으로 바꾼다. 운영체제에서 사용자 프로그램으로 CPU를 다시 넘길 때에는 mode bit을 1로 바꿔서 넘긴다.

#### Timer
타이머는 **time sharing**을 구현하기 위해 사용되는데, 무한루프와 같이 CPU를 특정 프로그램이 독점하는 것으로부터 보호해준다. 그래서 운영체제가 사용자 프로그램에게 CPU를 넘겨줄 때 타이머에 시간 세팅을 하고 넘겨준다. 할당된 시간이 끝나면 타이머가 CPU에게 인터럽트를 걸어 다시 CPU가 운영체제 쪽으로 넘어간다.

#### Device Controller
I/O 장치를 전담하는 일종의 작은 CPU로 실제 데이터를 저장하는 *local buffer* 와 제어 정보를 위한 *control register* 를 갖는다.
ex) 화면에 출력할 데이터는 local buffer에 넣고, 실제 *"화면에 출력해"* 라는 지시는 control register에서 실행한다.

- device driver : OS 코드 중 각 장치별 처리 루틴 (**software**)
- device controller : 각 장치를 통제하는 일종의 작은 CPU (**hardware**)

#### DMA (Direct Memory Access) 
원래는 CPU만 메모리에 접근할 수 있다. 하지만 `DMA Controller`를 둠으로써 CPU와 DMA Controller 둘 다 메모리에 접근할 수 있게 된다. 

- CPU와 DMA Controller가 메모리에 둘 다 접근하는 오버헤드
    - *memory controller* 가 중재자 역할 (교통 정리)
- CPU가 모든 I/O 장치의 인터럽트를 처리하기엔 비효율적
    - DMA Controller가 local buffer에 있는 데이터를 메모리에 복사해줌으로써 CPU 효율성 상승

### 입출력(I/O)의 수행
- 모든 입출력 명령은 **특권 명령**
- 사용자 프로그램이 I/O 장치에 접근하는 방법
    - **system call** : 사용자 프로그램이 운영체제의 커널 함수를 호출하는 것 (I/O 요청)
    - trap을 사용하여 인터럽트 벡터의 특정 위치로 이동

### 인터럽트 (Interrupt)
인터럽트 당한 시점의 레지스터와 PC (`Program Counter`)를 저장한 후 CPU의 제어를 인터럽트 처리 루틴에 넘긴다.

- *interrupt* (HW interrupt) : 하드웨어가 발생시킨 인터럽트 (timer interrupt, I/O controller interrupt 등)
- *trap* (SW interrupt)
    - exception : 프로그램이 오류를 범한 경우
    - system call : 프로그램이 커널 함수를 호출하는 경우

따라서 **I/O 요청할 때에는 소프트웨어 인터럽트와 하드웨어 인터럽트가 모두 필요**하다.

1. 사용자 프로그램이 I/O 요청을 위해 소프트웨어 인터럽트 (system call 호출)
2. I/O 작업이 끝나면 *"일을 다 했다"* 고 하드웨어 인터럽트로 CPU에게 알림

현대의 운영체제는 인터럽트에 의해 구동된다.

- 인터럽트 관련 용어
    - 인터럽트 벡터 : 해당 인터럽트의 처리 루틴 주소를 갖고 있는 테이블
    - 인터럽트 처리 루틴 (= 인터럽트 핸들러) : 실제 인터럽트를 처리하는 함수

### 동기식 입출력과 비동기식 입출력
- 동기식 입출력 (Synchronous I/O)
    - I/O 요청 후 **입출력 작업이 완료된 후에야** 제어가 다시 사용자 프로그램으로 넘어감
- 비동기식 입출력 (Asynchronous I/O)
    - I/O 요청 후 **입출력 작업이 완료되기를 기다리지 않고** 제어가 사용자 프로그램으로 즉시 넘어감

두 경우 모두 I/O의 완료는 인터럽트로 알려준다.

![image](https://user-images.githubusercontent.com/46131688/113017363-54f1a800-91ba-11eb-80bc-62adf6298e48.png)

(사진 출처 - [티스토리](https://yoon1fe.tistory.com/18))

### 서로 다른 입출력 명령어
1. 일반적인 I/O 방식
    - CPU에서 수행할 수 있는 instruction은 메모리에 접근하는 instruction과 I/O 장치에 접근하는 instruction 두 가지 존재
2. Memory Mapped I/O
    - I/O 장치에도 메모리 주소를 입혀 메모리에 접근하는 instruction으로 I/O 장치에도 접근

### 저장장치 계층 구조
![image](https://user-images.githubusercontent.com/46131688/113019468-9e42f700-91bc-11eb-965b-ac0a5d88cbc6.png)

(사진 출처 - [티스토리](https://wan-blog.tistory.com/32))

위로 갈수록 속도가 빠르고, 값이 비싸 저장 용량이 적다.

- Primary (Executable) : CPU에서 직접 접근해서 처리, **휘발성**
- Secondary : CPU에서 직접 접근 불가, **비휘발성**

### 커널 주소 공간의 내용
- *code*
    - 자원 관리를 위한 코드
    - 시스템콜, 인터럽트 처리 코드
    - 편리한 서비스 제공을 위한 코드
- *data*
    - 운영체제가 사용하는 여러 자료구조 정의
    - PCB (Process Control Block) : 각 프로그램을 관리하기 위한 자료구조
- *stack*
    - 각 프로세스의 커널 스택

### 사용자 프로그램이 사용하는 함수
- 사용자 정의 함수 → 프로세스 A의 code 영역
    - 내가 직접 작성한 함수
- 라이브러리 함수 → 프로세스 A의 code 영역
    - 누군가 만들어놔서 갖다 쓴 함수
- 커널 함수 → 커널의 code 영역
    - 운영체제 안에서 정의된 함수
    - **커널 함수의 호출 = 시스템콜**

### 프로그램의 실행
![image](https://user-images.githubusercontent.com/46131688/113023388-840b1800-91c0-11eb-8e85-b54890deb97d.png)

(사진 출처 - [벨로그](https://velog.io/@gojaegaebal/210207-%EA%B0%9C%EB%B0%9C%EC%9D%BC%EC%A7%8062%EC%9D%BC%EC%B0%A8-%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9COS-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-2-0-User-mode-Kernel-mode))


---

#### 참고
- [KOCW - 운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)

