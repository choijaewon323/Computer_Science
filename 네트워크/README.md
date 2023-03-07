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
