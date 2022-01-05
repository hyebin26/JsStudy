## Transport Layer
TCP/IP의 계층으로 송신된 데이터를 수신측 애플리케이션에 확실히 전달하게 한다.

예를 들어, TCP, UDP, RTP, RTCP

- End Point간 `신뢰성`있는 데이터 `전송`을 담당하는 계층
- 신뢰성: 데이터를 순차적, 안정적인 전달
- 전송: 포트 번호에 해당하는 프로세스에 데이터를 전달
- 신뢰성있는 데이터를 포트 번호에 해당하는 프로세스에 전달하는 것

**만약 전송 계층이 없다면 ?**

- 데이터의 순차 전송 원할하지 않다면 ex)  123 전송 ⇒ 231
- Flow(흐름문제) 송수신자 간의 데이터 처리 속도 차이 ⇒ 데이터를 보내는 것은 마음껏 보낼 수 있으나 받는 쪽에서 데이터의 처리 속도가 느려서 한계량을 초과한 상태에서 데이터가 온다면 데이터가 누락됨
- Congestion(혼잡문제) 네트워크의 데이터 처리 속도 ⇒ 통신자에서 데이터를 보내도 네트워크가 혼잡해서 데이터가 가지 않을 때

### TCP(Transmission Control Protocol)

**전송 제어 프로토콜**(Transmission Control Protocol)은 인터넷 프로토콜 스위트(IP)의 핵심 프로토콜 중 하나로, IP와 함께 TCP/IP라는 명칭으로도 널리 불립니다. 주로 서버와 클라이언트간에 데이터를 신뢰성 있게 전달하기 위해 사용한다.


- 신뢰성있는 데이터 통신을 가능하게 해주는 프로토콜
- 특징: 3-way handshake(Connection 연결)
- 데이터의 순차 전송을 보장
- Flow Control(흐름제어)
- Congestion Control(혼잡 제어)
- Error Detection(에러 감지)

문제점 

- 전송의 신뢰성은 보장하지만 매번 Connection을 연결해서 시간 손실 발생
- 패킷을 조금만 손실해도 재전송(패킷은 데이터의 형식화된 블록)

### UDP(User Datagram Protocol)

- TCP보다 신뢰성이 떨어지지만 전송 속도가 일반적으로 빠른 프로토콜
- Connection(3 way-handshake X)
- Error Detection
- 비교적 데이터의 신뢰성이 중요하지 않을 때 사용(ex 영상 스트리밍)

## 참고
[https://www.youtube.com/watch?v=ikDVGYp5dhg](https://www.youtube.com/watch?v=ikDVGYp5dhg)
