## 운영체제 1일차


* System Call 보다 API의 사용이 권장되는 이유
1. Compatibility
Linux 뿐만아니라 FreeBSD에서 동작하도록 하기 위해서 운영체제에 따라 달라지는 시스템콜보다 POSIX API의 사용

|Application||Application|
|:---:|:---:|:---:|
|POSIX|    |POSIX|
|LINUX|    |FreeBSD|

윈도우 같은 경우에는 Win32 API에서 프로그래밍을 해야한다

cygwin - Win32 위에 올라가며, 윈도우에서 POSIX 코드를 수행할 수 있게 해준다
        그러나 통과해야하는 계층이 늘어나 속도가 느리다



2. Information Hiding
process file, message queue 등 OS의 내부적인 구조를 알고 있지 않은 상태에서 앱개발



우리는 System Call에 대해 알아본다

System call : open() - user mode
-> kernel model
  -> os kernel 내 구현된 커널 코드 실행

``printf("");`` 는 __POSIX API__ 에 정의된 함수이며 실행되기 위해 ``write(fd,..)`` __System Call__ 을
호출한다.

System Call은 자신만의 번호를 갖는다
```c
/* This file contains the system call number */
#define __NR_restart_syscall 0
#define __NR_exit 1
#define __NR_fork 2
#define __NR_read 3
#define __NR_write 4
#define __NR_open 5
...
```

컴퓨터가 부팅되면 System Call Table이 메모리 상에 만들어진다

```c
Entry(sys_call_table)
  .long sys_restart_syscall
  .long sys_exit
  .long sys_fork
  .long sys_read
  .long sys_write
  .long sys_open

```

각각의 system call entry는 해당 system call 동작이 정의된 코드를 가르킨다.  
함수 포인터가 코드를 포인트 하고 있다.



int read(int fd, void* pbuf, int nBytes);

#define __NR_read 3
__syscall3(int, __NR_read, int, fd, void*, pBuf, int, nBytes)

![이미지](https://www.linux.it/~rubini/docs/ksys/ksys-figure1.png)
<div style="text-align:center"> https://www.linux.it/~rubini/docs/ksys/ </div>

* user mode
1. read 함수 호출
2. EAX register에 system call 번호 저장
3. interrupt 0x80 명령어로 exception 발생
* kernel mode
4. EAX가 syscall table의 배열에 있는 인덱스로 사용됨 (sys_read 호출)
5. sys_read 실행
6. 다시 usermod로 복귀


* interrupt 0x80
  - 프로그램에 오류가 발생한 경우 (메모리 참조 오류 등)
  - user mode에서 kernel mode로 변환하기 위해 TRAP 호출

* 변수들을 커널 모드 시스템 함수에 전달하는 방법
  - CPU Register -> 레지스터 수가 적어서 다른 방법 필요
  - CPU Register에 변수들이 담긴 블록 위치를 전달
    - 스택에 블록이 저장된다


코드 부분에서는 def로 시작하는 문장과 class로 시작하는 문장만 사용하면 될 거 같기도하고..;;

그러면 markdown에서는 ##로 시작하는 부분만 추출?

"""## >""" 등으로 시작하는 것만 추출

## 2일차

* 시스템 프로그램이란?

개발자가 프로그램을 개발하고 실행하는데 사용하는 환경으로, 다음 동작들을 지원한다.
- 파일 변경


- 상태 정보


- 프로그래밍 언어 지원
  컴파일러, 어셈블러, 디버거, 인터프리터

- 프로그램 로드 및 실행
  프로그램 링크

- 통신


### monolithic architecture -> MS-DOS
  접근하면 안될 정보들을 잘못하여 접근하게 되면 서버가 다운될 수 있다.
  ![이미지](https://t1.daumcdn.net/cfile/tistory/99FA733359C353CE2B)


### Layered Approach
Layer N -> Layer N-1 -> ... -> layer 1 -> layer 0 의 순서로 진행된다.

Layer N이 유저 인터페이스이고, Layer 0이 하드웨어를 뜻한다.

up call 은 불가능하며 `down call` 만 가능하다

장점 - 만들고 디버깅하는데 편리하다

각각의 프로그래머가 각각의 계층만 프로그래밍하면 된다.

아래 계층이 제공하는 환경을 바로 위 계층에서 사용한다.

단점
  - 계층들에 각각의 역할들을 분리하여 나누어주는 것이 어렵다
  - 계층이 많아질 수록 CPU 부하가 증가한다


 - 계층이 추가된는 경우 위에 계층의 코드들을 수정해주어야 하기 때문에 안정성에 문제 발생



## Microkernel structure
1. prcess management
2. memory management
3. synchronization
4. IPC

외의 memory server, process server, display server, network server, file  server 등을 user mode에서 관리

![이미지](https://t1.daumcdn.net/cfile/tistory/999A543359C353CF33)

조금 더 쉽게 기능들을 추가할 수 있다.


## Modules
module + layered approach

- device driver
필요할 때마다 OS에 device driver가 동적으로 등록된다.

굳이 사용할 필요 없는 device driver을 추가할 필요 없다


- Solaris Modular approach

![이미지](https://www.researchgate.net/profile/Eduardo-Fernandez-40/publication/268397726/figure/fig3/AS:669563205201931@1536647716657/The-modular-design-of-the-Solaris-10-Operating-System-Sil03.png)

예로,
NTFS가 기본적으로 탑재되어 있는 상태에서 EXT3가 필요할 때 동적으로 파일 시스템을 추가해 줄 수 있다.



## Virtual Machine
os와 하드웨어를 새로운 하드웨어처럼 보여지게하는 방법

- vmware
![이미지](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT_QxC320c6R9x_TrhnE8inI09VEGujX8zscw&usqp=CAU)

하나의 하드웨어 위에 OS가 배치된다.
 가장적인 CPU, 메모리, 디바이스 위에 guest OS가 탑재된다

- java virtual machine

![이미지](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter2/16_10_Java_VM.jpg)

class loader : .class 파일들을 로드
java interpreter : Java Bytecode를 CPU 명령어로 바꾸어 준다

코드를 개발하기는 쉽지만, 속도에 문제가 생기게된다.


## System Booting 과정

one-step
- Bootstrap program (BIOS)
  - DRAM과 기타 디바이스들에 문제가 있는지 검사
  - 디스크에 있는 kernel을 메모리 상에 로드 한다



two-step booting process
  - Master Boot Record (boot block) 를 메모리 상에 로드
  - 다양한 OS 선택이 가능

  - boot block
    - LILO (LInux LOader)
      - master boot record에서 선택된 OS의 커널 이미지를 실행시킨다.
