# Pre-rendering and Data Fetching 
### Pre-rendering

Next.js는 default로 모든페이지가 **pre-render**가 된다. 즉, Next.js는 클라이언트 측 Javascript로 모든 작업을 수행하는 대신 각 페이지에 대해 미리 HTML을 생성합니다. **Pre-render은 더 나은 성능과 SEO로 이어질 수 있습니다.**

만들어진 HTML은 페이지에 꼭 필요한 최소한의 코드로 이루어져 있습니다. 브라우저에 의해 페이지가 로드되었을 때, Javascript code는 동작되고 페이지를 전체적으로 interactive하게 만듭니다.(이러한 과정을 hydration이라 부름)

Next.js는 크롬 devTools로 javascript Disable할 경우 JS가 적용되지 않은 정적인 HTML사이트를 볼 수 있습니다. 하지만 plain React.js인 경우 Disable할 경우 페이지가 보이지 않습니다.

---

### Two Forms of Pre-rendering

Next.js는 두 가지 pre-rendering form을 가지고 있습니다. 이 두가지 form의 차이점은 페이지에 HTML이 만들어질 때 입니다.

- Static Generation은 **build time**에 HTML을 생성하는 method를 pre-rendering합니다.
- Server-Side Rendering은 **각각의 요청**에 HTML을 생성하는 method를 pre-prendering합니다.

![static-generation](https://user-images.githubusercontent.com/67263146/154225267-ac906580-c8df-4a5e-8737-cf76e22a35ee.png)

![server-side-rendering](https://user-images.githubusercontent.com/67263146/154225274-94a10089-ddea-4d1a-a0a4-4af7bb92d7da.png)


Next.js에서 pre-rendering의 형태를 선택하는 것은 중요합니다. 우리는 대부분의 페이지에 Static Generation을 상요하고 다른 곳에는 Server-Side Rendering을 사용하면서 하이브리드앱을 만들 수 있습니다.

### Static Generation vs Server-side Rendering

우리는 최대한 Static Generation(데이터 없이)를 사용하는 것을 권장합니다. 왜냐하면 페이지를 한 번 만들고 CDN에서 제공할 수 있기 때문에 서버가 모든 요청에 대해 페이지를 렌더링하도록 하는 것보다 훨씬 빠릅니다.

페이지에 많은 타입에서 Static generation을 사용할 수 있습니다.

- Marketing pages
- Blog posts
- E-commerce product listings
- Help and documentation

반면에 Static Generation은 사용자의 요청에 앞서 페이지를 미리 렌더링할 수 없다면 좋은 생각이 아닙니다. 페이지에 자주 업데이트되는 데이터가 표시되고 모든 요청에 따라 페이지 콘텐츠가 변경될 수 있습니다.

이 경우 Server-side Rendering을 사용할 수 있습니다. 속도는 느려지지만 미리 렌더링된 페이지는 항상 최신 상태로 유지됩니다. 또는 사전렌더링을 건너뛰고 Client-side Javascript를 사용하여 자주 업데이트되는 데이터를 채울 수 있습니다.

---

### Static Generation with and without Data

Static Generation은 데이터 유무에 관계없이 수행할 수 있습니다.

지금까지 우리가 만든 모든 페이지는 외부 데이터를 가져올 필요가 없습니다. 이러한 페이지는 앱이 프로덕션용으로 빌드될 때 자동으로 정적으로 생성됩니다.

그러나 일부 페이지의 경우 먼저 외부 데이터를 가져오지 않고는 HTML을 렌더링하지 못할 수도 있습니다. 파일 시스템에 엑세스하거나 외부 API를 가져오거나 빌드할 경우에 데이터베이스를 쿼리해야 할 수도 있습니다. Next.js는 이러한 경우에 (Static Generation with data)를 즉시 지원합니다.

![static-generation-with-data](https://user-images.githubusercontent.com/67263146/154225276-a2c31ae7-0204-49d1-bee2-f005aba905cf.png)

어떻게 이것이 동작할까? Next.js에서 페이지 component를 내보낼 때 `getStaticProps` 라는 비동기함수를 export 할 수 있습니다.

- `getStaticProps` 는 build time에 동작합니다.
- 함수안에서, 외부의 데이터를 fetch할 수 있고 데이터를 페이지에 props로 보낼 수 있습니다.

```jsx
export default function Home(props) { ... }

export async function getStaticProps() {
  // Get external data from the file system, API, DB, etc.
  const data = ...

  // The value of the `props` key will be
  //  passed to the `Home` component
  return {
    props: ...
  }
}
```

`getStaticProps` 는 Next.js에게 “이 페이지는 디펜던시 data를 가지고 있고 사전렌더링 때 이 문제를 첫 번째로 해결해야 됩니다.”라고 말하는 것과 같습니다.

---

### getStaticProps

만약에 `getStaticProps` 함수를 export한다면, Next.js는 `getStaticProps` 함수에서 return된 props를 **build time**에 사전렌더링할 것입니다.

그럼 언제 `getStaticProps` 를 사용해야 할까?

- 페이지를 렌더링하는 데 필요한 데이터는 사용자의 요청에 앞서 빌드 시 사용할 수 있습니다.
- 데이터를 공개적으로 캐시할 수 있음(사용자별이 아님)
- 페이지는 미리 렌더링되야 하고(SEO용) 빨라야 되는 경우. `getStaticProps`을 위해 CDN에서 캐시할 수 있는 HTML 및 JSON파일을 생성합니다.

`getStaticProps` 가 **build time**에 동작하기 때문에, 정적 HTML을 생성하므로 들어오는 request요청(예, 쿼리 매개변수 또는 HTTP헤더)에 액세스할 수 없습니다. 만약에 너의 페이지에 request에 접근해야 한다면, 미들웨어 사용을 고려해봐야 합니다.

request time에 data fetch를 원한다면 어떻게 해야될까?  

유저의 요청보다 먼저 페이지를 pre-render할 수 없다면 Static Generation은 좋은 아이디어가 아닙니다. 페이지에 자주 업데이트되는 데이터가 표시되고 모든 요청에 따라 페이지 콘텐츠가 변경될 수 있습니다.

---

### Fetching Data at Request Time

만약 build time이 아닌 request time에 데이터를 패치하는 것을 원한다면, Server side rendering을 사용하는 것이 좋습니다.

서버사이드 렌더링을 사용하기 위해서는 `getStaticProps`가 아닌 `getServerSideProps` 를 export해야 합니다.

```jsx
export async function getServerSideProps(context) {
  return {
    props: {
      // props for your component
    }
  }
}
```

 `getServerSideProps` 는 request time에 호출되기 때문에, 해당 매개변수(contenxt)에는 요청 특정 매개변수가 포함됩니다.

요청시 데이터를 가져와야 하는 페이지를 미리 렌더링해야 하는 경우에만 `getServerSideProps`를 사용해야 합니다. 서버가 모든 요청에 대해 결과를 계산해야 하고 추가 구성없이 CDN에서 결과를 캐시할 수 없기 때문에 TTFB(Time to First byte)는 `getStaticProps`보다 느립니다.

만약에 사전 렌더링 데이터를 필요로 하지 않는다면, 클라이언트 사이드 렌더링 전략을 사용할 수 있습니다. 

- 외부 데이터가 필요하지 않은 페이지 부분을 정적 생성(사전 렌더링)합니다.
- 페이지가 로드되면 Javascript를 사용하여 클라이언트에서 외부 데이터를 가져오고 나머지 부분을 채웁니다.
    
![client-side-rendering](https://user-images.githubusercontent.com/67263146/154225560-4ea6686d-6265-4e8f-b04d-06a022bae518.png)

이 접근 방식은 대시보드 페이지에 적합합니다. 대시보드는 사용자별 비공개 페이지이기 때문에 SEO는 관련이 없으며 페이지를 미리 렌더링할 필요가 없습니다. 데이터는 자주 업데이트되므로 요청 시 데이터를 가져와야 합니다.
