AWS를 이용해서 웹을 배포하면서 공부한 글입니다.

## Amazon S3
Amazon Simple Storage Service는 여러 기능을 제공하는 객체 스토리지 서비스이다.

### Amazon S3 작동 방식

Amazon S3는 데이터를 버킷 내의 객체로 저장하는 객체 스토리지 서비스이다. 객체는 해당 파일을 설명하는 모든 메타데이터이다. 버킷은 객체에 대한 컨테이너다.

Amazon S3에 데이터를 저장하려면 먼저 버킷을 생성하고 버킷 이름 및 AWS리전을 지정해야 한다. 그런다음 Amazon S3에서 객체로 해당 버킷에 데이터를 업로드한다. 각 객체에는 키가 있으며, 이는 버킷 내 객체에 대한 고유한 식별자이다.

S3는 특정 사용 사례를 지원하도록 구성할 수 있는 기능을 제공한다. 예를 들어 S3버전 관리를 사용하여 동일한 버킷에 여러 버전의 객체를 보관하고, 실수로 삭제되거나 덮어쓰기된 객체를 복원할 수 있다.

버킷과 버킷의 객체는 프라이빗하며, 액세스 권한을 명시적으로 부여한 경우에만 액세스할 수 있다. 버킷 정책, AWS Identity and Access Management 정책, 액새스 제어 목록, S3 액세스 포인트를 사용하여 액세스를 관리할 수 있다.

## EC2(Elastic Compute Cloud)
탄력있는(Elastic) 가상 공간 컴퓨터로 아마존의 컴퓨터의 일정 공간을 빌려서 사용가능하다. 

Amazon EC2는 각 사용사례에 맞게 최적화된 다양한 인스턴스(가상 컴퓨팅 환경)유형을 제공한다. 인스턴스 유형은 CPU, 메모리, 스토리지 및 네트워킹 용량의 다양한 조합으로 구성되며, 애플리케이션에 따라 적합한 리소스 조합을 선택할 수 있는 유연성을 제공한다. 각 인스턴스 유형은 하나 이상의 인스턴스 크기를 포함하고 있으므로 목표로 하는 워크로드 요구 사항까지 리소스를 확장할 수 있다. 

특징
- 서버 컴퓨터를 사지 않고 빌릴 수 있다.
- 트래픽에 따라 서버 용량을 쉽게 바꿀 수 있다.

### AMI(Amazon Machine Image)
EC2의 인스턴스를 시작하면 가장 먼저 선택해야 하는 것으로, AMI는 소프트웨어 구성이 기재된 템플릿이다(예 운영체제, 애플리케이션, 서버 등). AMI에서 인스턴스를 바로 시작할 수도 있는데, 이 인스턴스는 AMI의 사본으로, 클라우드에서 실행되는 가상서버이다. 인스턴스는 중단하거나 최대 절전 모드로 전환하거나 종료할 때까지 또는 오류가 발생하지 않는 한 계속 실행된다. 인스턴스가 실패하면 AMI에서 새로 실행할 수 있다.

이번 프로젝트에서 웹을 배포할 때 인스턴스의 국가를 미국으로 설정해서 국가를 재설정해야 되는 상황이 있었는데 AMI로 잘못설정한 인스턴스를 저장하고 나라를 변경해서 다시 만들었다.

### Instances
인스턴스는 클라우드의 가상서버이다. 시작할 때 구성은 인스턴스를 시작할 때 지정한 AMI의 사본이다. 

하나의 AMI에서 다양한 인스턴스 유형을 실행할 수 있다. 인스턴스 유형에 따라 인스턴스에 사용되는 호스트 컴퓨터의 하드웨어가 기본적으로 결정된다. 일단 인스턴스가 시작되면, 인스턴스는 다른 컴퓨터와 다를 것이 없고 어느 컴퓨터와 동일한 방식으로 다루면 된다. 인스턴스의 완벽한 통제가 가능하며, 루트 권한이 필요한 명령은 sudo를 사용하여 실행할 수 있다.

이번 웹을 배포할 때는 정적인 웹이 아니라 node.js를 이용한 동적인 웹이므로, EC2를 이용했다. 

## RDS(Amazon Relational Database Service)
RDS는 AWS클라우드에서 관계형 데이터베이스를 더 쉽게 설치, 운영 및 확장할 수 있는 웹서비스다. 이 서비스는 산업 표준 관계형 데이터베이스를 위한 경제적이고 크기 조절이 가능한 용량을 제공하고 공통 데이터베이스 관리 작업을 관리한다.

## 내가 사용한 것들
- 정적인 웹을 배포하는 것은 S3를 이용해도 되나 동적인 웹을 배포해야 하므로 EC2를 사용했다.
- node.js로 배포하는 정보가 많아서 EC2의 Unbuntu를 사용했다. => Ubuntu: Linux의 배포판
- 가비아로 도메인을 구매하고 Route 53으로 DNS시스템 도입했다.
- 환경변수를 저장하기 위해 AWS System Manager에 Parameter store를 이용하려고 파라미터를 생성하고 API호출하는 권한을 설정을 실패해서 env파일로 환경변수를 관리한다.
- CCM으로 HTTPS인증서를 받고 인스턴스에 적용했다.


참고: https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/ec2-instances-and-amis.html

https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/Welcome.html