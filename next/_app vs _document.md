## _document vs _app

_app.js는 동적인 것중에 공통 적용할 사항들을 기재

_document.js는 정적인 것중에 공통 적용할 사항들을 기재

### _document

커스텀 `document` 는 `<html>` 태그 그리고 `<body>`태그를 사용하여 렌더하는 페이지에 업데이트할 수 있습니다. 이 파일은 오직 서버에서만 사용되고, 그래서 `onClick`같은 이벤트 핸들러는 `_document` 에서 사용할 수 없습니다.

`_app` 다음에 실행되며, 공통적으로 활용할 `<head>`(ex. 매타태그)나 `<body>`태그 안에 들어갈 내용들을 커스텀할 때 활용합니다. 쉽게 말해, 공통적으로 적용할 HTML마크업을 커스텀하는 공간으로 볼 수 있습니다.

- 글로벌 커스텀 스타일 시트 로드(ex. Material UI, Bootstrap 등)
- 글로벌 Head
- 폰트 import

### _app

앱이 시작할 때 가장 먼저 호출되며, 라우팅도 이 페이지에서 내부적으로 작동하면 되기 때문에 앱의 글로벌한 작업들을 수행하기에 좋습니다.

모든 컴포넌트에 공통으로 적용할 속성을 관리할 때 주로 사용됩니다.

- 페이지들이 변화할 때 layout을 유지
- 페이지를 navigating할 때 state유지
- componentDidCatch로 고객들의 에러 관리
- 페이지들에 추가데이터 사용가능(예를 들어 전달되는 GraphQL queries)
- `_app` 은 현재 Next.js에 데이터 패칭 메소드 `getStaticProps` 혹은 `getServerSideProps` 를 지원하지 않습니다.
- 커스텀 `getInitialProps` 를 추가하는 것은 정적인 생성이 없는 페이지에서 **Automatic Static Optimization**을 사용할 수 없습니다.

### Automatic Static Optimization

Next.js는 `getServerSideProps` 그리고 `getInitialProps`가 없다면 자동적으로 페이지가 정적이라고 결정합니다. **Next.js는 static HTML을 페이지에 사전 렌더링(prerendering)을 함으로써 페이지를 자동적으로 정적으로 최적화**합니다.

하지만 사전렌더링을 하는 동안, 라우터의 `query` 오브젝트는 이 단계에 `query` 정보를 제공하고 있지 않기 때문에, 텅 비어있게 됩니다. `hydration` 후에, Next.js `query` 객체에 route파라미터를 추가합니다.

다른 렌더링을 트리거하는 hydration후에 쿼리가 업데이트되는 경우는 다음과 같습니다.

- 페이지가 dynamic-route일 경우
- 페이지가 URL에서 query-value를 가지고 있는 경우

주의할점

- `getInitialProps`와 함께 custom `_app` 을 가지고 있다면, 이러한 최적화는 정적인 생성없이 페이지에서 실행되지 않습니다.
- `getInitialProps` 와 함계 custom `_document` 를 가지고 있다면 페이지가 서버사이드에서 렌더링되는 것을 가정하기 전에  `ctx.req` 를 사용하고 있는 지 확인해야 합니다. `ctx.req` 는 사전 렌더링된 페이지에  대해 정의되지 않습니다.

### 페이지별로 데이터 패칭하기

사실 페이지 별로 데이터 패칭하는 것도 `getInitialProps`로 가능합니다. 그러나 그것이 정적데이터인지, 페이지 요청마다 렌더되는 데이터인지에 다라 그 방식을 분리한 것이 `getStaticProps`, `getServerSideProps`입니다. 

- `getStaticProps`: 빌드시 고정되는 값으로 빌드 이후에는 수정이 불가합니다. data를 빌드시에 미리 떙겨와서 static하게 제공하기 떄문에 빠른 속도로 렌더됩니다. 예를 들어, 유저에 구애받지 않고 퍼블릭하게 캐시할 수 있는 데이터, SEO등의 이슈로 인해 빠르게 미리 렌더링해야만 하는 페이지가 있습니다.
- `getSeverSideProps`: 빌드와 상관없이, 매 페이지 요청마다 데이터를 서버로부터 가져옵니다. `getServerSideProps`는 페이지를 렌더링하기 전에 반드시 fetch해야할 데이터가 있을 때 사용합니다. 매 페이지 요청시마다 호출되므로 `getStaticProps`보다 느리지만, 빌드 이후에는 페이지 요청마다 실행된다는 특징이 있습니다. `getServerSideProp`는 서버사이드에서만 실행되고 절대로 브라우저에서 실행되지 않습니다.
- 참고: [https://velog.io/@devstone/Next.js-100-활용하기-feat.-initialProps-webpack-storybook](https://velog.io/@devstone/Next.js-100-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0-feat.-initialProps-webpack-storybook)

### Dynamic Routes

사전에 정의하는 path를 사용하면서 routes를 정의하는 것은 복잡한 앱에서 충분하지 않습니다. Next.js에서 페이지에 Dynamic Routes를 추가할 수 있습니다.

예를 들어 `pages/post/[pid].js` 를 살펴보겠습니다.

```jsx
import { useRouter } from 'next/router'

const Post = () => {
  const router = useRouter()
  const { pid } = router.query

  return <p>Post: {pid}</p>
}

export default Post
```

`/post/1`, `/post/abc` 등 모든 라우트는 `page/post/[pid].js` 와 매치됩니다. 이러한 매치된 path 파라미터는 페이지에 쿼리 파라미터로 보내지고, 이것은 다른 쿼리 파라미터와 통합됩니다.

예를 들어, `/post/abc` 는 밑의 `query` 객체를 가집니다.

```jsx
{"pid": "abc"}
```

주의할 점은

- Automatic Static Optimization으로써 정적인 최적화를 하는 페이지는 라우트 파라미터가 제공되는 것없이 빈 객체로 하이드레이트 됩니다. 예를 들어 쿼리 객체는 ``{}`` 처럼 텅비어있게 됩니다. hydration후에, Next.js는 `query` 객체안에 라우트 파라미터를 제공하는 업데이트를 트리거 합니다.

### getInitialProps

`getInitialProps` ⇒ 페이지에서 SSR을 하고 초기 데이터를 설정할 수 있게 해줍니다. 그 의미는 서버로 부터 미리 받은 데이터를 함께 페이지에 보냅니다. 이것은 SEO에 유용합니다.

`getInitialProps`는 `static method` 로 모든 페이지에 추가될 수 있는 비동기 함수입니다.

`getIntialProps` 는 일부 데이터를 비동기적으로 가져오는데 사용되고 `props` 로 밀집된다

첫 페이지의 로딩에서, `getInitialProps` 는 오직 서버에서 동작합니다. 그리고나서 `getInitialProps` 는 사용자가 링크를 타고 다른 컴포넌트로 이동했을 때 혹은 router를 이용해서 이동했을 때 클라이언트에서 동작합니다. 그러나 `getIntialProps` 가 커스텀`_app.js` 에서 사용되거나 이동한 페이지가 `getServerSideProps` 를 실행한다면, `getInitialProps` 는 서버에서 동작합니다.
