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

