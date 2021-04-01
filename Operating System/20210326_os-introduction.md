> *"KOCW - 반효경 교수님의 운영체제"* 를 듣고 정리한 내용입니다.

## Introduction to Operating System
### 운영체제란 ?
![image](https://user-images.githubusercontent.com/46131688/112940421-17fcc580-9168-11eb-8666-8276f0cc93ec.png)

(사진 출처 - [벨로그](https://velog.io/@jehjong/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C%EC%99%80-%EC%A0%95%EB%B3%B4%EA%B8%B0%EC%88%A0%EC%9D%98-%EC%9B%90%EB%A6%AC-2%EC%9E%A5.-%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B0%9C%EC%9A%94))

- 하드웨어 바로 위에 설치되어 사용자 및 다른 소프트웨어와 하드웨어를 연결하는 계층
    - 좁은 의미의 운영체제 : **커널**, 부팅 후 항상 메모리에 상주하는 부분
    - 넓은 의미의 운영체제 : 커널 + 각종 시스템 유틸리티 포함

### 운영체제의 목적
1. 컴퓨터 시스템 자원을 효율적으로 관리
    - 시스템 자원은 CPU, memory, I/O 장치 등의 *하드웨어 자원* 과 프로세스, 파일, 메시지 등의 *소프트웨어 자원* 이 있다. 
    - 운영체제는 주어진 자원으로 최대한의 성능을 내도록 한다.
2. 컴퓨터 시스템을 편리하게 사용할 수 있는 환경을 제공
    - 사용자로 하여금 프로그램들이 각각 독자적 컴퓨터에서 수행되는 것과 같은 환상을 제공한다.

### 운영체제의 분류
- 동시 작업 가능 여부
    - 단일 작업 (Single tasking) : 한 번에 하나의 작업만 처리 (과거)
    - **다중 작업** (Multi tasking) : 동시에 두 개 이상의 작업 처리 (현대)
- 사용자의 수 *(동시 접근)*
    - 단일 사용자 (single user) : MS-DOS, MS Windows
    - **다중 사용자** (multi user) : UNIX
- 처리 방식
    - 일괄 처리 (batch processing)
        - 작업을 바로 처리하지 않고 모아서 한꺼번에 처리
        - 작업이 완전히 종료될 때까지 기다려야 함
    - **시분할** (time sharing)
        - 여러 작업을 일정한 시간 단위로 분할하여 처리
        - 짧은 응답 시간을 가져 사용자 입장에서는 interactive한 결과를 얻음
    - 실시간 (realtime OS)
        - *Deadline* 이 존재하여 정해진 시간 안에 특정한 일이 종료되는 것을 보장해야 함

### 용어 정리
아래 나온 용어들은 컴퓨터에서 **여러 작업을 동시에 수행**하는 것을 뜻한다.

- Multitasking : 여러 작업이 동시에 수행되는 것
- Multiprogramming : 여러 프로그램이 메모리에 올라가 있는 것 (*메모리 측면* 에서의 강조)
- Time sharing : CPU 시간을 분할하여 나눠쓰는 것 (*CPU 측면* 에서의 강조)
- Multiprocess : 여러 프로그램이 동시에 수행되는 것

참고로 *Multiprocessor* 는 CPU가 여러 개 붙어 있는 컴퓨터를 뜻하기 때문에 위의 용어들과는 하드웨어적으로 다른 시스템이다.

---

#### 참고
- [KOCW - 운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)

