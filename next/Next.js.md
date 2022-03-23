# Next.js

우선 Next.js는 React의 프레임워크입니다. 그럼 React의 어떤 문제를 해결하기 위해서 이러한 프레임워크가 탄생하게 됬는지 살펴보겠습니다.

1. 코드는 webpack과 같은 번들러를 사용하여 번들링되고 Babel과 같은 컴파일러를 사용하여 변환되어야 합니다.
2. code splitting 같은 production 최적화를 할 수가 없습니다.
3. Client Side Rendering이기 때문에 SEO를 적용할 수 없습니다.
4. React 앱을 데이터 저장소 연결 등 서버 측 코드를 작성할 수 없습니다.

다른 React 프레임워크도 이러한 문제들을 해결할 수 있습니다. 하지만 프레임워크는 높은 수준의 추상화를 필요로 하고, 또한 이것은 높은 수준의 개발경험이 있어야 팀이 코드를 쉽게 작성할 수 있습니다.

Next.js는 이러한 문제들에 해결책을 제공하고, React 앱을 구축할 때 당신의 팀을 성공하기 쉽게 만들어줍니다. 

---

### Gatsby는?

Gatsby는 위와 비슷하게 Client Side Rendering의 단점을 해결하기 위해 나온 프레임워크입니다. Gatsby는 React로 구성된 코드를 빌드할 때 React의 구성요소를 Public폴더에서 정적인  HTML페이지로 렌더링합니다(Static Site Generation). 즉, React로 구성된 코드를 정적인 파일로 미리 생성하고 서버에 배포해놓는 구조입니다. 

하지만 웹페이지가 모두 정적인 것이 아니라, 유저가 HTML을 보고  있을 때 React 구성요소를 다운받고 실행하고 리액트는 HTML과 동기화(Sync)되어서 동적인 요소도 추가할 수 있습니다.

넥스트 배경

---

### Next.js의 장점

Next는 SSG도 지원하고, SSR(Server Side Rendering),CSR도 지원해서 목적에 알맞게 사용할 수 있습니다.

1. Dynamic Routing과 함께 직관적인 페이지 기반 라우팅 시스템
2. [Pre-rendering](https://nextjs.org/docs/basic-features/pages#pre-rendering), SSG 그리고 SSR 사용 가능
3. 빠른 페이지 로딩을 위한 Automatic Code Splitting
4. [Client-side routing](https://nextjs.org/docs/routing/introduction#linking-between-pages)에 최적화된 Prefetching
5. [Fast Refresh](https://nextjs.org/docs/basic-features/fast-refresh)와 함께 빠른 개발 환경
6. Serverless Functions으로 API엔드포인트를 빌드하기 위한 [API routes](https://nextjs.org/docs/api-routes/introduction)

---

### Pre-rendering

Next.js는 default로 모든페이지가 **pre-render**가 된다. 즉, Next.js는 클라이언트 측 Javascript로 모든 작업을 수행하는 대신 각 페이지에 대해 미리 HTML을 생성합니다. P**re-render은 더 나은 성능과 SEO로 이어질 수 있습니다.**

만들어진 HTML은 페이지에 꼭 필요한 최소한의 코드로 이루어져 있습니다. 브라우저에 의해 페이지가 로드되었을 때, Javascript code는 동작되고 페이지를 전체적으로 interactive하게 만듭니다.(이러한 과정을 hydration이라고 부릅니다.)
- hydration: CSR에서는 HTML파일의 컨텐츠가 없이 오기 댸문에 React의 render함수를 이용해 컨텐츠를 생성해 DOM에 채워넣게 되고, 자연스럽게 React는 현재 DOM을 알게됩니다. 하지만 SSR의 결과로 이미 HTML에 컨텐츠가 존재하는 상황에서는 React의 render함수를 통해 다시 컨텐츠를 생성해내고 DOM을 채우는 것은 비효율적입니다. React는 `hydrate`를 통해 존재하는 HTML의 내용을 흡수할 수 있도록 합니다. HTML의 내용을 기반으로 React의 가상돔이 생성되고 React는 이 가상돔을 기반으로 향후 DOM을 조작하게 됩니다.

Rendering Form은 두 가지가 존재합니다.

- Static Generation(Recommended): HTML은 **build time**에 생성되고 각각의 요청마다 재사용될 수 있습니다. 이것은 **유저의 요청보다 먼저 렌더링될 수 있는 페이지에 좋습니다**. 또한 추가적인 데이터를 받아오는 클라이언트 사이드 렌더링과 함께 사용할 수 있습니다.
- Server Side Rendering: **HTML은 각각의 요청에 생성됩니다.** 서버사이드 렌더링의 결과는 **Static Generation보다 성능이 더 느리기 때문에, 꼭 필요할 때만 사용하는 것이 필요합니다.**

 

---

### Fast Refresh

Fast Refresh는 React구성 요소에 대한 편집(edit) 사항에 대한 즉각적인 피드백을 제공하는 Next.js기능입니다. Fast Refresh는 9.4이상의 Next.js애플리케이션에서 기본적으로 활성화 되어있습니다. Next.js의 Fast Refresh가 활성화되면 component state를 잃지 않고 대부분의 편집 내용이 1초 이내에 표시되어야 합니다.

- 만약에 React components를 export를 한다면, Fast Refresh는 그 파일을 업데이트하고, 너의 컴포넌트를 리렌더링할 것입니다. styles, 렌더링 로직, 이벤트 핸들러 등 어떤 것도 수정할 수 있습니다.
- **마지막으로 React트리 외부의 파일에서 가져온 파일을 편집하는 경우 빠른 새로 고침은 전체 로드를 수행하는 것으로 대체됩니다**. React구성요소를 렌더링하지만 **non-React component에서 가져온 값을 내보내는 파일**이 있을 수 있습니다**.** (ex.. 변수들 등) 이러한 경우에 이 파일들을 별도의 파일로 마이그레이션하고 두 파일로 가져오는 것을 고려해야 한다. 이렇게 하면 빠른 새로고침이 다시 활성화됩니다.

---

### Client Side Routing

Next.js router를 사용하면 단일 페이지 애플리케이션과 유사하게 페이지 간에 클라이언트 측 경로 전환을 수행할 수 있습니다. 뷰포트의 모든 <Link />(초기 또는 스크롤을 통해)는 정적 생성을 사용하는 페이지에 대해 기본적으로(해당 데이터 포함) 미리 가져(prefetch)옵니다. 서버 렌더링 경로에 대한 해당 데이터는 미리 가져오지 않습니다.

---

### API Routes

API Routes는 Next.js로 API를 빌드하기 위한 솔루션을 제공합니다.

페이지/api 폴더 내의 모든 파일은 /api/*에 매핑되며 페이지 대신 API end point로 처리됩니다. 서버 측 전용 번들이며 클라이언트 측 번들 크기를 늘리지 않습니다.

예를 들어 다음 API 경로 pages/api/user.js는 상태 코드가 200인 json 응답을 반환합니다.

```jsx
export default function handler(req, res) {
  res.status(200).json({ name: 'John Doe' })
}
```

API 경로가 작동하려면 함수를 기본(요청 핸들러라고도 함)로 내보내야 합니다. 그러면 다음 매개변수가 수신됩니다.

API route가 작동하기 위해, 밑의 파라미터들을 받는 default(ex. request handler)로서 함수를 export하는 것을 필요로 합니다. 

- “req” : [pre-built middlewares](https://nextjs.org/docs/api-routes/api-middlewares) 추가한 [http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage)의 인스턴스
- “res”:  :[helper functions](https://nextjs.org/docs/api-routes/response-helpers)를 추가한 [http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse) 인스턴스

API route에서 다른 HTTP 메소드를 다루기 위해, request handler로 `req.method` 사용할 수 있습니다.

```jsx
 export default function handler(req, res) {
  if (req.method === 'POST') {
    // Process a POST request
  } else {
    // Handle any other HTTP method
  }
}
```

새로운 프로젝트를 위해, API Route와 함께 전체의 API를 빌드할 수 있습니다. 만약에 API가 존재한다면, API Route를 통한 API호출을 필요로 하지 않을 수 있습니다.

- 외부 서비스의 URL 마스킹(masking) (ex. “https://company.com/secret-url” 대신에 “/api/secret”)
- 서버에서 환경 변수를 사용하여 외부 서비스에 안전하게 액세스합니다.

**주의사항**

- API Route는 CORS 헤더를 지정하지 않습니다. 즉, 기본적으로만 동일한 출처임을 의미합니다. 요청 처리기를 CORS 미들웨어로 래핑하여 이러한 동작을 사용자 지정할 수 있습니다.
