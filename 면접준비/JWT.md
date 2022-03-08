## JWT(JSON WEB TOKEN)

### TL;DL

JWT의 탄생배경은 HTTP Request, 보안 문제 때문에 탄생하게 되었습니다. 장점은

- 토큰을 사용하면 세션을 통한 방식과 달리 서버측 부하를 낮출 수 있습니다.
- 능률적인 접근 권한 권리를 할 수 있으며, 분산/클라우드 기반 인프라스트럭처에 더 잘 대응할 수 있습니다.
- 모바일에서 JSON 웹토큰을 사용할 수 있다.

### 보안에 대한 문제

권한이 없는 사용자, 즉 API를 요청한 유저가 아닌 다른 유저가 API 접근하면 해당 유저의 정보가 탈취될 수 있습니다.

- 권한이 없는 사용자(해당 아이디와 비밀번호로 로그인을 하지 않은)가 API를 호출할 경우가 생김 ⇒ 보안이 문제
- Access Token을 발급해 사용자 권한이 있을 경우만 API호출이 가능하게함 ⇒ 하지만 Access Token을 갈취할 경우가 생김
- Access Token이 만료 기간을 짧게 설정하여 Refresh Token을 이용해 Access Token을 재발급 받는 방식으로 해결 ⇒ 하지만 기간을 짧게 설정해도 탈취될 가능성은 충분히 존재함
- 만약에 Refresh Token과 같이 발급된 Access Token이 존재하는데 다른 Access Token을 사용해 API를 호출하면 해당 Refresh , Access Token은 파괴하고 무효화 ⇒ 사용자는 로그인을 다시 해야됨.

### State에 대한 문제

HTTP프로토콜은 Stateless로 동작합니다. 새 요청은 이전 요청에 대해 아무것도 모르기 때문에, 새 요청마다 다시 인증해야 합니다. 즉, 모든 요청 시 마다 사용자를 인증해야 합니다. 

이를 처리하기 위해 과거에는 **SSL(Server Side Session)를 사용했습니다**. 사용자 이름과 비밀번호를 확인하고 인증된 경우 서버는 세션 ID를 메모리에 저장하고 그 ID를 클라이언트에게 반환하는 방식이었습니다. 하지만 이러한 SSL 방식은 유저가 많아질 경우 서버측 과부하를 발생시킬 수 있습니다.

### JWT

JW(Json Web Token)이란 JSON포멧을 이용하여 사용자에 대한 속성을 저장하는 Claim 기반의 Web Token입니다. JWT는 토큰 자체를 정보로 사용하는 Self-Contained 방식으로 정보를 안전하게 전달할 수 있습니다.

장점은

- 토큰을 사용하면 세션을 통한 방식과 달리 서버측 부하를 낮출 수 있습니다.
- 능률적인 접근 권한 권리를 할 수 있으며, 분산/클라우드 기반 인프라스트럭처에 더 잘 대응할 수 있습니다.
- 모바일에서 JSON 웹토큰을 사용할 수 있다.

사용자 인증 정보를 넣어서 토큰을 발급해주면 추후 인증이 필요한 리소스에 접근 시 사용자가 서버에 토큰을 포함해서 전송하여 서버측에서는 복잡한 인증 과정없이 토큰만으로 사용자를 인증 및 인가할 수 있는 것이다.

### RefreshToken을 사용한 이유

서버측 리소스에 접근할 때 클라이언트 본인을 인증할 수 있는 액세스 토큰으로 동작합니다. 그런데 이 JWT는 Stateless한 방식이기 때문에 서버측에서는 이 토큰을 갖고 있는 클라이언트가 정말 클라이언트 본인이 맞는지 확인할 수 없다는 문제점이 있습니다.

그래서 이에 대한 보안대책으로 리프레쉬 토큰이라는 추가적인 토큰을 활용할 수 있습니다. 이 리프레쉬 토큰은 사용자 인증이 아닌 새로운 액세스 토큰을 생성하는 용도로만 사용됩니다. 이는 JWT유출 문제를 다음처럼 해결하기 위해서 입니다.

- Access Token의 유효 기간을 짧게 설정한다.
- Refresh Token의 유효기간은 길게 설정한다.
- 공격자는 Access Token을 탈취하더라도 짧은 유효기간이 지나면 사용할 수 없다
- 정상적인 클라이언트는 유효 기간이 지나더라도 Refresh Token을 사용하여 새로운 Access Token을 생성, 사용할 수 있음

물론 이렇게 할 경우 Refresh Token이 탈취되면 공격자는 똑같이 API에 접근할 수 있습니다. 그러나 이제는 액세스 토큰만으로 불가능했던 이러한 사건을 쉽게 식별하고 피해를 줄이기 위해 필요한 조치를 취할 수 있게 되었습니다. 

- 만약에 해당 Refresh토큰과 같이 발급된 Access토큰이 존재하는데 다른 Access Token에서 요청이 온다면 서버는 이상으로 간주한다.
- 이상을 감지하면 서버는 문제의 Refresh토큰을 파괴하고 모든 것과 연결된 액세스 토큰을 무효화한다. 따라서 리소스를 필요로 하는 모든 권한 부여에 대한 악의적인 추가 액세서를 방지한다.
- 사용자는 로그인을 다시하고 Refresh, Access Token을 재발급 받아야 한다.

### 참고

[https://smoh.tistory.com/347](https://smoh.tistory.com/347)

[https://velog.io/@park2348190/JWT에서-Refresh-Token은-왜-필요한가](https://velog.io/@park2348190/JWT%EC%97%90%EC%84%9C-Refresh-Token%EC%9D%80-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%9C%EA%B0%80)

[https://stackoverflow.com/questions/32060478/is-a-refresh-token-really-necessary-when-using-jwt-token-authentication](https://stackoverflow.com/questions/32060478/is-a-refresh-token-really-necessary-when-using-jwt-token-authentication)
