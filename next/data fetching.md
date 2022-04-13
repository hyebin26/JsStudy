# Data Fetching

## getServerSideProps

페이지에서 `getServerSideProps` 로 불리는 함수를 export한다면, Next.js는 `getServerSideProps` 로 반환된 데이터를 사용하여 각각의 요청(request)에서 이 페이지를 사전렌더링합니다.

```jsx
export async function getServerSideProps(ctx){
	return {
    props:{}, // 페이지 컴포넌트를 지나갑니다.
	}
}
```

### 그럼 언제 getServerSideProps가 동작할까

`getServerSideProps`는 오직 서버사이드에서 동작하고 절대 브라우저에서는 동작하지 않습니다. 만약에 페이지에서 `getServerSideProps`를 사용하면,

- 해당 페이지에서 직접적으로 요청했을 때, `getServerSideProps` 는 request time에 동작하고 해당 페이지는 반한된 props와 함께 사전렌더링 됩니다.
- `next/link` 또는 `next/router` 를 통하여 클라이언트 사이드에서 페이지의 전환을 요청하면, Next.js는 서버에 API 요청을 보내고, `getServerSideProps` 를 동작합니다.

`getServerSideProps` 는 페이지에 렌더에 사용되는 JSON을 리턴합니다.  이러한 일들에 모든 것은 Next.js에 의해 자동적으로 다뤄지며, `getServerSideProps` 를 사용하면 추가의 다른 것들을 요구하지 않습니다.

### getServerSideProps를 사용해야 될 때

오직 request time에 데이터를 `fetch`해야되는 페이지를 사전렌더링을 해야 하는 경우에  `getServerSideProps`를 사용해야 합니다.  

데이터를 사전렌더링하는 것을 필요로 하지 않는다면 client-side 데이터를 패칭하는 것을 고려해야만 합니다.

### Fetching data on the client side

페이지가 자주 업데이트되는 데이터를 포함하거나 데이터를 사전렌더링하는 것을 필요로 하지 않으면 client-side에서 데이터를 `fetch`할 수 있습니다. 예를 들면 특정한 유저 데이터입니다.

- 처음으로, 데이터 없이 페이지를 보여줍니다. 페이지의 일부분은 Static Generation을 사용하여 사전렌더링 될 수 있습니다.
- 그리고나서 client-side에서 데이터를 패치하고 데이터가 준비될 때 보여줍니다.

예를 들어 이러한 접근은 dashboard페이지에서 효과가 있습니다. 왜냐하면 dashboard는 사전렌더링되는 것을 필요로 하지 않고 SEO와 연관되어 있지 않고 사적이고, 특정한 유저 페이지이기 때문입니다. 데이터는 자주 업데이트되므로 요청 시 데이터를 가져와야 합니다.


## getStaticPaths

페이지가 Dynamic Routes를 사용하고 있고 `getStaticProps` 를 사용한다면 정적으로 생성된 paths에 리스트를 정의하는 것을 필요로 합니다.

```jsx
export async function getStaticPaths(){
	return {
    paths: [
      { params: { id: '1' } },
      { params: { id: '2' } }
    ],
    fallback: ...
  }
}
```

`getStaticPaths` 는 `getStaticProps` 와 같이 사용되어야 합니다. `getServerSideProps` 와 사용될 수 없습니다.

Dynamic Routes를 사용하는 페이지로 부터 `getStaticPaths` 함수를 exporting할 때, Next.js는 정적으로 `getStaticPaths`에 의해 정의된 모든 paths를 사전렌더링할 것입니다.

`getStaticProps` 는 오직 `getStaticProps` 를 사용하는 dynamic route로부터 export될 때 사용할 수 있습니다. 너의 components폴더에 있는 페이지 파일이 아닌 것으로 부터 export할 수 없습니다. 

`fallback`이 `true`라면, `getStaticProps`는 밑의 변화를 따릅니다.

- `getStaticPaths` 로 반환된 paths는 `getStaticProps`에 의해 build time에 HTML로 렌더됩니다.
- 빌드타임에 생성되지 않은 paths는 404페이지를 생성하지 않습니다. 대신에 Next.js는 이러한 경로에 대한 첫 번째 요청에서 페이지의 `fallback` version을 제공합니다. Google과 같은 웹크롤러는 대체 서비스를 제공하지 않으며 대신 경로가 `fallback:blocking` 과 같이 동작합니다.
- 백그라운드에서, Next.js는 정적으로 요청된 path인 HTML과 JSON을 생성합니다. 여기서 `getStaticProps` 의 동작이 포함됩니다.
- 완료되면 브라우저는 생성된 path에 JSON을 수신합니다. 이것은 필요로하는 props와 함께 페이지에 렌더할 때 자동적으로 사용될 것입니다. 유저의 관점에서 보면, 페이지는 fallback page에서 full page로 스왑이 되는 것입니다.
- 동시에 Next.js는 이 경로를 미리 렌더링된 페이지 목록에 추가합니다. 동일한 경로에 대한 후속 요청은 빌드 시 미리 렌더링된 다른 페이지와 마찬가지로 생성된 페이지를 제공합니다.

### 언제 fallback:true가 유용할까

`fallback:true` 는 앱에 데이터에 의존하는 정적 페이지가 많은 경우(ex. 매우 큰 e-commerce 사이트)에 유용합니다. 모든 제품 페이지를 미리 렌더링하려면 빌드 시간이 매우 오래 걸립니다.

대신 페이지의 작은 하위 집합을 정적으로 생성하여 `fallback:true` 하고 나머지를 사용할 수 있습니다. 누군가 아직 생성되지 않은 페이지를 요청하면 사용자는 로딩 스패너 혹은 스켈레톤 UI요소가 있는 페이지를 보게 됩니다.

잠시후 `getStaticProps` 가 완료되고 페이지가 요청된 데이터로 렌더링됩니다. 이제부터 동일한 페이지를 요청하는 모든 사람은 정적으로 사전 렌더링된 페이지를 받게 됩니다.

이를 통해 사용자는 빠른 빌드와 정적 생성의 이점을 유지하면서 항상 빠른 경험을 할 수 있습니다.

`fallback:true` 는 생성된 페이지를 업데이트 하지 않습니다.  생성된 페이지를 업데이트하기 위해서는 Incremental Static Regeneration을 확인하세요.

### 언제 getStaticPaths가 동작할까

`getStaticPaths` 는 오직 production의 build하는 동안 동작하고 이것은 runtime에 동작하지 않습니다. 

- `getStaticProps` 는 build 하는 동안 반환된 특정한 paths가 next build하는 동안 동작합니다.
- `getStaticProps` 는  를 사용할 때 뒤에서 동작합니다.
- `getStaticProps` 는 `fallback:blocking` 를 사용할 때 첫 렌더링 전에 호출됩니다.


## getStaticProps

페이지로부터 `getStaticProps` 함수를 export한다면 Next.js는 `getStaticProps`에 반환된 props를 사용하여 빌드타임에 페이지를 사전렌더링 할 것입니다.

`getStaticProps` 은 밑의 항목에서 사용할 수 있습니다.

- 렌더할 때 데이터를 필요로하는 페이지는 유저의 요청에 앞서 빌드타임에 이용할 수 있습니다.
- headless cms로 부터 오는 데이터
- 데이터를 공개적으로 캐시할 수 있음(특정한 사용자 별이 아님)
- 페이지는 SEO를 위해 사전렌더링 되어야 하고 매우 빠릅니다. ⇒ `getStaticProps` 는 HTML 과 JSON파일을 생성하고, 성능을 위해 CDN으로 캐쉬될 수 있습니다.

```jsx
export default function index({ message }: { message: string }) {
  console.log('Product', message); // Next.js awesome
  return (
    <>
      <Head>
        <title>
          Product
        </title>
        <meta name="description" content="Product" />
      </Head>
      <NavBar />
      <Products />
    </>
  )
}

export async function getStaticProps() {
  console.log('getStaticProps!')
  return {
    props: { message: 'Next.js is awesome' },
  }
}
```

`getStaticProps` 는 서버에서 동작하고 client에서는 절대 동작하지 않습니다. 진행 순서를 확인하면

- `getStaticProps` 는 항상 `next build`하는 동안 동작합니다.
- `getStaticProps` 는 `revalidate` 를 사용할 때 뒤에서 동작합니다.
- `getStaticProps` 는  `unstable_revalidate` 를 사용할 때 뒤에서 주문에 맞게 동작합니다.

`getStaticProps` 는 오직 페이지 컴포넌트가 아닌 페이지에서 export할 수 있습니다. 이러한 제한의 이유는 React가 페이지가 렌더되기 전에 모든 필요로 하는 데이터를 가지는 것을 필요로하기 때문입니다.

### HTML과 JSON 둘다  정적으로 생성하기

`getStaticProps` 를 사용하여 빌드타임에 페이지를 사전렌더링 할 때, 페이지 HTML파일 외에 Next.js가 실행 결과를 담은 JSON파일을 생성합니다.

이러한 JSON 파일은 `next/link` 그리고 `next/router` 통하여 클라이언트에서 라우팅에서 사용됩니다. `getStaticProps` 를 사용하여 사전렌더링한 페이지는 네비게이트할 때, Next.js는 JSON file(빌드타임에 미리 계산한)을 fetch하고 page의 컴포넌트에 props로서 사용합니다. 클라이언트의 페이지 변환은 오직 export된 JSON을 사용함으로서 `getStaticProps` 를 호출하지 않는다는 것을 의미합니다.

Incremental Static Generation을 사용할 때, `getStaticProsp`는 클라이언트 네비게이션을 위해 필요로하는 JSON을 생성하기 위해 background에서 생성됩니다. 동일한 페이지에 대한 여러 요청의 형태로 이를 볼 수 있지만 의도된 것이며 최종 사용자 성능에 영향을 미치지 않습니다.

## Incremental Static Regeneration

Next.js는 사이트를 build한 후에 정적인 페이지를 업데이트하거나 생성하는 것을 허락합니다. Incremental Static Regeneration(ISR)는 전체의 사이트를 리빌드하지 않고 해당 페이지에 정적인 페이지를 사용하는 것을 가능하게 합니다. ISR를 사용하여면 수백만 페이지로 확장하면서 정적 페이지의 이점을 유지할 수 있습니다.

```jsx
export async function getStaticProps() {
  console.log('getStaticProps!')
  const hello = new Date()
  return {
    props: { message: hello.getSeconds() },
    revalidate: 10,
  }
}
```

빌드 시 사전 렌더링된 페이지에 대한 요청이 이루어지면 처음에는 캐시된 페이지가 표시됩니다.

- 초기 요청 후 10초 전에 페이지에 대한 모든 요청도 캐시되고 즉각적입니다.
- 10초 후 다음 요청은 여전히 캐시된(부실) 페이지를 표시합니다.
- Next.js는 백그라운드에서 페이지 재생성을 트리거합니다.
- 페이지가 성공적으로 생성되면 Next.js는 캐시를 무효화하고 업데이트된 페이지를 표시합니다. 백그라운드 재생성이 실패하면 이전 페이지는 여전히 변경되지 않습니다.

생성되지 않은 경로에 대한 요청이 만들어지면 Next.js는 첫 번째 요청에서 페이지를 서버 렌더링합니다. 향후 요청은 캐시에서 정적 파일을 제공합니다. 

1. 페이지를 처음 방문하면 데이터는 SSG방식과 마찬가지로 빌드타임에 가져온 데이터입니다.
2. 그러나 ISR은 revalidate시간이 지나게 되면 이 페이지만 서버사이드에서 재빌드하게 되고 API백그라운드에서 접속하기 때문에 클라이언트쪽에 보이는 데이터는 변함이 없습니다.
3. revalidate시간 후에 새로고침을 누르면 ISR이 페이지를 재빌딩하는 시간 후에 렌더링됩니다.

위의 예제를 확인하면 첫 콘솔에 2가 나오게 되면 다시 해당 페이지에 재접속하면 build할 때 콘솔에 찍힌 2가 아니라 12라는 숫자가 콘솔에 나오게 됩니다.
