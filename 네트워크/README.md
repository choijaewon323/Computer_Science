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

#### 커넥션 연결 시 three-way handshake
- SYN(클라이언트) -> SYN+ACK(서버) -> ACK(클라이언트) 
- SYN 세그먼트 : 데이터를 전송 X but 시퀀스 넘버 소비
- SYN + ACK 세그먼트 : 데이터 전송 X but 시퀀스 넘버 소비
- ACK 세그먼트 : 데이터 전송이 없을 시 시퀀스 넘버 소비 X

#### 데이터 전송 (클라이언트 -> 서버)
- PSH 플래그가 1이 됨

#### 커넥션 종료 시 three-way handshake
- FIN(클라이언트) -> FIN + ACK(서버) -> ACK(클라이언트)
- FIN 세그먼트 : 전송할 데이터가 없을 시 시퀀스 넘버 소비
- FIN + ACK 세그먼트 : 전송할 데이터가 없을 시 시퀀스 넘버 소비
- FIN + ACK 세그먼트에서 만약 서버가 클라이언트에게 전송할 데이터가 있을 경우 ACK을 먼저 보내고 데이터 전송이 끝나면 FIN 을 보냄 (Half-Close)

#### 커넥션 거부
- SYN(클라이언트) -> RST(서버)

#### 커넥션 중단(abort)
- RST + ACK(클라이언트)

### TCP와 UDP 차이

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
