> *"KOCW - 반효경 교수님의 운영체제"* 를 듣고 정리한 내용입니다.

## Memory Management
### Logical Address vs Physical Address
메모리는 주소를 통해 접근하는 매체이다.

- *Logical address* (= virtual address)
    - 프로세스마다 독립적으로 갖는 주소 공간
    - 각 프로세스마다 0번지부터 시작
    - **CPU가 바라보는 주소는 logical address**
- *Physical address*
    - 메모리에 실제 올라가는 위치
    - 아랫 부분에는 운영체제 커널이, 윗 부분에는 여러 프로그램들이 섞여서 올라가 있음

특정한 프로그램이 물리적인 메모리 어디에 올라갈 지 결정하는 것을 **주소 바인딩**이라고 한다.

### 주소 바인딩 (Address Binding)
- **Compile time binding**
    - 컴파일될 때 주소 변환이 이루어짐 (**논리적 주소 = 물리적 주소**)
    - 컴파일러는 *absolute code* 생성
    - 비효율적, 현재 잘 사용하지 않음
- **Load time binding**
    - 실행 시작될 때 주소 변환이 이루어짐
    - 컴파일러가 *relocatable code* 생성한 경우
        - 항상 특정 위치에 올라가는 것이 아니라 실행 시 비어있는 위치로 올라갈 수 있음
- **Execution time binding** (= Runtime binding)
    - 실행 이후에도 물리적인 메모리 주소가 바뀔 수 있음
    - CPU가 주소를 참조할 때마다 바인딩 점검
        - 하드웨어적인 지원 필요 (**MMU**)

### Memory-Management Unit (MMU)
- MMU
    - **논리적 주소를 물리적 주소로 매핑해주는 하드웨어 장치**
- MMU scheme
    - 두 개의 레지스터를 이용해서 주소 변환
        1. *base register* (relocation register) : 접근할 수 있는 물리적 메모리 주소의 최소값
        2. *limit register* : 논리적 주소의 범위
    
    CPU가 논리 주소를 요청하면 limit register를 통해 프로그램보다 더 큰 논리 주소를 요청한 것은 아닌지 확인한다. 만약 limit register에 있는 값을 벗어나는 요청이면 `trap` (SW interrupt)이 걸린다. 프로그램 크기 이내에 있는 요청이라면 *(논리 주소 + base register)* 값을 메모리에서 찾는다.

![image](https://user-images.githubusercontent.com/46131688/114143696-6d4a8b00-994f-11eb-9d3c-58c366d240e7.png)

(사진 출처 - [티스토리](https://kimdora.tistory.com/entry/%EC%9A%B4%EC%98%81-%EC%B2%B4%EC%A0%9C-%EC%86%8C%EA%B0%9C))

- user program
    - **logical address**만을 다룸
    - 실제 physical address를 볼 수 없으며 알 필요도 없음

### Dynamic Loading
- 프로세스 전체를 메모리에 미리 올리는 것이 아니라 해당 루틴이 불려질 때 load하는 것
- **메모리 이용률 향상**
- 가끔씩 사용되는 코드의 경우 유용 (ex. 오류 처리 루틴)
- 운영체제 지원 없이 프로그램 자체에서 구현하는 것 (OS는 라이브러리를 통해 지원 가능)
    - 프로그래머가 명시하지 않고 운영체제가 알아서 메모리에 올리고 쫓아내는 것은 `paging` 

### Overlays
- 메모리에 프로세스 부분 중 실제 필요한 정보만을 올림
- **프로세스 크기가 메모리보다 클 때 유용**
- 작은 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현

다이나믹 로딩은 라이브러리를 통해 구현하는 반면 오버레이는 프로그래머가 하나하나 전부 구현해야 한다는 차이점이 있다.

### Swapping
- **Swapping**
    - 프로세스를 일시적으로 메모리에서 *backing store* 로 쫓아내는 것
- **Backing store** (= swap area)
    - 하드 디스크와 같이 충분히 빠르고 큰 저장 공간
- **Swap in / Swap out**
    - swap out : 메모리에서 backing store로 쫓아내는 것
    - swap in : backing store에서 메모리로 다시 올리는 것
    - 일반적으로 중기 스케줄러 (swapper)에 의해 swap out 시킬 프로세스 선정
        - 메모리에 너무 많은 프로세스가 올라와 있으면 비효율적이기 때문
    - *priority-based CPU scheduling algorithm*
        - 우선순위가 낮은 프로세스를 swap out
        - 우선순위가 높은 프로세스를 swap in
    - swapping이 효율적으로 사용되려면 **runtime binding**이 지원되어야 함
    - swap time은 대부분 *transfer time*, 즉 swap 되는 양에 비례하는 시간

### Dynamic Linking
- `linking`을 실행 시간까지 미루는 기법
    - linking : 여러 군데에 존재하는 컴파일된 파일들을 묶어 하나의 실행 파일을 만드는 과정
- *Static linking*
    - 라이브러리가 프로그램의 실행 파일 코드에 포함됨
    - 실행 파일의 크기가 커짐
    - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비
- *Dynamic linking*
    - 라이브러리가 실행 파일에 포함되어 있지 않고, 라이브러리 위치를 찾기 위한 작은 코드 (`stub`)를 둠
    - 라이브러리를 호출하면 그 라이브러리가 어디에 있는지 찾아 필요하면 디스크에서 읽어와 메모리에 올리고, 이미 메모리에 있다면 그 주소로 가서 실행

100명이 *printf* 함수를 호출했다고 가정해보자. static linking을 사용하면 같은 기능을 하는 함수라고 하더라도 별도의 프로그램에 속한 것이기 때문에 100개가 전부 메모리에 올라간다.   
하지만 dynamic linking을 사용하면 누군가 printf를 호출해서 라이브러리를 메모리에 올려놓은 경우 다른 사람들이 동일한 함수를 호출했을 때 공유해서 사용할 수 있다. 그래서 dynamic linking을 해주는 라이브러리를 `shared library`라고 한다.

### Allocation of Physical Memory
메모리는 일반적으로 두 영역으로 나누어서 사용한다.

- OS 상주 영역 (낮은 주소 영역)
- 사용자 프로세스 영역 (높은 주소 영역)
    
사용자 프로세스 영역은 크게 두 가지 방법으로 할당한다.

1. **Contiguous allocation** (연속 할당)
    - 각각의 프로세스가 메모리 연속적인 공간에 적재되도록 하는 것
    1. Fixed partition allocation
    2. Variable partition allocation
2. **Noncontiguous allocation** (불연속 할당)
    - 하나의 프로세스가 메모리 여러 영역에 분산되어 올라갈 수 있음
    1. Paging
    2. Segmentation
    3. Paged Segmentation

### Contiguous Allocation
1. **고정 분할 방식** (Fixed partition allocation)
    - 프로그램이 들어갈 사용자 메모리 영역을 미리 나누어놓는 것
    - external fragmentation, internal fragmentation 발생
2. **가변 분할 방식** (Variable partition allocation)
    - 프로그램이 실행될 때마다 차곡차곡 메모리 영역에 넣는 것
    - external fragmentation 발생

![image](https://user-images.githubusercontent.com/46131688/114150668-485a1600-9957-11eb-89a1-0980bf8c3381.png)

(사진 출처 - [KOCW 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323))

- Hole
    - 가용 메모리 공간
    - 운영체제는 할당 공간과 가용 공간의 정보를 유지
- *Dynamic Storage-Allocation Problem*
    - 가변 분할 방식에서 사이즈 *n* 인 요청을 만족하는 가장 적절한 hole을 찾는 문제

    1. **First-fit**
        - 사이즈 *n* 이상인 것 중 최초로 찾아지는 hole에 할당
    2. **Best-fit**
        - 사이즈 *n* 이상인 가장 작은 hole을 찾아서 할당
        - hole이 크기 순으로 정렬되지 않은 경우 모든 hole을 탐색해야 함
    3. **Worst-fit**
        - 가장 큰 hole에 할당
        - 상대적으로 아주 큰 hole들이 생성됨
- *compaction*
    - external fragmentation 문제를 해결하는 한 가지 방법
    - **사용 중인 메모리 영역을 한 군데로 몰고, hole들을 다른 한 곳으로 몰아 아주 큰 hole을 만드는 것**
    - 메모리 영역 자체를 이동시키는 것이기 때문에 비용이 매우 많이 드는 방법

### Paging
- **Paging**
    - 프로그램을 구성하는 논리적인 메모리를 동일한 크기의 페이지로 잘라 각 페이지를 물리적 메모리 빈 공간에 올리는 것
    - 일부는 backing storage에, 일부는 physical memory에 저장
- Basic method
    - physical memory를 동일한 크기의 *frame* 으로 나눔
    - logical memory를 동일한 크기의 *page* 로 나눔
    - **페이지 테이블을 사용하여 logical address를 physical address로 변환**
    - external fragmentation은 발생하지 않으나 internal fragmentation 발생 가능

### Implementation of Page Table
- 페이지 테이블은 *main memory* 에 상주
- register
    - *page-table base register* (`PTBR`) : 페이지 테이블의 시작 위치를 가리킴
    - *page-table length register* (`PTLR`) : 페이지 테이블의 크기 보관
- 모든 메모리 접근 연산에는 2번의 메모리 엑세스 필요
    1. page table을 통한 주소 변환을 위한 접근
    2. 실제 데이터를 가져오기 위한 접근
- 메모리에 두 번 엑세스하는 것은 상당히 비용이 큰 작업
    - 속도 향상을 위해 *associative register* 혹은 `TLB` (Translation Look-aside Buffer) 사용
        - TLB는 주소 변환을 위한 고속의 하드웨어 캐시 메모리
        - *(page number, frame number)* 쌍
        - **페이지 테이블에서 빈번히 참조되는 일부 엔트리들을 캐싱**
        - context switch 시 *flush* (모든 엔트리들을 비움)

        CPU로부터 논리적인 주소를 통해 요청이 들어오면 일단 TLB를 검사한다. TLB에 해당 주소에 대한 내용이 존재하면 바로 주소 변환이 이루어지고, 존재하지 않는다면 페이지 테이블로 접근한다.

        하지만 TLB는 논리적인 페이지 번호와 물리적인 페이지 번호를 쌍으로 저장하고 있다. 그래서 논리적인 주소를 인덱스로 삼아 바로 접근할 수 없어 TLB 전체를 검사해야 하는데, 이 시간마저 줄이기 위해 **associative register**를 사용한다. associative register는 `parallel search`를 통해 *O(1)* 만에 전체를 탐색할 수 있다.

![image](https://user-images.githubusercontent.com/46131688/114167817-6715d800-996a-11eb-84bd-ee4e909c3b0b.png)

(사진 출처 - [스택오버플로우](https://stackoverflow.com/questions/37825859/cache-miss-a-tlb-miss-and-page-fault))

### Two-Level Page Table
- 현대 컴퓨터는 address space가 매우 큰 프로그램 지원
    - 32 bit address 사용 시 (2<sup>32</sup> (4GB)의 주소 공간)
        - 4KB 페이지가 1M개의 페이지 테이블 엔트리 필요
        - 각 페이지 엔트리가 4B 시 프로세스 당 4MB의 페이지 테이블 필요
        - 하지만 대부분 프로그램은 4GB의 주소 공간 중 극히 일부만 사용하므로 페이지 테이블 공간이 심하게 낭비됨
- two-level page table을 쓰면 시간은 조금 더 걸리나 페이지 테이블을 위한 공간을 줄일 수 있음
    - 페이지 테이블 자체를 페이지로 구성 (**outer page table**)
    - **사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 *NULL***

### Multi-level Paging and Performance
- address space가 더 커지면 다단계 페이지 테이블 필요
- 각 단계의 페이지 테이블이 메모리에 존재하므로 **주소 변환에 더 많은 메모리 접근 필요**
- **TLB를 통해 메모리 접근 시간을 줄일 수 있음**
    - 메모리 접근 시간이 *100ns*, TLB 접근 시간이 *20ns*, TLB hit ratio가 *98%* 인 경우
        - *effective memory access time* = 0.98 * 120 + 0.02 * (120 + 400) = 128ns
        - 결과적으로 주소 변환을 위해 *28ns* 만 소요

### Memory Protection
페이지 테이블의 각 엔트리마다 아래의 *bit* 를 둔다.

- Protection bit
    - 페이지에 대한 접근 권한 (read / write / read-only)
- Valid-invalid bit
    - **valid** : 해당 주소의 프레임에 그 프로세스를 구성하는 유효한 내용이 있음 (접근 허용)
    - **invalid** : 해당 주소의 프레임에 유효한 내용이 없음 (접근 불허)
        1. 프로세스가 해당 주소 부분을 사용하지 않는 경우
        2. 해당 페이지가 메모리에 있지 않고 *swap area* 에 있는 경우

### Segmentation
- 프로그램을 **의미 단위**인 여러 개의 세그먼트로 구성
    - 작게는 프로그램을 구성하는 함수 하나하나
    - 크게는 프로그램 전체
    - 일반적으로는 code, data, stack 부분이 하나씩의 세그먼트로 정의됨

### Segmentation Architecture
- logical address는 다음의 두 가지로 구성
    - *(segment number, offset)*
- **Segment table**
    - each table entry has
        - *base* : starting physical address of the segment
        - *limit* : length of the segment
- register
    - *segment-table base register* (`STBR`) : 물리적 메모리에서 세그먼트 테이블의 위치
    - *segment-table length register* (`STLR`) : 프로그램이 사용하는 세그먼트의 수

![image](https://user-images.githubusercontent.com/46131688/114201352-106fc480-9991-11eb-8a41-96876cfe5d64.png)

(사진 출처 - [티스토리](https://woodforest.tistory.com/m/11?category=789360))

### Segmentation Architecture
- *Protection*
    - 각 세그먼트 별로 protection bit 존재
        - valid bit = 0 : illegal segment
        - read / write / execution 권한 bit
- *Sharing*
    - shared segment
    - same segment number

    세그먼트는 의미 단위이기 때문에 **공유와 보안에 있어 페이징보다 훨씬 효과적**이다. (장점)
- *Allocation*
    - first fit / best fit
    - external fragmentation 발생

    세그먼트의 길이가 동일하지 않기 때문에 연속 할당에서의 가변분할 방식과 동일한 문제들이 발생한다. (단점)

### Paged Segmentation
세그먼트 하나가 여러 개의 페이지로 구성되어 있다. 물리적인 메모리에는 페이지 단위로 올라가기 때문에 external fragment로 인한 *hole* 이 생기지 않는다.

- pure segmentation과의 차이점
    - 세그먼트 테이블 엔트리가 세그먼트의 base address를 갖고 있지 않고 **세그먼트를 구성하는 페이지 테이블의 base address를 갖고 있음**

![image](https://user-images.githubusercontent.com/46131688/114208633-a78c4a80-9998-11eb-9c4a-f1ab1908f004.png)

(사진 출처 - [chegg](https://www.chegg.com/homework-help/questions-and-answers/consider-segmentation-paging-address-translation-scheme-shown-figure--scheme-provides-proc-q36880098))

---

#### 참고
- [KOCW - 운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
