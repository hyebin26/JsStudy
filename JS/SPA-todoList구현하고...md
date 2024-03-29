### 알게된 것

1. 데이터를 보낼 때 header의 컨텐트 타입과 body의 데이터 타입이랑 똑같아야 함 => 아닐 시 전송이 되지 않는다.
2. DOMcontentedLoad vs window onLoad
- window onload는 이미지 그리고 css ,서브프레임 등이 완벽하게 로딩이 끝났을 때 이벤트가 시작된다.
- DOMcontentedLoad는 초기 HTML문서를 완전히 불러오고 분석했을 때 발생하고, 스타일시트, 이미지, 하위 프레임의 로딩은 기다리지 않는다.
3. useState는 클로저를 이용해 구현할 수 있다.
- [https://ko.javascript.info/onload-ondomcontentloaded](https://ko.javascript.info/onload-ondomcontentloaded)
- [https://github.com/hyebin26/JsStudy/blob/main/JS/클로저.md](https://github.com/hyebin26/JsStudy/blob/main/JS/%ED%81%B4%EB%A1%9C%EC%A0%80.md)
4. JWT token
- Refresh 토큰과 Access 토큰 두 개를 받아서 accessToken이 만료될 시 Refresh를 이용해 재발급하는 방식
5. onclick 문제점
- <button onclick>로 이벤트를 등록하면 함수를 전역에서만 찾는다.
- 전역변수로 선언되면 자바스크립트는 파일이 달라도 하나의 전역변수로 선언되기 때문에 문제가 일어나기 쉽다.
6. attribute에서 이벤트를 등록하는 것이 아니라 직접적으로 이벤트를 등록한 이유
- index.js에서 DOM을 찾아서 이벤트를 등록하면 가독성이 떨어짐
- 전역변수로 선언하는 것에 있어서 거부감이 있었으나 가독성과 속도의 저하도 크지 않을 것 같아서 attribute로 직접 이벤트를 등록함
- 또한 처음엔 e.target이 되지가 않아서 다시 바꿧으나 event(this)이렇게하면 e.target을 가져올 수 있음
7. axios를 추가한 이유
- 로그인을 하고 API통신을 하기 위해서는 쿠키를 헤더에 넣고 통신을 해야된다.
- 쿠키를 사용해서 저장하는 것보다 axios.defaults 사용하는 것이 더 안전할 거라고 생각하기도 했고 fetch를 더 간편하게 할 수 있기 때문에 추가했음
- 하지만 페이지의 새로고침이 일어날 때 axios.defaults로 되어 있는 설정이 초기화되서 쿠키를 사용했지만 fetch보다 사용이 편리해 fetch대신 axios를 사용했다.
8. 코드 로직을 짜야 할 때
- 이번에 social로그인 로직을 짤 때 기준이 없어서 너무 오래걸렸다. => auth_redirect를 처음에 로그인, 다음에 signUp그 다음에 main으로 했는데 main으로 할 경우 리렌더링이 일어날 때 마다 API가 호출됨 => 비효율 => 다시 login으로 로직변경
- 최대한 직관적으로 짜기 위헤 API를 호출해서 이미 회원가입한 아이디면 needSignup:false 회원가입을 하지 않은 아이디면 needSignUp:true를 주는 것으로 해결했다.
9. LocalStorage vs Cookie
- Cookie vs LocalStrage
- Cookie는 매번 서버로 전송이 되고 LocalStorage에 비해 용량이 적다.
- LocalStrage 매번 서버로 전송X 클라이언트 사이드에서 저장하고 관리, 용량도 쿠키보다 큼,영구 데이터 저장이 가능하다.
10. 토큰 유효성 검증(비동기 동기)
- token의 유효성을 검증할 때 checkToken이라는 함수에 우선 accessToken을 검증하고 만료되었을 경우 DB에 refreshToken이 존재하는지 체크하고 있을 경우 accessToken을 재발급 받는 형식으로 함수를 작성하였다. 하지만 DB를 호출하는 함수가 비동기여서 return값에 undefined가 나옴 => 다른 방법을 찾지 못하여 DB호출하는 함수를 쿼리문에서 구현
- 참고: [https://stackoverflow.com/questions/14220321/how-to-return-the-response-from-an-asynchronous-call](https://stackoverflow.com/questions/14220321/how-to-return-the-response-from-an-asynchronous-call)
11. Query String vs Path Variable

Path Variable

- 특정 인덱스에 대한 조회
- ex) 아이디가 20번인 유저 조회 -> /user/:userId

Query String

- 특정 값으로 필터링
- 이름이 james이고 20살인 유저조회 => /user?userName=james?age=20
12. event.currentTarget vs event.target
- 바닐라 자바스크립트로 링크를 구현할 때 모든 링크에서 onClickTag라는 하나의 함수를 공용으로 호출하기 때문에, 함수의 매개변수 to값이 계속해서 변경되었음.
- 고민된게 우선 계속해서 새로운 함수를 만들어 주는 것은 올바른 로직이 아니라고 생각해서 고민하던 중 현재의 target에 적혀있는 href값을 가져와서 url을 변경하려고 했으나 a태그 자식요소가 존재할 경우 target에 href가 존재하지 않음
- event.currentTarget은 무조건 이벤트가 걸려있는 위치를 반환하므로 자식요소를 클릭해도 이벤트가 걸려있는 a태그를 가져오므로 href값을 가져올 수 있음
13. form태그 사용할 때 form요청에 필요한 데이터가 사용되는 곳에만 form태그 감싸기
=> 안에 다른 버튼이나 이벤트가 등록되어 있을 경우 이벤트가 버블링 되기 때문에 이벤트가 꼬이게됨
14. useState가 async인 이유
참고:[https://github.com/hyebin26/JsStudy/blob/main/React/UseState가 async인 이유.md](https://github.com/hyebin26/JsStudy/blob/main/React/UseState%EA%B0%80%20async%EC%9D%B8%20%EC%9D%B4%EC%9C%A0.md)
