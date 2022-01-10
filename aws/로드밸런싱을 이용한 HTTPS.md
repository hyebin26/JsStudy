이번에 AWS EC2를 이용해서 웹서버를 배포할 때 HTTP가 아닌 HTTPS로 배포하기 위해 ELB를 적용하면서 공부한 글입니다.

## Load Balancing
Load Balancing부터 이야기를 해보면 클라이언트가 한 두명인 경우 서버가 여유롭게 처리할 수 있지만 사용자가 1억명이라면 서버가 과부하에 동작을 멈추게 된다. 과부하를 막기 위한 방법은 크게 두 가지로 나눌 수 있다.

- Scale-up: 서버가 더 빠르게 동작하기 위해 하드웨어 성능을 올리는 방법
- Scale-out: 서버의 개수를 늘려 여러대의 서버가 나누어 작업을 수행하는 방법 => **로드밸런싱**

### 로드밸런싱의 종류
로드 밸런싱의 종류는 OSI계층에 따라 나뉜다. 
- OSI계층은 네트워크에서 통신이 일어나는 과정을 7단계로 나눈 것으로 정보기술에서 패킷 방식의 컴퓨터 네트워크에서 데이터를 주고받을 때 정해놓은 규칙으로 통신이 일어나는 과정을 단계별로 파악할 수 있다. 
![hello](https://user-images.githubusercontent.com/67263146/148767561-87578781-277c-4f5e-9e2f-3c22971a9691.png)
- L4: Transport Layer을 사용, IP주소와 포트 번호 부하 분산이 가능
- L7: Application Layer에서 로드를 분산하기 때문에 HTTP헤더, 쿠키 등과 같은 사용자의 요청을 기준으로 특정서버에 트래픽을 분산하는 것이 가능하다. 쉽게 말해 패킷 내용을 확인하고 그 내용에 따라 로드를 특정 서버에 분배하는 것이 가능한 것이다.
![load2](https://user-images.githubusercontent.com/67263146/148768085-edb86867-4575-417e-bd25-7859299bb799.png)

## HTTPS적용하기!!
우선 HTTPS로 로드밸런싱하는 것을 진행하기 전에 ACM(Amazon Certificate Manager)에 대해서 알아야한다. 
- ACM은 Amazon Certificate Manager로 SSL/TLS 인증서를 손쉽게 프로비저닝, 관리, 배포 및 갱신할 수 있다.
- SSL(Secure Socket Layer): 보안 프로토콜을 통해 클라이언트와 서버가 보안이 향상된 통신을 하는 것을 말한다.
- HTTPS는 TLS와 HTTP가 조합된 프로토콜을 가르키며 TLS는 SSL의 후속버전이다.
- 우리는 ACM을 이용해서 기존의 사이트에 HTTPS인증서를 요청할 수 있다.

이제 아마존에서 제공하는 로드밸런싱 ELB(Elastic Load Balancing)에 대해서 알아보면 ELB는 자동적으로 EC2 인스탠스, Container 그리고 IP주소와 같은 다양한 타겟에 들어오는 트래픽을 분배한다. 이것은 이것에 등록된 타겟의 상태(healthy)를 감시하고, healthy target으로 라우팅한다.

ELB는 밑의 Load balancer를 지원한다.
- Application Load Balancer => L7
- Network Load Balancer => L4
- Gateway load Balancer

이해하기 위해 웹에서 사용하기 편리한 Application Load Balancer의 동작 순서를 확인해보자.
1. Client는 너의 앱에 요청을 보낸다.
2. 로드밸런서의 Listener는 구성한 프로토콜 및 포트와 일치하는 요청을 받는다.
3. Receiving Listener는 사용자가 지정한 규칙에 따라 들어온 요청을 평가하고 해당한다면 요청을 적절한 대상그룹으로 라우팅한다. HTTPS 리스너를 사용하여 TLS암호화 및 암호 해독 작업을 로드 밸런서로 오프로드(분담) 할 수 있다. 
4. 하나 이상의 대상그룹에 있는 Healthy targets은 로드 밸런싱 알고리즘과 리스너에서 지정한 라우팅 규칙에 따라 트래픽을 받는다.
![load balancing](https://user-images.githubusercontent.com/67263146/148774056-1ef58982-075c-4654-93d0-1b45ea8f55dd.png)



그럼 HTTPS를 적용하기 위해서 로드밸런싱이 왜 필요할까? 위의 동작 순서를 HTTPS에 적용해보자.
1. 웹에(기존의 HTTP)도메인을 입력하고 엔터를 누른다.
2. Listener는 지정한 규칙(포트)로 들어온 HTTP요청을 받는다.
3. Listener는 들어온 요청을 평가하고 요청을 설정한 HTTPS로 라우팅(redirect로 설정)한다.
4. HTTPS는 리스너에서 지정한 라우팅 규칙에 따라 트래픽을 받는다 => 등록한 EC2 인스턴스 

### HTTPS를 적용할 때 주의할 점 
- HTTPS 로드밸런싱 설정을 하고 Route 53의 호스팅영역에도 로드밸런싱을 적용해줘야 한다.
- 설정한 로드밸런싱에 맞는 EC2 인스턴스에 인바이드 규칙을 설정해줘야 한다.

## 참고

참고: https://shlee0882.tistory.com/110
https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html
https://velog.io/@makeitcloud/란-L4-load-balancer-vs-L7-load-balancer-란
