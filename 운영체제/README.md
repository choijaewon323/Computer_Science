# 운영체제 정리

## 인터럽트 (Interrupt)
- 프로세서 이용성을 높이기 위해 제공
- I/O 수행 시 프로세서를 기다리게 하는 것이 아닌 다른 프로세스를 실행시키고 I/O 작업이 끝나면 프로세서에게 알려주는 방식

- 비동기 인터럽트 (인터럽트)
  - 하드웨어에 의해 발생
  - 외부 인터럽트 : 주변 장치에 의해 발생
  - CPU 클락과 비동기적
- 동기 인터럽트 (exception)
  - CPU에 의해 발생
  - 시스템 콜 포함
  - CPU 클락과 동기적

## Resource protection and System Call
### 1. 용어 : 커널과 유저 프로그램
- Kernel
	- 메모리에 있는 OS 부분
	- 시스템 콜을 통해 서비스 제공
- Utility
	- 시스템 유틸리티, 유저 유틸리티 구분
	- 디스크에 있는 OS 부분
- Shell
	- 특별 유틸리티, 커맨트 컨트롤 주 목적

### 2. 듀얼 모드 오퍼레이션
- 하드웨어 지원을 통해 여러 모드를 구별함
- 커널 모드 : 모든 opcode 실행 가능, 메모리 어디든 접근 가능, 레지스터 어디든 접근 가능
- 유저 모드 : privileged opcode 실행 불가능, 내 메모리만 접근 가능, 스페셜 레지스터 불가능
- 모드 스위치 : 인터럽트나 예외, 시스템 콜이 발생하면 하드웨어가 커널 모드로 스위칭

### 3. 리소스 보호
##### 3.1 CPU 보호
- CPU 보호 메커니즘
	- CPU 독점 막음
	- 타이머를 통해 수행
- 시스템 타이머
	- 일정 시간 이후 인터럽트 발생
	- 타이머 인터럽트가 발생하면 컨트롤이 OS로 돌아감
	- 타이머는 privileged -> OS만 사용 가능
##### 3.2 메모리 보호
- Memory Management Unit (MMU)
	- 메모리 논리 주소를 물리 주소로 변환
	- 메모리 보호, 캐시 조절 등도 수행
##### 3.3 I/O 보호
- Privileged I/O Instructions
	- 모든 I/O 인스트럭션은 privileged
	- 유저는 I/O 직접 접근 불가능
	- 시스템 콜을 통해서만 접근 가능
### 4. System Call
- 목적
	- 시스템 리소스 보호 : 모드 구분
	- 신뢰성 : 무한 루프, 메모리 침범 등 버그가 많은 프로그램으로부터 보호
	- 보안 : permission 없이는 OS 읽기, 쓰기 불가능하게

## Process Description and Control 1

### 1. 용어 : 프로그램과 프로세스
- Program : passive entity – 디스크에 저장되어있는 상태
- Process : active entity – 메모리에 로드되어 돌아가고 있는 상태
- 하나의 프로그램은 여러 프로세스가 될 수 있음
- 프로세스의 필수 요소 : 코드 + 데이터
- 콜 스택 : 프로그램의 서브루틴 정보 저장하는 자료구조
	- 프로세서가 call 을 하면 리턴 주소를 스택에 push
	- ,매개변수와 리턴 매개변수도 push

### 2. Process Description
- System-level Context
	- OS가 프로세스 관리에 필요한 정보름 담음 -> Process Control Block (PCB)
- User-level Context (memory)
	- 유저 프로그램의 기초 구성요소를 포함
	- text, data, stack
- Hardware Context (register)
	- register의 정보들로 구성
	- PC, SP, PSW + General-purpose registers

### 3. Process Image
- OS가 각 프로세스마다 description으로서 유지하는 것
- 프로그램 코드 + 데이터 + 스택 + PCB

### 4. Process Control Block
- OS가 필요한 프로세스의 정보 포함
- multiprocessing에 필수적
- 구성 요소
	- Process Identification : 프로세스 ID
	- Processor state information : 레지스터의 정보들
	- Process control information : 프로세스 상태, 스케줄링 정보 등
### 5. Process States
- seven-state process model 기준
	- New : 새 프로세스 생성
	- Ready : 실행될 준비가 된 상태
	- Running : CPU에서 돌아가고 있는 상태
	- Blocked : I/O 등으로 잠깐 막힌 상태
	- Blocked/Suspend : Blocked 상태에서 필요없어서 disk로 swap된 상태
	- Ready/Suspend : Ready 상태에서 필요없어서 disk로 swap된 상태
	- Exit : 프로세스 release 된 상태

## Process Description and Control 2
### 1. 용어 : 모드 스위치와 프로세스 스위치
- Mode Switch
	- 유저 모드와 커널 모드를 바꾸는 것
	- 프로세스의 상태를 바꾸지 않고 발생 가능
	- 이 경우 오버헤드 낮음
- Process Switch (Context Switch)
	- 프로세스를 바꾸는 것
		- processor의 context 저장
		- 현재 process의 PCB 업데이트
		- 현재 프로세스의 PCB를 큐에 삽입
		- 다른 프로세스 선택 (스케줄링)
		- 선택된 프로세스의 PCB 업데이트 (Running 상태로)
		- TLB 업데이트
		- 선택된 프로세스로 processor의 context 복구
	 오버헤드 높음

### 2. OS의 실행 모델
- Nonprocess Kernel
	- 옛날 방식
	- 커널이 분리된 엔티티로 존재
- Execution within User Processes
	- 모든 OS functions이 유저 프로세스의 context 안에 존재
	- 모든 프로세스 이미지에 커널의 프로그램, 데이터 스택 포함
	- 커널 모드에서는 커널 스택 사용
- Process-Based OS
	- 중요하지 않은 OS function만 분리된 프로세스로 존재

### 3. 커널 스택
- 모든 프로세스는 2개의 스택 소유 – User space stack, Kernel space stack
- 이점
	- 유저 스택이 corrupt 되어도 커널 실행 가능
	-> 버퍼 오버플로우 같은 공격에도 커널이 영향받지 않음

### 4. 프로세스 스위치
- 모드 스위치 : 소프트웨어로 구현
- 이전 process의 PCB를 저장하고, 다음 process의 PCB를 복구하는게 하드웨어로 구현

### 5. 프로세스 생성과 종료(termination)
- Directed Process Creation
	- 새로운 메모리 공간 할당
	- 메모리에 a.out 적재
	- PCB 초기화
	- 새로운 프로세스 시작
	 이는 /sbin/init 과 같은 super parent에서만 수행
- Cloning
	- 부모 프로세스의 완벽한 복제로 자식 프로세스 생성
	- text, data, stack, PCB 복제, PID 수정 이루어짐
	- fork()로 수행
- Replacing
	- PID는 바뀌지 않고 text, data, stack, PCB만 바뀜
- Copy on Write (COW)
	- write 전에는 메모리의 page 공유
	- write 하면 (change 되면) page 복사본 생성 -> 부모와 자식이 다른 page 가지도록
- Process Termination
	- Voluntary termination : exit(status)
		- 프로세스 자신이 직접 종료
	- Involuntary termination
		- 다른 프로세스나 OS에 의한 종료
		- Kill() or abort()
- Zombie
	- exit()으로 종료하면 커널은 PCB를 바로 제거하지 않음
	- 더 이상 실행하지 않는데도 PCB가 남아있음
	- 부모로부터 reap 될때까지 terminated state로 남아있음
	-> 좀비 상태
- Reaping
	- 종료 상태가 부모 프로세스한테 넘겨지는것
	- wait() 을 통해 수행
- Orphan
	- 자식보다 부모 프로세스가 먼저 종료
	- super parent (/sbin/init)이 모두 reap 해감
  

## Process Scheduling 1

### 1. 프로세스 스케줄링의 종류
- Long-Term 스케줄링 (job scheduler)
	- 어떤 프로그램을 선택할지 (New, Exit)
- Medium-Term 스케줄링 (swapper)
	- 어떤 프로세스를 골라 상태를 바꿀지 (Blocked/Suspend, Ready/Suspend)
- Short-Term 스케줄링 (CPU scheduler)
	- 어떤 프로세스를 골라 CPU에 돌릴지 (Running, Ready, Blocked)
- I/O 스케줄링
	- 어떤 프로세스에게 I/O 작업을 수행할지

### 2. 스케줄링 알고리즘
##### 2.1 FCFS (FIFO)
	- 가장 오래 기다린 프로세스 선택
	- 비선점 – 중간에 가로채지 않음
	- 간단, but convoy effect 야기(긴 프로세스가 너무 많이 잡아먹음)
##### 2.2 SPN
	- 실행 시간이 가장 짧은 걸 선택
	- 비선점
	- turnaround 시간 향상 but 긴 프로세스에게 starvation 발생(스케줄링을 오래 받지 못함)
	- 각 프로세스의 실행 시간 알아내는거 자체도 어려움
##### 2.3 Round-Robin
	- FIFO의 선점 방식
	- 타임 퀀텀 q가 크면 : FIFO랑 다를게 없음
	- 작으면 : context switch에 대한 오버헤드가 너무 커짐
##### 2.4 SRT
	- SPN의 선점 방식
	- turnaround 시간 성능 좋음
	- 오버헤드 높음
	- starvation이 발생할 수도 있음
##### 2.5 HRRN
	- 가장 높은 반응비를 가진 프로세스 선택
	- 반응비 : 표준화된 turnaround time ( R = (q + s) / s )
	- 비선점 방식
	- 서비스 타임을 측정하는게 어려움


## Process Scheduling 2

### 1. Terminologies
- Burst (time)
	- CPU Burst : CPU가 operation을 실행하는 시간
	- I/O Burst : CPU가 I/O를 기다리는 시간

### 2. Scheduling Criteria
- Scheduling Metrics
	- CPU Utilization : CPU를 가능한 많이 사용하도록
	- Throughput : 단위 시간당 완료한 작업의 양
	- Turnaround Time : 특정 프로세스를 실행하는 시간의 양
	- Response Time : 응답까지 걸린 시간

### 3. Virtual Round Robin
- 긴 프로세스의 starvation을 극복하기 위해 고안
- Auxiliary Queue를 두어 ready queue 보다 우선순위를 줌
- I/O를 끝마친 프로세스는 Auxiliary queue로 들어감


### 4. Multi-Level Feedback Queues
- 여러 단계의 ready queue를 둠
- 높은 우선순위의 큐부터 실행
- 블로킹 없이 타임 슬라이스를 모두 사용한 프로세스는 우선순위가 낮아짐
- 오래 동작하는 작업은 starvation 야기할 수 있음

### Multiprocessor Scheduling
- HW issue : cache affinity
	- 같은 job -> 같은 CPU에
- SW issue : concurrency(동시성)
	- mutual exclusion 필요

### 5. Single queue multiprocessor scheduling (SQMS)
- 싱글 큐에서 next job 선택
- 장점 : 구현 쉬움 / 단점 : cache affinity 없음, scalability(확장성) 제약

### 6. Multi queue multiprocessor scheduling (MQMS)
- CPU 하나당 하나의 큐
- 장점 : 확장성, cache affinity, 단점 : load imbalance – 어떤 CPU는 프로세스 계속 안들어옴

### O(1) scheduler
- 우선순위 적용
- 알고리즘
	1. 수행 가능한 task -> active run queue에 삽입 
	2. Task가 time slice 다 쓰면 expired run queue에 삽입 
	3. 만약 active run queue가 비어있으면 active와 expired 스왑

## Threads and synchronization

### 1. Process and Thread
- Thread : unit of execution (실행 단위)
- Process : unit of resource ownership (자원 소유자)
- Multithreading : 프로세스 하나에 다수의 실행 흐름, 같은 프로세스이므로 같은 자원 공유
- Concurrency(동시성) : interleaving – 교차로 배치하다
- Parallelism(병행성) : overlapping – 겹치다

### 2. 이점
- responsiveness(응답성) – blocked나 lengthy 작업에 상관없이 진행, overlapped 가능(동시에)
- economy of time – thread가 생성 및 스위치가 더 빠름
- resource sharing – thread는 이미 같은 데이터 영역 공유
- scalability(확장성) on multicore – 프로세스가 병행적으로 몇 개의 CPU에 실행 가능

### 3. 멀티쓰레딩
- Amdahl’s law
	- 의존성 존재 -> 프로세서 2개 추가돼도 2배로 빨라지지는 않음
	- 코어 수 동일 -> 쓰레드를 늘릴수록 성능이 안 좋아짐 why? 스위치 비용 증가
- Programming challenges
	- identifying tasks – task의 의존성 없어야함
	- balance – task가 같은 양의 작업을 수행해야 함
	- data dependency – 데이터가 의존적이라면 동기화 필요

### 4. Multithreaded Process Model
- 각 쓰레드마다 자신의 스택 존재 (유저, 커널 둘다)
- 쓰레드마다 TCB 필요 - PCB보다 훨씬 가벼움

### 5. 쓰레드 구현
##### 5.1 유저 레벨 쓰레드 (ULT)
– application이 쓰레드 관리 -> 빠름(kernel 안 거침) but 안정성 문제(하나 block, 전체 block)
##### 5.2 커널 레벨 쓰레드 (KLT)
– kernel이 쓰레드 관리 – 안정성 높음 but 느림
##### 5.3 혼합 방식
– 평소는 ULT, block 되면 kernel 사용


## Synchronization 1

### 1. Race Condition
- Race condition - 동시에 공유 자원 접근

### 2. Critical Section & Mutual Exclusion
- Critical resource - 한번에 하나만 사용 가능한 공유 자원
- Critical section - 임계 자원에 접근하는 코드
- Mutual exclusion - 하나의 쓰레드만 그 자원을 사용하게 하는 것
- Synchronization – race condition을 막기 위해 critical section을 mutual exclusion시키는 기법
- 동기화의 분류 
	1. Mutual exclusion 
	2. Condition synchronization
- Critical section problem 조건
	1. mutual exclusion 
	2. Progress – 아무도 임계영역에 없으면 들어갈 수 있게 
	3. Bounded waiting – 무한정 기다리면 안됨

### 3. Naïve approach
- if문 하나로 flag를 만들어 lock
- 제대로 동작 x

### 4. Peterson’s Algorithm
```
do {
flag[0] = TRUE;
turn = 1;
While ( flag[1] && turn == 1);
CRITICAL SECTION
flag[0] = FALSE;
REMAINDER SECTION
} while (TRUE);
```

### 5. HW support – Disabling Interrupt, TAS and CAS
- Disabling Interrupt
	- 임계 영역 안에서 인터럽트 비활성화
	- 장점 : 간단
	- 단점 : 인터럽트의 장점을 모두 없앰
- Test-And-Set (TAS)
	- atomic한 instruction으로 제공
	- Test : 이전 lock 값 체크
	- Set : 새로운 lock 값 설정
- Compare And Swap (CAS)
	- 주어진 값으로 해당 메모리 영역 비교, 같으면 수정 가능하게

### 6. Spin Lock
- 하나의 쓰레드만 임계 영역에 들어올 수 있도록 함
- 다른 쓰레드는 들어올 수 없어 낭비됨

### 7. Binary Semaphore
- Semaphore
	- Semaphore S : 정수 값, 이용 가능한 리소스의 수
	- semWait() : 값 1 감소 (P)
	- setSignal() : 값 1 증가 (V)
	- busy waiting을 피하기 위해 대기 큐를 사용
- Binary Semaphore (mutex lock) : 세마포어 값이 0과 1밖에 안됨

### 8. Counting Semaphore
- 세마포어 값이 1 이상 – 여러 개 들어올 수 있음


## Synchronization 2

### 1. Condition Synchronization
- Mutual Exclusion은 하나의 스레드만 출입 가능
- Condition Synchronization 은 여러 스레드를 동기화할 수 있음
- 상태를 만족할 때까지 대기


### 2. Condition Variables
- Condition Variable (CV) : 조건을 기다리는 대기 큐
- wait() : 특정 조건을 기다리는 것
- signal() : 쓰레드가 대기 중인 쓰레드 중 하나를 깨움
	- broadcast() : 대기 중인 쓰레드 모두 깨움

### 3. Monitor in Java
- Monitor 로 만든 클래스 안에서 프로시저 호출 -> monitor가 동기화 제공

### 4. Producer and Consumer Problem
- 주의점 (Contition Variable을 통한 해결책)
	- state를 체크하는건 항상 while 로 re-check를 해줘야함 (Mesa semantics)
	- 누구를 깨워야하는지 모름 – 소비자가 소비자를 깨울수도 있음
		-> Empty, Fill condition 변수를 두어 확인
	- Empty : 비워지기를 기다림(생산자), Fill : 채워지기를 기다림(소비자)
- 세마포어를 이용한 해결
	- 생산자 : P(empty) -> P(m) -> put -> V(m) -> V(fill)
	- 소비자 : P(fill) -> P(m) -> get -> V(m) -> V(empty)


## Deadlocks

### 1. Deadlock
- 서로의 자원을 잡고 상대방의 자원을 기다림 -> 절대 일어날 수 없음
- 자원 요구의 충돌

### 2. Resource allocation graphs
- P -> R : 자원을 요청
- R -> P : P가 자원을 가지고 있는 것
- 사이클 존재 X : 데드락 발생하지 않음
- 사이클 존재 O
	- 리소스마다 단 하나의 프로세스가 존재 -> 데드락 발생
	- 리소스마다 여러 프로세스 존재 -> 데드락 발생 X

### 3. Dining Philosophers Problem
- 원탁 테이블에 5명이 있고 5개의 포크 존재
- 한명당 2개의 포크가 필요
- 두 사람 이상이 같은 포크 동시에 사용 불가능
```
void philosopher (int i)
{
	while (true)
	{
		think();
		wait (fork[i]);
		wait (fork [(i+1) mod 5]);
		eat();
		signal(fork [(i+1) mod 5]);
		signal(fork[i]);
	}	
}
```

### 4. The Conditions for Deadlock
- Mutual exclusion : 하나의 프로세스만이 리소스 사용 가능
- No Preemption : 리소스가 비선점
- Hold and Wait : 프로세스가 리소스를 가진 상태로 다른 프로세스가 소유한 리소스 대기
- Circular Wait : 리소스 할당 그래프에서 사이클 발생
- 위 4가지 조건일 경우 데드락 발생
- 데드락 처리 방법
	- Deadlock Prevention : 데드락 발생 조건을 제거
	- Deadlock Avoidance : 조건 제거 X but 상태를 파악하여 할당 결정

### 5. Dining Philosophers Solution
- 1. 테이블에 4명만 앉을 수 있게
- 2. 포크를 한번에 줌 – No Hold and wait
- 3. 포크를 주는 순서를 정함 – No Circular Wait 


### 6. Deadlock Prevention
- 데드락 발생시키는 4가지 조건중 하나를 제거함
- Hold and wait – 요청하는 리소스들을 무조건 한번에 줌
- Circular Wait – 리소스 할당 순서를 정하기


### 7. Deadlock Avoidance
##### 7.1 Resource-Allocation-Graph Algorithm
- 데드락이 발생하지 않는 safe state를 구함
- 미래의 프로세스 요청에 대한 지식을 요구함
- but 각 리소스의 여러 인스턴스가 존재할 경우 불가능
- Process Initiation Denial : 프로세스 할당 거부
	- Rj >= Cij >= Aij
	- R – 자원의 양, C – 최대로 요구하는 양, A – 지금 할당된 양
	- Rj >= C(n+1)j + 시그마cij 면 새 프로세스 시작
- Resource Allocation Denial : 자원 할당 거부

##### 7.2 Banker’s Algorithm
- Resource Allocation Denial 예시
- Ci – Ai <= V 면 할당


## Memory management

### 1. Terminology
- static allocation : 프로그램 실행 전
- dynamic allocation : 런타임
- 동적 할당 필요 이유 : 얼마나 많은 메모리가 필요한지 정확히 알 수 없기 때문

### 2. Memory Management Requirements
- Relocation
	- 프로그램은 항상 메모리 안에서 움직일 수 있음
	- 재할당이 반드시 필요
- Protection
	- 프로세스는 반드시 할당된 영역 안에서만 참조해야함
- Sharing
	- 메모리의 일부분은 여러 프로세스에서 접근할 수 있음
	- 복사본을 모두 가지고 있는 것보다 하나의 복사본을 공유하는게 효율적
- Logical Organization
	- 실제 물리적 메모리와 프로그램 구조는 다름
- Physical Organization
	- 메모리는 적어도 두단계 이상으로 조직되어 있음

### 3. Types of Addresses
- symbolic address – 소스 코드 안에서 사용되는 주소 ex) 변수명, 상수명
- logical address (virtual address, relative address) 
	– 프로세스 안에서의 위치
	- complie 시 symbolic -> logical
- physical address (absolute address)
	- 메인 메모리 안에서의 실제 주소
	- loader 가 logical -> physical



### 4. Continuous allocation
- 연속적인 할당

##### 4.1 Fixed Partitioning
- 고정 분할 방식
- Equal Size
	- 메모리를 고정된 사이즈로 나눔
	- 장점
		- 구현 쉬움
	- 단점
		- 프로그램의 크기와 상관 없이 분할된 크기 전체를 사용해야함
		- 분할 크기보다 작은 프로그램의 경우, internal fragmentation 발생
- Unequal Size
	- 2M, 5M, 7M, 9M… 와 같은 방식으로 점점 크게 분할
	- 각 파티션에 대한 큐 : 각 파티션에 맞게 프로세스를 큐에 푸시 -> 내부 단편화 최소화
	- 모든 프로세스에 대한 하나의 큐 : 모든 파티션 중 가장 작은 것 -> 유연성 증가
	- 장점
		- 구현 쉬움
		- OS 오버헤드 감소
	- 단점
		- 역시 내부 단편화 발생
		- 파티션의 수가 시스템 한계를 결정 -> 멀티프로그래밍에 불리
		- 파티션보다 훨씬 큰 프로그램이 있을 수 있음

##### 4.2 Dynamic Partitioning
- 동적 분할 방식
- 파티션의 크기를 제한하지 않음
- placement 알고리즘
	- first-fit : 메모리 맨 위부터 가장 첫번째로 이용 가능한 블록에 할당
	- next-fit : 가장 최근의 할당 위치부터 탐색 시작
	- best-fit : 요청 사이즈와 가장 가까운 블록에 할당 – 성능 젤 안좋음
- 장점
	- 내부 단편화 없음
	- 메모리 효용성 상승
- 단점
	- 외부 단편화 : 블록들 사이에 작은 공간들만 남아 할당 불가능
	- 메모리 compaction : 단편들을 모으는 작업을 추가로 해야함

##### 4.3 Buddy System
- 메모리를 2^k word 로 쪼갬
- 2^(u-1) < s <= 2^u 를 만족할 경우 전체 블록 할당
- 그렇지 않으면 2^(u-1)로 파티션 쪼갬
- 한마디로 이용 가능한 가장 작은 2의 승수를 구해서 할당


### 5. Non-continuous allocation
- 분산 적재

##### 5.1 Paging
- 프로세스를 고정 사이즈로 쪼갬 – 그 단위가 page
- 메모리 역시 고정 사이즈로 쪼갬 – frame
- page table : 페이지에 해당하는 frame을 매핑해놓은 테이블
- 장점
	- 외부 단편화는 없음
- 단점
	- 내부 단편화 but 매우 적음

##### 5.2 Segmentation
- address space 를 의미있는 단위인 segment 로 쪼갬 ex) code segment, stack segment 등
- 프로세스를 같지 않은 사이즈로 분리 – 세그먼트 단위로
- segment table : 각 segment와 실제 메모리에서의 위치를 매핑
- 장점
	- 내부 단편화를 제거
- 단점
	- 외부 단편화


## Virtual memory

### 1. Motivation and Rational
- Motivation
	- 모든 페이지나 세그먼트가 메모리에 올라갈 필요가 없음
- Rational
	- locality 가 존재 : 프로세스는 일부 코드를 굉장히 많이 사용
	- virtual memory 를 사용하면 더 효율적
### 2. Fetch Policy
- 메모리에 언제 페이지를 가져다 놓을지에 대한 정책

##### 2.1 Demand paging
- 필요할 때 적재	
- 처음에는 page fault 발생

##### 2.2 Prepaging
- 필요할 거 같은걸 미리 적재
- 미래를 예측하기 힘듦

### 3. Replacement Policy
- 메모리가 꽉 찼을 때 어떤 페이지를 대체할지에 대한 정책
##### 3.1 Optimal
- 미래를 전부 알고있는 가정하에 작동하는 알고리즘
- 구현 불가능 but 다른 알고리즘에 비교하는 기준으로 사용
	

##### 3.2 First-in, first-out (FIFO)
- 가장 오래된 page를 교체
- 장점 : 구현 쉬움
- 단점
	- 자주 사용되는 페이지는 주로 오래된 경우가 많아 자주 사용되어도 교체될 수 있음
	- Belady’s anomaly : frame 수가 늘어나면 page fault 가 줄어들어야 하는데 그렇지 않은 것

##### 3.3 Least recently used (LRU)
- 가장 오래 참조되지 않은 페이지 교체
- 장점
	- optimal에 근접
	- Belady’s anomaly 를 겪지 않음
- 단점
	- 구현이 어려움

##### 3.4 Clock (Second Chance)
- 프레임들을 원형으로 놓고 각 프레임에 참조 비트를 놓음
- 프레임들을 돌면서 참조 비트가 0인 곳을 교체
- 1을 만나면 0으로 바꿈

##### 3.5 Enhanced Second Chance
- Clock 알고리즘에서 수정 비트를 하나 더 놓음
- 두 비트 쌍으로 우선순위 결정
- 가장 낮은 우선순위인 프레임을 교체


### 4. Resident Set Policy
- 각 프로세스에 얼마나 많은 페이지를 할당할 것인지에 대한 정책
- Fixed-allocation policy : 페이지 부족하면 자신의 페이지 바꿈
- Variable-allocation policy : 페이지 부족하면 다른 페이지 뺏어옴

### 5. Why Virtual Memory?
- 가상 메모리 : 크고 빠른 메모리를 가지고 있다는 illusion 제공
- 이점
	- 더 많은 프로세스를 메모리에 적재 가능
	- 프로세스가 메인 메모리보다 더 커질 수 있음
	-> 논리 주소 공간이 물리 주소 공간보다 커질 수 있음
	- I/O 작업이 줄어듦


### 6. Hardware and Control Structures for VM
- Page Table entry
	- 페이지 테이블은 메모리에 있음
	- Valid bit, Dirty bit 가 추가로 제공되어야함
- TLB
	- 페이지 테이블에 대한 캐시 – 하드웨어로 제공
	- 입력으로 page 숫자를 받고, 출력으로 frame 번호를 리턴
	- Hit : frame 즉시 리턴
	- Miss : 페이지 테이블 체크
		- 페이지 테이블 Hit : frame 번호 리턴, TLB 에 적재
		- 페이지 테이블 miss : page fault

### 7. Virtual Memory Issues
- page size
	- 커지면 장점 : 페이지 테이블 자체가 작아짐 – 자원 아낌
	- 단점 : 내부 단편화 발생, 메모리 효용성 떨어짐
- faster translation
	- TLB : Context Switch 가 발생하면 모든 내용이 의미없음 – 다른 프로세스이므로
	- 해결
		1. Flush : 모든 valid bit 0으로 – miss 가 많이 발생
		2. ASID (Address Space Identifier) : 프로세스마다 ID 부여하여 다르게 취급
- smaller tables
	- 페이지 테이블이 너무 커서 메모리 차지 많이 함
	- 해결
		1. Combined paging and segmentation
			- 세그먼트 테이블의 페이징화
			- 주소 공간을 세그먼트로 나눔
			- 각 세그먼트를 고정 크기의 페이지로 나눔
			- 장점 : 메모리 오버헤드 감소, 외부 단편화 없음
			- 단점 : 내부 단편화 발생 가능, 복잡한 하드웨어 지원 필요
		2. Hierarchical Paging
			- 페이지 테이블의 페이징화
			- 장점 : 페이지 테이블이 연속적일 필요 없음, 낭비 최소화
			- 단점 : 추가적인 load 발생
		3. Inverted Page Tables
			- 페이지가 아닌 frame을 엔트리로
			- frame number를 인덱스로 사용
			- 한 시스템에 하나의 페이지 테이블
			- 장점 : 메모리 공간 더 확보 가능
			- 단점 : 한번에 못찾고 검색 필요
