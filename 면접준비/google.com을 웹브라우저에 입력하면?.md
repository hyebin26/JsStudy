# google.com을 웹브라우저에 입력하면?
웹브라우저에 google.com을 입력한다는 것은 구글 웹서버에 80포트로 HTTP Request메시지를 보내는 것이다. 

해당 요청을 인터넷을 통해 구글서버로 전달하기 위해 우리는 패킷을 만들어야 한다.

패킷(데이터의 형식화된 블록)에는 각 계층에 필요한 정보들이 담겨져 있어야 한다. 

![TCP-IP-model-Application-Layer-the-developed-application-uses-at-this-layer-a-protocol](https://user-images.githubusercontent.com/67263146/148182318-6a8bfaa8-a9ba-42e3-8672-4fad378c7192.png)


***Network Access Layer(Link Layer)***

- Network Access Layer: 네트워크에 직접 연결된 기기 간 전송을 할 수 있도록 해준다. (ex. Token Ling, Ethernet)
- Ethernet사용
- Ethernet프로토콜의 헤더를 만들기 위해서는 MAC Address를 알아야 한다.
- 맥주소는 컴퓨터간의 데이터를 전송하기 위해 있는 컴퓨터의 물리적 주소이다. 만약에 미국 A라는 컴퓨터와 통신을 하고 싶다고 하면 IP주소는 변동 가능성이 있으므로 절대 변하지 않는 그 기계의 고유 주소 번호가 있어야 하는데 그것이 MAC Address이다.
- 데이터를 통신할 때 일단 옆 사람에게 전달, 그 옆사람이 전달, 그 옆사람이 전달 이렇게 해서 A까지 가는게 네트워크 통신 방법인데 나와 이웃간의 통신을 보장해주는 프로토콜이 데이터 링크 계층 프로토콜이고, 이때 통신에 필요한게 MAC Address이다.
- 이러한 MAC Address를 별도로 전달하지 않아도 네트워크 단에서 IP주소로 MAC주소를 알아오는 기능을 하는 프로토콜을 ARP프로토콜이라고 한다.

***Internet Layer*** 

- Internet Layer: 수신 측 까지 데이터를 전달하기 위해 사용 (ex. IP, ICMP, RARP, OSPF)
- IP사용
- IP(Internet Protocol)이란 인터넷에 연결되어 있는 모든 장치(컴퓨터,서버장비,스마트폰 등)을 식별할 수 있도록 각각의 장비에게 부여되는 고유 주소이다.
- 여기서도 중요한 정보는 SA와 DA로 SA는 시작 IP주소로 내 컴퓨터의 IP주소로 이미 알고 있다. 그리고 목적지IP주소는 DNS를 이용해 도메인정보로 IP주소를 알 수 있다.
- DNS(Domain Name System)는 HTTP와 달리 사용자가 직접 요청하는 프로토콜이 아니다. 하위 트랜스포트 계층의 프로토콜을 이용하므로 Application Layer이다.
- DNS 서버주소는 각 컴퓨터에 등록되어 있다.
- DNS가 존재하지 않다면 google이 아니라 google의 IP주소를 외우고 다녀야 한다.

***Transport Layer***

- Transport Layer: 송신된 데이터를 수신측 애플리케이션에 확실하게 전달, 네트워크 통신을 하는 애플리케이션은 포트번호를 사용하는데 포트번호를 사용해서 애플리케이션을 찾아준다.  (ex, TCP, UDP, RTP, RTCP)
- TCP사용
- 들어가는 데이터 중에 중요한 것은 SP와 DP로 SP는 시작 포트번호로 내 컴퓨터에서 만든 소켓의 포트번호이고 DP는 목적지의 포트번호로 80으로 웹서버의 웰노운 포트 번호이다.

***Application Layer*** 

- Application Layer: 특정 서비스를 제공하기 위해 애플리케이션 끼리 정보를 주고 받을 수 있다.(ex. FTP, HTTP, SSH, Talnet, DNS, SMTP)
- HTTP사용

Network Access Layer의 ARP로 IP주소를 통해 MAC주소를 얻고, Internet Layer의 IP주소를 사용해서 올바른 도착지인 google.com에 도착할 수 있다. 그 다음 Transport Layer의 목적지 포트번호인 80번을 보고 80번 포트를 사용하는 애플리케이션에게 데이터를 전달해줘야 되는 것을 알 수 있다. 그 다음 Application Layer까지 오면 웹서버가 사용될 HTTP Request 데이터를 얻을 수 있다.

브라우저는 응답 패킷에 대한 파싱을 먼저 수행하고 구글 웹사이트의 경우 text/html타입으로 데이터를 받고 파싱하여 화면에 출력한다.

## Trasport Layer
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
[https://www.youtube.com/watch?v=BEK354TRgZ8](https://www.youtube.com/watch?v=BEK354TRgZ8) 

[웹사이트에 접속하면 일어나는 일](https://velog.io/@cada/%EC%9B%B9%EC%82%AC%EC%9D%B4%ED%8A%B8%EC%97%90-%EC%A0%91%EC%86%8D%ED%95%A0-%EB%95%8C-%EC%9D%BC%EC%96%B4%EB%82%98%EB%8A%94-%EC%9D%BC)

[MAC Address, IP](https://jhnyang.tistory.com/404)
[https://www.youtube.com/watch?v=ikDVGYp5dhg](https://www.youtube.com/watch?v=ikDVGYp5dhg)



이미지 출처: [https://www.researchgate.net/figure/TCP-IP-model-Application-Layer-the-developed-application-uses-at-this-layer-a-protocol_fig20_326632268](https://www.researchgate.net/figure/TCP-IP-model-Application-Layer-the-developed-application-uses-at-this-layer-a-protocol_fig20_326632268)
