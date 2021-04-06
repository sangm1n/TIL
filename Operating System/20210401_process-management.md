> *"KOCW - 반효경 교수님의 운영체제"* 를 듣고 정리한 내용입니다.

## Process Management
### 프로세스 생성 (Process Creation)
- 부모 프로세스가 자식 프로세스 복제 생성 (모든 문맥)
    - 프로세스의 계층 구조 형성
    - `COW` (Copy-on-Write)
    - 사실 자식 프로세스가 생성될 때 바로 복제하는 것이 아니다. 부모 프로세스의 것을 공유하고 있다가 내용이 바뀌었을 때, 즉 *write* 시 복제한다.
- 프로세스는 자원을 필요로 함
    - 운영체제로부터 받거나 부모와 공유
- 자원의 공유
    1. 부모와 자식이 모든 자원을 공유하는 모델
    2. 일부를 공유하는 모델
    3. 전혀 공유하지 않는 모델
- 수행 (Execution)
    1. 부모와 자식이 공존하며 수행되는 모델
    2. 자식이 종료될 때까지 부모가 기다리는(wait) 모델
- 주소 공간 (Address space)
    - 자식 프로세스는 부모 프로세스의 공간을 복사 (binary and OS data)
    - 자식 프로세스는 복제된 공간에 새로운 프로그램을 올림
- 유닉스의 예
    - *fork()* : **부모 프로세스를 복제**
    - *exec()* : **새로운 프로그램을 덮어씌우기**
    
    둘 다 시스템콜이기 때문에 운영체제를 통해서만 생성이 가능하다.

### 프로세스 종료 (Process Termination)
프로세스 세계에서는 부모보다 자식이 먼저 죽는 것이 원칙이다.

- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (exit) → **자발적 종료**
    1. 자식 프로세스가 부모 프로세스에게 output data를 보냄
    2. 프로세스의 각종 자원들이 운영체제에게 반납됨
- 부모 프로세스가 자식의 수행을 종료시킴 (abort) → **비자발적 종료**
    1. 자식이 할당 자원의 한계치를 넘어선 경우
    2. 자식에게 할당된 태스트가 더이상 필요하지 않은 경우
    3. 부모가 종료(exit)하는 경우
        - 단계적인 종료

### fork() 시스템콜
- A process is created by the *fork()* system call

```c
int main() { 
    int pid; 
    pid = fork(); 
    
    if (pid == 0) 
        printf("\n Hello, I am child !\n"); 
    else if (pid < 0) 
        printf("\n Hello, I am parent !\n");
 }
```

자식 프로세스는 부모 프로세스의 문맥을 그대로 복제한다. 부모 프로세스의 program counter도 그대로 가져오기 때문에 생성된 자식 프로세스는 *main()* 함수 처음부터 실행하는 것이 아닌 *fork()* 이후부터 실행된다.

- 부모 프로세스의 fork() return value : **자식 프로세스 PID** (Process ID)
- 자식 프로세스의 fork() return value : **0**

### exec() 시스템콜
- process can execute a different program by the exec() system call

```c
int main() { 
    int pid; 
    pid = fork(); 
    
    if (pid == 0) { 
        printf("\n Hello, I am child ! Now I'll run date\n"); 
        execlp("/bin/date", "/bin/date", (char *)0);
    } else if (pid > 0) 
        printf("\n Hello, I am parent !\n"); 
}
```

부모 프로세스가 자식 프로세스를 만들고, 자식 프로세스는 *execlp()* 함수를 만나 새로운 프로그램을 실행하여 되돌아오지 않는다. *exec()* 을 꼭 자식 프로세스를 만들고 할 필요는 없다.

### wait() 시스템콜
- 프로세스 A가 *wait()* 호출 시
    1. 커널은 자식 프로세스가 종료될 때까지 프로세스 A를 **sleep** 시킴 (Blocked)
    2. 자식 프로세스가 종료되면 커널은 프로세스 A를 깨움 (Ready)

### exit() 시스템콜
- 프로세스의 종료
    - 자발적 종료
        - 마지막 문장 수행 후 *exit()* 시스템콜을 통해 종료
- 비자발적 종료
    - 부모 프로세스가 자식 프로세스를 강제 종료
    - 키보드로 kill, break 등을 입력한 경우
    - 부모가 종료하는 경우

### 프로세스 간 협력
- 독립적 프로세스 (Independent process)
    - 프로세스는 각자의 주소 공간을 가진 채 수행되므로 원친적으로 다른 프로세스의 수행에 영향을 미치지 못함
- 협력 프로세스 (Cooperating process)
    - 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
- 프로세스 간 협력 메커니즘 (`IPC`, Interprocess Communication)
    1. 메시지를 전달하는 방법
        - message passing : 커널을 통해 메시지 전달
    2. 주소 공간을 공유하는 방법
        - shared memory : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하는 메커니즘

### Message Passing
1. Direct Communication
    - 통신하려는 프로세스의 이름을 명시적으로 표시
2. Indirect Communication
    - mailbox 혹은 port를 통해 메시지를 간접 전달

어쨌든 두 방법 모두 커널을 통해 메시지를 전달한다.

---

#### 참고
- [KOCW - 운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
