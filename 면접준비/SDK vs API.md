참고: https://www.youtube.com/watch?v=kG-fLp9BTRo


## SDK vs REST API
## API(Application Programming Interface)
- API는 커뮤니케이션에 관한 것이며 앱, 서버 등과 통신하기 위한 정의 및 프로토콜 집합이다.. ex) 앱과 클라우드 서비스와의 연결하는 다리
### API를 사용하는 것에 장점
- Communication: 앱과 클라우드 서비스와 커뮤니케이션이 가능하다.
- Abstraction: API에는 코드가 상당히 길 수도 있지만 API는 추상화에서 프로세스를 단순화한다. 
- StandardAPI: 업계 표준 정의가 있다 ex) SOP, GraphQL, REST

### 구성요소
1. Request(요청)
- Operation이 필요하다.(PUT,POST 등)
- Parameter optional 
- End Point => 요청을 받는 곳 Basically URL
2. Resopnse => 보통 JSON 

### SDK(Software Develop Kit)
- 복잡한 API를 사용하는 것이 아니라 SDK라는 툴박스를 이용해서 API를 호출할 수 있다. ex) Firebase
- 일일히 REST API를 요청하는 것이 아니라 메소드로 호출할 수 있다.
- 응답이 JSON이 아닐 수도 있다.
