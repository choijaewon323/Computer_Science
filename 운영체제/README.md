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

## 캐시 (Cache)
- SRAM의 접근 속도와 DRAM의 크기를 가진 가상 메모리를 제공하는 메모리 기술
- Hit : 찾는 블럭이 캐시 안에 있을 때
- Miss : 찾는 블럭이 캐시 안에 없을 때

- 매핑 함수
  - 1. Direct Mapped Mapping : 각 블럭에 한 라인 할당
  - 2. N-way Set Associative Mapping : 각 블럭에 N개의 라인 할당
  
- 캐시 읽기 : 찾는 정보가 캐시에 있으면 Hit, 없으면 메인 메모리에서 가져와 캐시에 할당
- 캐시 쓰기
  - Write-through : 항상 캐시와 메모리에 둘다 씀 - 간단하지만 느림
  - Write-back : 캐시에만 쓰고 메모리에는 나중에 씀 (더티 비트 필요) - 빠르지만 구현 복잡
  
## I/O 통신 기술
1. Programmed I/O (Polling) - CPU가 주기적으로 I/O 상태 체크
2. Interrupt-driven I/O - I/O가 끝나면 CPU에게 인터럽트
3. DMA(Direct Memory Access) - CPU 개입 없이 디바이스 컨트롤러에서 메모리로 데이터 블럭 단위로 전송

## 프로세스
- 프로그램 : 디스크에 저장되어 있는 비활성화된 엔티티
- 프로세스 : 메모리에서 활성화된 엔티티
- PCB (Process Control Block) : 프로세스에 대한 정보(메타데이터)를 포함

#### 7-상태 프로세스 모델

## 모드 스위치 vs 프로세스 스위치
#### 모드 스위치 (Mode Switch)
- 프로세서의 상태를 유저 모드에서 커널 모드로 바꾸는 것
- 저장/복원 작업 오버헤드가 낮음

#### 프로세스 스위치 (Process Switch)
- 프로세서에 돌아가고 있는 프로세스를 다른 프로세스로 바꾸는 것
- 저장/복원 작업 오버헤드 높음
1. 현재 프로세스의 컨텍스트 저장
2. 현재 프로세스의 PCB 저장
3. 다른 프로세스 스케줄링
4. PCB를 다른 프로세스로 업데이트
5. 프로세서의 컨텍스트를 선택된 프로세스로 업데이트

## 메모리 (Memory)
### 메모리 계층
- 캐시 : 임시 저장소
	- 시간 지역성
	- 공간 지역성

### 메모리 관리
##### 가상 메모리
- 메모리를 캐시로 사용하여 하드디스크만큼의 용량과 메인 메모리의 속도를 가지도록 보이게 만드는 것
- 가상 주소 (Logical Address) : 가상적으로 주어진 주소
- 실제 주소 (Physical Address) : 실제 메모리상의 주소
- 가상주소 -> MMU -> 실제 주소
- 페이지 테이블 : 가상 주소와 실제 주소의 매핑을 모아놓은 것
-> 속도 향상을 위해 TLB 사용 – 페이지 테이블의 캐시

##### 메모리 할당
- 연속 할당 : 메모리에 연속적으로 공간 할당
	- 고정 분할 방식 : 메모리 미리 나눔, 내부 단편화 발생 가능
	- 가변 분할 방식 : 매 시점 프로그램 크기에 맞게 나눔, 외부 단편화 발생 가능
		- 최초적합(first fit) : 가장 처음 찾은 홀에 할당
		- 최적적합(best fit) : 프로세스 크기 이상인 홀 중 가장 작은 홀부터
		- 최악적합(worst fit) : 프로세스 크기와 가장 차이 많이 나는 홀에
- 불연속 할당 : 연속적으로 공간 할당하지 않고 임의 위치에 할당
	- 페이징 : 동일 크기의 페이지로 메모리를 나누어 서로 다른 위치에 할당
	- 세그멘테이션 : 의미 단위인 세그먼트(코드, 스택 등)로 나누어 서로 다른 위치에 할당
	- 페이지드 세그멘테이션 : 프로그램을 세그먼트로 나누고 페이지로 나눔

##### 페이지 교체 알고리즘
- 오프라인 알고리즘 : 먼 미래에 참조되는 페이지를 바꾸는 알고리즘, 가장 좋은 방법 but 실용 불가 -> 다른 알고리즘과의 성능 비교 상한선 제공
- FIFO : 가장 먼저 온 페이지를 가장 먼저 교체
- LRU : 참조가 가장 오래된 페이지 바꿈
- NUR : clock 알고리즘, 0과 1로 참조 여부 나타냄, 시계 방향 돌면서 0 찾은 순간 교체
- LFU : 가장 참조 횟수 적은 페이지 교체

### Process and Thread
- Thread : unit of execution (실행 단위)
- Process : unit of resource ownership (자원 소유자)
- Multithreading : 프로세스 하나에 다수의 실행 흐름, 같은 프로세스이므로 같은 자원 공유
- Concurrency(동시성) : interleaving – 교차로 배치하다
- Parallelism(병행성) : overlapping – 겹치다

### Race Condition
- Race condition - 동시에 공유 자원 접근
- Critical resource - 한번에 하나만 사용 가능한 공유 자원
- Critical section - 임계 자원에 접근하는 코드
- Mutual exclusion - 하나의 쓰레드만 그 자원을 사용하게 하는 것
- Synchronization – race condition을 막기 위해 critical section을 mutual exclusion시키는 기법
- 동기화의 분류 
	1. Mutual exclusion 
	2. Condition synchronization
- HW support – Disabling Interrupt, TAS and CAS
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
### Semaphore
- Semaphore S : 정수 값, 이용 가능한 리소스의 수
- semWait() : 값 1 감소 (P)
- setSignal() : 값 1 증가 (V)
- busy waiting을 피하기 위해 대기 큐를 사용
- Binary Semaphore (mutex lock) : 세마포어 값이 0과 1밖에 안됨
- Counting Semaphore : 세마포어 값이 1 이상 – 여러 개 들어올 수 있음
### Deadlock
- Deadlock : block된 프로세스에게 event를 기다림
- Deadlock의 조건
	1. mutual exclusion
	2. no preemption
	3. hold and wait
	*4. Circular wait – 이게 있으면 무조건 deadlock
- Deadlock prevention : 1, 2, 3, 4 중 하나 없앰
	- hold and wait : 필요한 자원들을 한번에 요청 – 안되면 계속 대기
	- circular wait : 자원에 순서를 매김
- Deadlock avoidance : 1, 2, 3 유지, 상태를 확인하여 deadlock 안 걸리는지 확인
	- process initiation denial 
		- 프로세스 시작 거부
		- Rj >= Cij >= Aij
		- R – 자원의 양, C – 최대로 요구하는 양, A – 지금 할당된 양
		- Rj >= C(n+1)j + 시그마cij 면 새 프로세스 시작
	- resource allocation denial 
		- 자원 할당 거부
		- banker’s algorithm
		- Ci – Ai <= V 면 할당
### Memory Management
- Static allocation : 프로그램 전에 할당
- Dynamic allocation : 런타임에 할당
- 요구사항
	- Relocation
		1. 미래에 메모리 어디에 프로그램이 놓일 지 모름 
		2. 프로그램이 움직일 수도 있음
	- Protection : 메모리 참조는 런타임에 체크해야함
	- Sharing : 같은 데이터는 프로세스끼리 공유
	- Logical organization : 세그먼트마다 base와 bound 따로 -> 나머지 공간 활용 가능
	- Physical organization : 메모리에 일부만 적재, 나머지는 디스크
- Address 종류
	symbolic : 소스코드 상
	logical : 컴파일 시 symbolic -> logical
	physical : 런타임(메모리 안에서)
		- Physical address는 transparent(숨김) -> 복잡해서 알려줄 필요 X
- 메모리 할당
- contiguous : 고정 분할(같은 사이즈, 다른 사이즈), 동적 분할, 버디 시스템
	Fixed partitioning
		- equal size
			장점 : 구현 쉬움
			단점 : 파티션 수 제약, 파티션 크기 제약, internal fragmentation
		- unequal size : 조금 줄이지만 위와 동일
	Dynamic partitioning
		- first-fit : 첫번째로 가능한 블록
		- next-fit : 마지막 할당 다음 위치부터 스캔
		- best-fit : 가장 근접한 크기 블록 선택
		-> first-fit 최선
		- 장점 : no internal fragmentation, 메모리 이용성 높임
		- 단점 : external fragmentation, compaction
	Buddy system
		- 2^U-1 < s <= 2^U 면 s 할당
		- 아니면 2개로 나눔
		- 둘다 할당 안하면 합침
		- 병렬 프로그램에 좋음
- non-contiguous : 페이징, 세그멘테이션
	Paging
		- 고정 길이
		- page : 프로세스 조각, frame : 메모리 조각
		- 장점
			- internal fragmentation이 마지막 page에서만 발생
			- no external fragmentation
		- address translation : page(frame) 크기 – 2의 거듭제곱 / (page num, offset) , 
		- Page num이 가리키는 frame num * 2^offset 비트 수 + offset : 물리 주소
	Segmentation
		- 가변 길이
		- 장점 : internal fragmentation 없음
		- 단점 : external fragmentation, compaction
		- code 세그먼트 : 쓰기 금지
		- length보다 크거나 같으면 주소 invalid
### Virtual Memory
- 디스크를 메인메모리처럼 사용 – 큰 메모리를 가진 것 같은 illusion 제공
- 장점 : 프로세스 더 많이, 프로세스 더 크게, i/o 줄어듦
- TLB – page table을 위한 cache
- locality를 이용
	- temporal locality : 참조한 곳 또 참조할 가능성 높음
	- spatial locality : 참조한 곳 주변부를 참조할 가능성 높음
- Fetch policy
	demand paging – 요청될 때 적재
	prepaging – 미래 예측하여 미리 적재
- Replacement policy
	- page fault 적은게 중요
	- optimal : 미래를 다 아는 가상의 상황 – 비교를 위한 것
	1. FIFO
		장점 – 구현 쉬움
		단점 : 자주 사용되는 page가 자주 out됨, belady’s anomaly
		belady’s anomaly : 프레임이 많아질수록 page fault가 줄어야 하는데 더 늘음
	2. LRU(Least Recently Used)
		장점 – belady’s anomaly 없음, optimal과 근접
		단점 : 비용 높음
	3. Clock 
		– LRU의 근사, 환형 버퍼와 bit 이용
		-> bit = 0이면 replace 후 1 설정, bit = 1이면 0으로 바꾸고 탐색 계속 진행
	4. Enhanced second-chance 
		- Clock 개선, modify bit 추가 – (reference bit, modify bit)
- Resident set policy
	fixed-allocation policy – 부족하면 자기 frame 희생
	variable-allocation policy – 부족하면 다른 frame 뺏어옴
- 가상 메모리 참조 순서
	1. TLB 체크 – 만약 있으면(hit) 프레임 번호 리턴
		없으면(miss) 페이지 테이블(메모리) 참조
	2. 페이지 테이블 – 있으면 엔트리 TLB에 올리고 프레임 리턴
		없으면 page fault exception
- issue 
	- page size
		- 페이지 사이즈 커짐 -> 페이지 테이블 작아짐 but internal fragmentation, 						memory 효용성 낮아짐
	- faster translation
		- TLB : context switch 필요 – 전 process는 의미없음
		1. Flush – 모든 valid 0으로 
		2. ASID – 프로세스마다 ID 부여 – 다르게 취급
	- smaller tables
		- 페이지 테이블이 메모리 너무 많이 차지
		1.  combined segmentation and paging – 세그먼트 테이블의 페이징
			장점 : 메모리 오버헤드 감소, external fragmentation 없어짐
			단점 : internal fragmentation 발생, 복잡한 하드웨어 지원 필요
	2. hierarchical paging – 페이지 테이블의 페이징
		장점 : small waste, 페이지 테이블이 연속적일 필요 없음
		단점 : load 추가
	3. inverted page tables – frame 넘버를 인덱스로 사용 -> 시스템에 하나의 페이지 테이블
		장점 : 테이블로 인한 메모리 사용 줄임
		단점 : 배열이 아니므로 검색 필요


## 스케줄링 알고리즘
#### 1. FIFO (First In, First Out, FCFS)
- 비선점 방식
- 레디 큐에 가장 오래 있었던 프로세스부터 선택

#### 2. Shortest-Process-Next (SPN)
- 비선점 방식
- 처리 시간이 가장 짧은 프로세스부터 선택
- 단점 : 처리 시간이 긴 프로세스가 실행되지 못할 수도 있음 (starvation), 처리 시간을 정확하게 알기 어려움

