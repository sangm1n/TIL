> *"KOCW - 반효경 교수님의 운영체제"* 를 듣고 정리한 내용입니다.

## File Systems Implementation
### Allocation of File Data in Disk
- Contiguous Allocation
- Linked Allocation
- Indexed Allocation

### Contiguous Allocation
![image](https://user-images.githubusercontent.com/46131688/114302142-bbec5680-9b02-11eb-819d-002f0d745c47.png)

(사진 출처 - [questionsolves](http://www.questionsolves.com/Website-Content/ContiguousAllocation.php))

- 장점
    - fast I/O
        - 한 번의 seek/rotation 많은 바이트 transfer
        - *realtime file* 용으로, 혹은 이미 실행 중이던 프로세스의 *swapping* 용
    - **direct access (= random access) 가능**
- 단점
    - external fragmentation (hole 발생)
    - file grow가 어려움
        - 파일 크기가 커질 것을 대비해서 미리 할당 → internal fragmentation

**외부 조각은 아무도 사용하지 않아 누군가에게 할당될 수 있는 공간을 의미하고, 내부 조각은 이미 할당이 됐는데 아직 사용하지 않는 공간을 의미한다.**

### Linked Allocation
![image](https://user-images.githubusercontent.com/46131688/114302165-d292ad80-9b02-11eb-9c01-0838d592a97d.png)

(사진 출처 - [questionsolves](http://www.questionsolves.com/Website-Content/LinkedAllocation.php))

- 장점
    - external fragmentation 발생 X
- 단점
    - **No direct access**
    - *seek* 시 많은 시간 소요
    - reliability 문제
        - 중간에 하나의 섹터가 `bad sector` 되면 그 뒷 부분은 모두 잃음
    - 포인터를 위한 공간이 block의 일부가 되어 공간 효율성이 떨어짐
        - 보통 한 섹터는 512 bytes로 구성되는데 4 bytes짜리 포인터를 저장함으로써 508 bytes만 저장할 수 있는 상황
- 변형
    - `FAT` (File-Allocation Table) 파일 시스템
        - 포인터를 별도의 위치에 보관하여 reliability와 공간 효율성 문제 해결

### Indexed Allocation
![image](https://user-images.githubusercontent.com/46131688/114302212-01108880-9b03-11eb-859a-93d90d077cb8.png)

(사진 출처 - [questionsolves](http://www.questionsolves.com/Website-Content/IndexAllocation.php))

- 장점
    - external fragmentation 발생 X
    - direct access 가능
- 단점
    - 파일이 굉장히 작은 경우에도 공간 낭비 (실제로 많은 파일들이 굉장히 작음)
        - **인덱스를 위한 블록 + 실제 데이터를 저장하기 위한 블록**
    - 굉장히 큰 파일의 경우 하나의 인덱스 블록으로 모두 표현 불가능
        1. *linked scheme* : 마지막에 또 다른 인덱스 블록의 위치를 가리킴
        2. *multi-level index* : 인덱스가 또 다른 인덱스 블록을 가리킴

### UNIX 파일 시스템의 구조
![image](https://user-images.githubusercontent.com/46131688/114302930-58fcbe80-9b06-11eb-90a4-9fc98a08b1d5.png)

(사진 출처 - [티스토리](https://asfirstalways.tistory.com/145))

- Boot block
    - 부팅에 필요한 정보 (bootstrap loader)
    - 어떠한 파일 시스템이라도 0번 블록이 *boot block*
- Super block
    - 파일 시스템에 관한 총체적인 정보
        - 어디가 빈 공간인지, 어디까지가 inode list인지 등
- **Inode**
    - **파일 하나 당 inode 하나 할당**
    - 파일 이름을 제외한 파일의 모든 메타데이터 저장 (파일 이름은 data block에 저장)
- Data block
    - 파일의 실제 내용을 보관
    - directory file
        - *(파일 이름, inode 번호)*

### FAT File System
![image](https://user-images.githubusercontent.com/46131688/114302953-75006000-9b06-11eb-8038-87488f889fdf.png)

(사진 출처 - [티스토리](https://asfirstalways.tistory.com/145))

`FAT`은 linked allocation을 조금 변형한 것이다. linked allocation에서는 중간에 하나의 섹터가 bad sector 되면 그 뒤에 있는 파일들을 읽지 못한다거나 포인터의 저장으로 공간 효율성이 낮아지는 단점이 있었다. **다음 블록의 위치 정보를 디렉토리 파일에 저장하지 않고 FAT에 저장**해둠으로써 이러한 단점을 해결하고 *direct access* 또한 가능하다.

### Free-Space Management
- **Bit map or bit vector**
    - 해당 블록이 사용중인지 아닌지를 비트로 표현
        - *bit[i] = 0* : block[i] free
        - *bit[i] = 1* : block[i] occupied
    - bit map은 부가적인 공간을 필요로 함
    - 연속적인 n개의 *free block* 을 찾는 데 효과적
- **Linked list**
    - 모든 *free block* 들을 링크로 연결
    - 공간 낭비가 없지만 연속적인 빈 공간을 찾기는 어려움
- **Grouping**
    - indexed list 방법의 변형
- **Counting**
    - 프로그램들이 종종 여러 개의 연속적인 block을 할당하고 반납한다는 성질에 착안
    - 연속적인 *free block* 을 찾는 데 효과적

### Directory Implementation
- Linear list
    - *<file name, file metadata>* 의 리스트
    - 디렉토리 내에 파일이 있는지 찾기 위해서는 linear search 필요
    - 구현이 간단하지만 시간이 오래 걸려 비효율적
- Hash Table
    - linear list + hashing
    - 파일 이름을 이 파일의 linear list 위치로 바꾸어줌
    - search time을 없애서 효율적이지만 해시 특징 상 충돌 발생 가능

- 파일의 메타데이터 보관 위치
    - 디렉토리 내 직접 보관
    - 디렉토리에는 포인터를 두고 다른 곳에 보관
        - ex) inode, FAT 등
- 긴 파일 이름의 지원
    - *<file name, file metadata>* 의 리스트에서 각 엔트리는 일반적으로 고정된 크기
    - 파일 이름이 엔트리 길이보다 길어지는 경우 엔트리 마지막 부분에 포인터를 두는 방법
    - 이름의 나머지 부분은 동일한 디렉토리 파일 일부에 존재

### VFS and NFS
- VFS (Virtual File System)
    - 사용자가 다양한 파일 시스템에 대해 동일한 시스템콜 인터페이스로 접근하게 해주는 게층
- NFS (Network File System)
    - 네트워크를 통해 연결된 다른 파일 시스템에 접근하도록 해줌

### Page Cache and Buffer Cache
- *Page Cache*
    - 페이징 시스템에서 사용하는 페이지 프레임을 캐싱 관점에서 설명하는 용어
- *Memory-Mapped I/O*
    - 파일의 일정 부분을 메모리 영역에 매핑시킴
    - read, write 등의 시스템콜을 사용하는 것이 아닌 메모리에다가 읽고 씀
- *Buffer Cache*
    - 파일 시스템을 통한 I/O 연산은 메모리의 특정 영역인 buffer cache 사용
    - 파일 사용의 `locality` 활용
        - 한 번 읽어온 블록에 대한 후속 요청 시 디스크에 접근하지 않고 buffer cache에서 즉시 전달
    - replacement algorithm 필요 (LRU, LFU 등)
- *Unified Buffer Cache*
    - 최근 운영체제에서는 buffer cache가 page cache에 통합됨
    - 즉 **buffer cache도 페이지 단위로 관리**


---

#### 참고
- [KOCW - 운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
