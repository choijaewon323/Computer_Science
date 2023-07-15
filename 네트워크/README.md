# 네트워크 정리

## 7 Layers
- 인터넷 : TCP/IP 프로토콜을 이용해 정보를 주고받는 컴퓨터 네트워크

#### OSI 모델
|||Role|
|--------|----------------|-----------------------------|
|Layer 7|Application| |
|Layer 6|Presentation| |
|Layer 5|Session| |
|Layer 4|Transport|process to process|
|Layer 3|Network|source to destination|
|Layer 2|Data link|hop to hop delivery|
|Layer 1|Physical| |

#### TCP/IP Protocol Suite

| | | unit|
|--------|----------------|--------------------------|
|     /    |      /       |      /        |
|Layer 5|Application|message|
|      /        |     /        |       /      |
|Layer 4|Transport|segment|
|Layer 3|Network|datagram|
|Layer 2|Data link|frame|
|Layer 1|Physical|bit|

- 어플리케이션 레이어 : Application-Specific address
- 트랜스포트 레이어 : Port address
- 네트워크 레이어 : Logical address
- 데이터링크 레이어 : Physical address

- 물리 주소는 hop to hop 동안 바뀌지만, 논리 주소와 포트는 바뀌지 않음


## TCP

### Numbering System
- 데이터의 바이트는 임의로 생성된 숫자로 시작함
```
ex) TCP 커넥션에서 5000 바이트를 전송하고, 첫 바이트가 10001로 숫자가 정해졌을 때, 1000 바이트씩 나누어지는 각 세그먼트의 시퀀스 넘버는?
ans)
segment 1 : 10001
segment 2 : 11001
segment 3  :12001
segment 4 : 13001
segment 5 : 14001
```
- Cumulative ACK : 데이터를 받고 ack을 보낼 때, 자신이 받아야하는 시퀀스 넘버를 보냄
- Selective ACK : 데이터를 받고 ack을 보낼 때, 자신이 받은 시퀀스 넘버를 보냄
- TCP에서 체크섬은 필수
- 컨트롤 필드 : 1일 경우 활성화되었다는 뜻이다.
	- URG : Urgent pointer 가 유효함
	- ACK : Acknowledgment 가 유효함
	- PSH : push 요청
	- RST : 커넥션 리셋
	- SYN : 시퀀스 넘버 동기화
	- FIN : 커넥션 종료

### Connection
- 커넥션 연결 시 three-way handshake
	- SYN(클라이언트) -> SYN+ACK(서버) -> ACK(클라이언트) 
	- SYN 세그먼트 : 데이터를 전송 X but 시퀀스 넘버 소비
	- SYN + ACK 세그먼트 : 데이터 전송 X but 시퀀스 넘버 소비
	- ACK 세그먼트 : 데이터 전송이 없을 시 시퀀스 넘버 소비 X
- 데이터 전송 (클라이언트 -> 서버)
	- PSH 플래그가 1이 됨
- 커넥션 종료 시 three-way handshake
	- FIN(클라이언트) -> FIN + ACK(서버) -> ACK(클라이언트)
	- FIN 세그먼트 : 전송할 데이터가 없을 시 시퀀스 넘버 소비
	- FIN + ACK 세그먼트 : 전송할 데이터가 없을 시 시퀀스 넘버 소비
	- FIN + ACK 세그먼트에서 만약 서버가 클라이언트에게 전송할 데이터가 있을 경우 ACK을 먼저 보내고 데이터 전송이 끝나면 FIN 을 보냄 (Half-Close)
- 커넥션 거부
	- SYN(클라이언트) -> RST(서버)
- 커넥션 중단(abort)
	- RST + ACK(클라이언트)
### Flow Control
- stop and wait : 데이터 보내고 ack 받고 반복
	- 문제 : 대역폭이 충분해도 여러 개를 보내지 않음 – 비효율적
- sliding window : window 사이즈에 한해서 여러 데이터를 보내는 방식
	- rwnd 기준으로 : receiving 버퍼의 남은 공간
	- receiving 쪽의 어플리케이션이 얼마나 버퍼에서 데이터를 빨리 가져가냐에 따라 다름
	- silly window syndrome : 윈도우 사이즈에 비해 보내는 데이터가 너무 작을 때
		- 헤더 크기가 더 커서 비효율적
		- 송신 측
			- Nagle 알고리즘
				- 처음에 데이터 하나 보내고 ack 오는 시간 측정
				- 다음부터 ack 오는 시간동안 최대 사이즈까지 데이터 축적
				- 보낼 데이터 Max면 무조건 보냄
				- 그동안에도 다 안쌓이면 ack 도착시 보냄
		- 수신 측
			- Clark 해결
				- MSS 만큼 비거나 버퍼 반만큼 비어야 제대로 rwnd 보냄
				- 아닌 경우 rwnd 0으로 보냄
			- 확인응답의 지연
				- 위와 같은 상황에서 조건이 만족될때까지 ack 안보냄
### Error Control
- ack은 상대방이 받았는지 보장 x -> ack에 대한 ack은 없음
- out of order인 데이터는 상위 계층으로 안 보냄
- ack 개수 줄이기
	- rule 1 : 보낼 데이터가 있으면 ack과 데이터 함께 전송
	- rule 2 : 보낼 데이터 없어도 기다렸다가 그래도 없으면 ack만 보내기
	- rule 3 : 보낼 데이터가 없어 대기중일 때 ack이 또 오면 바로 ack 보내기
- Lost segment
	- send 세그먼트가 하나 없어졌을 때
	- rule 4 : out of order 가 발생하면 없어진 패킷에 대한 번호를 ack으로 바로 보내기
	- rule 5 : 다시 resent 받은 거 도착하면 바로 ack 보내기
- Fast retransmission
	- receiver가 못 받은 ack 3번 보내면 그때 다시 보내기
	- 원래는 timeout까지 기다려야 하지만 그 전에 보냄
- Lost ack
	- 중복된 패킷을 받을 수도 있음 <- sender는 ack이 없어진지 모르므로
	- rule 6 : 중복된 패킷을 받을 수 있고 이때 제대로 된 ack 보내기
### Congestion Control
- cwnd를 이용, 이는 보내는 쪽이 계산해야함
- Slow start
	- 지수 증가
	- 1에서 시작해서 2배씩 늘려나감
	- time out 발생 : thresh = 1/2 window, cwnd = 1 MSS
	- 3 중복 ack : cwnd = 1/2 cwnd, congestion avoidance로 전환
- Congestion avoidance
	- 덧셈 증가
	- 1씩 늘려가며 그 시간이 RTT와 일치하는지 -> 맞으면 계속 늘려감
	- time out 발생 : thresh = 1/2 window, cwnd = 1 MSS, Slow start로 전환
	- 3 중복 ack : cwnd = 1/2 cwnd
- Slow start ->(thresh) Congestion avoidance


## TCP와 UDP 차이

#### UDP
- 비디오 재생, DNS 조회와 같은 민감한 전송을 위해 사용
- TCP와 다르게 커넥션을 하지 않음
- TCP보다 빠름
- 패킷의 수신 순서, 의도를 파악하지 않음
- DDoS 공격에 취약함


#### TCP
- 안정성이 높음
- 쓰리 웨이 핸드셰이크를 통한 커넥션 필요
- 패킷 순서 지정, 오류 검사


## IP
### ARP
- IP 주소로부터 MAC 주소를 구하는 프로토콜
  1. ARP Request 브로드캐스트 – 해당 IP 주소에 해당하는 MAC 주소 찾음
  2. 해당 IP인 장치가 ARP Reply 유니캐스트 – MAC 주소 반환

### hop by hop 통신
- 통신망에서 패킷이 라우터를 건너가는 것
- IP 주소를 통해 통신하는 과정
- 라우팅 테이블 : 목적지로 가기 위한 방법이 들어있는 리스트
- 게이트웨이 : 서로 다른 통신망, 프로토콜을 사용하는 네트워크 간의 통신을 가능하게 하는 컴퓨터, 소프트웨어
- 라우터는 장비, 게이트웨이는 개념적 의미

### 주소 체계
- IPv4 : 32비트를 8비트로 잘라 표기
- IPv6 : 64비트를 16비트로 잘라 표기

### 클래스 기반 할당 방식
- A, B, C, D, E 클래스로 주소 분리
- 앞 부분을 네트워크 주소로, 뒷 부분을 호스트 주소로 사용
- 해당 네트워크 안의 첫번째 주소는 네트워크 주소, 가장 마지막 주소(~255) 는 브로드캐스트용

### DHCP
- IP 주소를 자동으로 할당

### NAT (Network Address Translation)
- 공인 IP, 사설 IP 로 구분지어 사용
- 하나의 공인 IP에 여러 사설 IP 할당 -> 주소 가용범위 확장
- NAT 장치가 사설 IP와 공인 IP 간의 변환

