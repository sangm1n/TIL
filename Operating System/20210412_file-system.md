> *"KOCW - 반효경 교수님의 운영체제"* 를 듣고 정리한 내용입니다.

## File Systems
### File and File System
- **File**
    - A named collection of related information
    - **일반적으로 비휘발성의 보조기억장치에 저장** (하드디스크)
    - 운영체제는 다양한 저장 장치를 *file* 이라는 동일한 논리적 단위로 볼 수 있게 해줌
    - operation
        - create, read, write, reposition (lseek), delete, open, close 등
- **File attribute** (= `metadata`)
    - 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
        - 파일 이름, 유형, 저장된 위치, 접근 권한, 소유자 등
- **File system**
    - 운영체제에서 파일을 관리하는 부분
    - 파일 및 메타데이터, 디렉토리 정보 등을 관리
    - 파일의 저장 방법 결정

### Directory and Logical Disk
- **Directory**
    - 파일의 메타데이터 중 일부를 보관하고 있는 특별한 파일
    - 그 디렉토리에 속한 파일 이름 및 메타데이터
- **Partition** (= `logical disk`)
    - 운영체제가 바라보는 디스크
    - 하나의 디스크 안에 여러 파티션을 두는 것이 일반적
    - 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
    - 각각의 파티션에 file system을 깔거나 swapping 등 다른 용도로 사용할 수 있음

### open()
- 파일의 메타데이터를 메모리에 올려놓는 것

![image](https://user-images.githubusercontent.com/46131688/114297050-6eafbb00-9ae9-11eb-9a6a-aebcf06e4fcb.png)

(사진 출처 - [KOCW 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323))

*open()* 도 시스템콜이기 때문에 CPU 제어권이 운영체제에게 넘어간다. 디렉토리의 계층적인 구조에 따라 각 파일의 메타데이터를 가져온다. 결과적으로 b의 메타데이터를 메모리에 올려놓으면 해당 메타데이터가 메모리 어디에 위치하는지에 대한 포인터 (**file descriptor**)을 반환한다.

그 후 *read()* 시 CPU가 다시 운영체제로 넘어간다. A의 PCB에서 file descriptor를 따라 b의 메타데이터로 가고, 메타데이터를 통해 b의 파일 내용을 읽어온다. 이때 읽어온 내용을 **사용자 프로그램한테 바로 주지 않고 커널 메모리로 읽어와 복사**해서 사용자 프로그램한테 전달해준다. 만약 다른 프로그램이 동일한 파일의 동일한 위치에 대해 read하면 굳이 디스크까지 접근하지 않고 운영체제가 미리 읽어둔 내용을 전달해줄 수 있는데 이것을 **버퍼 캐시**라고 한다.

### File Protection
- 각 파일에 대해 누구에게 어떤 유형의 접근 (read/write/execution)을 허락할 것인가 ?
- Access Control 방법
    1. Access Control Matrix
        - *sparse matrix* (희소 행렬) 형태가 되면 매트릭스 낭비가 심해져 연결리스트 형태를 고려
        1. **Access control list** : 파일별로 누구에게 어떤 접근 권한이 있는지 표시 (파일이 주체)
        2. **Capability list** : 사용자별로 자신이 접근 권한을 가진 파일 및 권한 표시 (사용자가 주체)
    2. Grouping
        - 일반적인 운영체제에서 사용하는 방식
        - 전체 user를 **owner / group / other** 세 그룹으로 구분
        - 각 파일에 대한 접근 권한을 9비트로 표현 가능
    3. Password
        - 파일마다 패스워드를 두는 방법
        1. 모든 접근 권한에 대해 하나의 패스워드 : all or nothing
        2. 접근 권한별 패스워드 : 암기 문제, 관리 문제

### Mounting 
- 서로 다른 파티션에 존재하는 파일 시스템을 접근 가능하게 해주는 것

![image](https://user-images.githubusercontent.com/46131688/114297600-64db8700-9aec-11eb-8e4e-bbd10098cd18.png)

(사진 출처 - [워드프레스](https://kkslinuxinfo.wordpress.com/2015/12/08/mounting-file-system/))

### Access Methods
1. 순차 접근 (sequential access)
    - 카세트 테이프를 사용하는 방식처럼 접근
    - 읽거나 쓰면 `offset` 자동 증가
2. 직접 접근 (direct access, random access)
    - LP 레코드판과 같은 방식으로 접근
    - 파일을 구성하는 레코드를 임의의 순서로 접근 가능

---

#### 참고
- [KOCW - 운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
