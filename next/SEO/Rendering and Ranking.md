### 렌더링 전략

1. Static Site Generation(SSG): SSG는 build time에 HTML을 생성하는 것입니다. 그리고나서 HTML은 각각의 요청에 사용됩니다. SSG는 사전 렌더링이 되기 때문에 모든 HTML이 있을 뿐만 아니라 페이지 성능에도 도움이 되기 때문에, SEO를 위한 렌더링 전략 중에 최고의 타입중에 하나일 수 있습니다.
2. Server-Side Rendering(SSR): SSG와 마찬가지로 SSR은 사전 렌더링되므로 SEO에도 적합합니다. SSG에서와 같이 빌드 시 생성되는 SSR은 HTML은 요청 시 생성됩니다. 이것은 매우 동적인 페이지가 있을 때 유용합니다. 
3. Incremental Static Regeneration(ISR): 매우 많은 페이지가 있는 경우 build time에 페이지를 모두 생성하는 것이 불가능할 수도 있습니다. Next.js를 사용하면 사이트를 구축한 후 정적 페이지를 만들거나 업데이트할 수 있습니다. **Increment Static Regeneration을 통해 개발자와 컨텐츠 편집자는 전체 사이트를 다시 빌드할 필요없이 페이지별로 정적 생성을 사용할 수 있습니다.** 

요약: SEO에서 가장 중요한 것은 Javascript 없이 페이지 로드 시 페이지 데이터와 메타데이터를 사용할 수 있다는 것입니다. 이 경우 SSG또는 SSR이 최선의 선택이 될 것입니다. Next.js에 주요 장점 중 하나는 위의 각 렌더링 방법을 페이지 단위로 수행할 수 있다는 것입니다. 블로그 게시물을 정적으로 생성하고 클라이언트 측을 렌더링한 다음 서버 측에서 렌더링하려는 new feed를 원할 수 있습니다.

### AMP(Accelerated Mobile Pages)란?

2016년에, Google은 AMP를 사용하여 웹 페이지에 search ranking preference를 주는 것을 시작했습니다. AMP는 모바일 장치에 더 빠르게 로드되는 웹 페이지를 만드는 것을 가능하게 해주는 기술입니다.

Core Web Vitals와 함께 페이지의 경험의 향상되면서, Google은 검색 캐러셀에 표시하기 위한 요구사항으로 AMP페이지를 삭제했습니다. 이것은 SEO목적 측면에서 Google이 AMP에 대해 가진 마지막 주요 이점 중에 하나입니다.

AMP가 도입된 이후 Next.js와 같은 새로운 기술은 개발자 경험을 희생하지 않고도 웹사이트 경험을 개선할 수 있는 능력이 입증되었습니다.

구글에서는 공식적으로 AMP는 SEO에 필수적이라고 하진 않지만, 최근 웹바이탈 지표를 발표하면서 속도에 대한 웹페이지 평가요소에 무게를 싣고 있는 경향입니다. 실제로 SEO작업 중, AMP전/후로 검색 퍼포먼스가 현저하게 달라지는 사례를 볼 수 있었습니다.

### URL구조

URL구조는 SEO전략의 중요한 부분입니다. Google은 SEO의 각 부분에 어떤 비중이 있는지 공개하진 않지만, 훌륭한 URL은 결국 순위 요인이 크든 작든 상관없이 모범 사례로 간주됩니다.

- **Semantic**: 의미 있는 URL을 사용하는 것이 가장 좋습니다. 즉 IDs나 무작위의 숫자 대신에 단어를 사용합니다. 예를 들어 `/learn/course-1/lesson-1` 보다 `[/learn/basics/create-nextjs-app](https://nextjs.org/learn/basics/create-nextjs-app)` 이 좋습니다.
- **논리적이고 일관된 패턴**: URL은 페이지 간에 일관된 패턴을 따라야 합니다. 예를 들어 가지고 있는 각 제품에 대해 다른 경로를 사용하는 대신 모든 제품 페이지를 그룹화하는 폴더를 원합니다.
- **키워드 중심**: Google은 여전히 웹사이트에 포함된 키워드에 시스템의 상당 부분을 기반으로 합니다. 페이지의 목적을 쉽게 이해할 수 있도록 URL에 키워드를 사용할 수 있습니다.
- **매개변수 기반 아님**: 매개변수를 사용하여 URL을 작성하는 것은 일반적으로 좋은 생각이 아닙니다. 대부분의 경우 의미가 없으며 검색 엔진에서 혼동을 일으키고 결과에서 순위를 낮출 수 있습니다.

### Next.js에서 경로

Next.js는 페이지개념을 기반으로 구축된 파일 시스템 라우팅을 사용합니다. 예를 들어,

- 홈페이지: [`https://www.example.com`](https://www.example.com/) ⇒ `pages/index.js`
- 목록: [https://www.example.com/products](https://www.example.com/products)⇒`pages/products.js`또는 `pages/products/index.js`
- 세부사항: [`https://www.example.com/products/product`](https://www.example.com/products/product) ⇒ `pages/products/product.js`

블로그 또는 전자 상거래 사이트의 경우 동적라우팅을 사용해야 합니다.

- 제품: [`https://www.example.com/products/nextjs-shirt`](https://www.example.com/products/nextjs-shirt) ⇒ `pages/products/[product].js`

예제는 [https://nextjs.org/learn/seo/rendering-and-ranking/url-structure](https://nextjs.org/learn/seo/rendering-and-ranking/url-structure) 참고

### Metadata

Metadata는 웹사이트 컨텐츠의 요약이며 사이트에 제목, 설명 및 이미지를 첨부하는데 사용됩니다.

1. Title 
- 제목 태그는 두 가지 주요 이유로 가장 중요한 SEO요소 중 하나입니다.
- 사용자가 검색 결과에서 클릭하여 웹사이트에 들어갈 떄 보게되는 것입니다.
- Google이 페이지 내용을 이해하는데 사용하는 주요 요소 중 하나입니다. 제목에 키워드를 사용하면 일반적으로 검색 엔진에서 순위가 향상되기 때문에 권장됩니다.
- `<title>iPhone 12 XS Max For sale in Colorado - Big Discounts | Apple</title>`
- 이 페이지에는 모든 주요 키워드가 포함되어 있으며 다음과 같은 명확한 가치 제안을 보여주는 사용자에게 매력적입니다.
2. Description
- Description 메타 태그는 제목보다는 덜 중요합니다. Google에 따르면 이 요소는 순위 지정 목적으로 고려되지 않지만 검색 결과의 클릭률에 영향을 줄 수 있습니다.
- 제목의 정보를 보완하고 제목에 맞지 않은 키워드가 있을 경우 컨탠츠에 더 많은 키워드를 입력하세요. 이러한 키워드는 검색에 포함된 경우 굵게 표시됩니다.

```jsx
<meta
  name="description"
  content="Check out iPhone 12 XR Pro and iPhone 12 Pro Max. Visit your local store and for expert advice."
/>
```

3. Open Graph

원래 Facebook에서 개발한 Open Graph Protocol은 지정된 웹 페이지에서 메타데이터가 사용되는 방식을 표준화합니다. 원하는 만큼 정보를 제공할 수 있지만 Open Graph 조각들은 연결된 사이트의 표현을 만드는 것에 적절합니다.

이러한 Open Graph 태그들은 SEO와 관련된 태그와 많은 유사성이 있으나, 검색 엔진 랭킹에 이점을 재공하지는 않습니다. 하지만 그들은 소셜미디어 혹은 WhatsApp, Telegram같은 메시징 툴에 사람들이 너의 컨텐츠를 공유할 수도 있으므로 사용을 추천합니다.

너는 `Head` 컴포넌트 내부의 매타 태그에 `property` 속성을 정의하여 Open Graph태그를 추가할 수 있습니다. 예제는 [https://nextjs.org/learn/seo/rendering-and-ranking/metadata](https://nextjs.org/learn/seo/rendering-and-ranking/metadata) 참고

4. 구조화된 데이터 및 JSON-LD

페이지는 HTML로 이루어져 있습니다. HTML은 실제로 그 페이지를 방문했을 때의 형태를 표시해 줄 뿐, 아직 방문도 안한 시점에서 이 데이터가 어떤 데이터인지는 파악할 수 없는데, 이를 파악하기 위해 구조화된 데이터(스키마 마크업)가 필요합니다. HTML이 각 데이터에 대한 설명을 나타내기에는 부족하기 떄문에 이에 설명을 추가해주는 것을 말합니다.

그 구조화된 데이터를 만드는데 필요한 것은 Schema.org로 인터넷, 웹페이지, 전자 메일 메시지 등의 구조화된 데이터에 대한 스키마 마크업을 생성해야 하는데 그 방법중 하나가 JSON-LD입니다. 즉 JSON-LD는 구조화된 데이터를 만드는 하나의 방법입니다.

구조화된 데이터는 검색 엔진이 페이지를 이해하는 것을 도와줍니다.

```jsx
import Head from 'next/head'

function ProductPage() {
  function addProductJsonLd() {
    return {
      __html: `{
      "@context": "https://schema.org/",
      "@type": "Product",
      "name": "Executive Anvil",
      "image": [
        "https://example.com/photos/1x1/photo.jpg",
        "https://example.com/photos/4x3/photo.jpg",
        "https://example.com/photos/16x9/photo.jpg"
       ],
      "description": "Sleeker than ACME's Classic Anvil, the Executive Anvil is perfect for the business traveler looking for something to drop from a height.",
      "sku": "0446310786",
      "mpn": "925872",
      "brand": {
        "@type": "Brand",
        "name": "ACME"
      },
      "review": {
        "@type": "Review",
        "reviewRating": {
          "@type": "Rating",
          "ratingValue": "4",
          "bestRating": "5"
        },
        "author": {
          "@type": "Person",
          "name": "Fred Benson"
        }
      },
      "aggregateRating": {
        "@type": "AggregateRating",
        "ratingValue": "4.4",
        "reviewCount": "89"
      },
      "offers": {
        "@type": "Offer",
        "url": "https://example.com/anvil",
        "priceCurrency": "USD",
        "price": "119.99",
        "priceValidUntil": "2020-11-20",
        "itemCondition": "https://schema.org/UsedCondition",
        "availability": "https://schema.org/InStock"
      }
    }
  `
    }
  }
  return (
    <div>
      <Head>
        <title>My Product</title>
        <meta
          name="description"
          content="Super product with free shipping."
          key="desc"
        />
        <script
          type="application/ld+json"
          dangerouslySetInnerHTML={addProductJsonLd()}
          key="product-jsonld"
        />
      </Head>
      <h1>My Product</h1>
      <p>Super product for sale.</p>
    </div>
  )
}

export default ProductPage
```

### 페이지 SEO

높은 수준에서 페이지 SEO는 페이지의 전체 구조를 구성하는 제목과 링크를 나타냅니다. 제목은 문서의 중요성을 나타내며 링크는 웹을 서로 연결합니다.

### Heading and H1

제목은 사용자가 페이지의 구조와 다음 단락에서 읽을 내용을 이해하는데 도움이 됩니다. 또한 페이지의 어느 부분이 가장 중요한지 이해하는 검색 엔진의 작업을 용이하게 합니다.

제목은 1-6으로 이동하며 H1이 가장 중요하다고 생각되는 경향이 있습니다. 각 페이지에 H1 제목태그를 사용하는 것이 좋습니다. H1은 페이지의 내용을 나타내야 하며, `title`태그와 유사해야 합니다.

```jsx
function Page() {
  return <h1>Your Main Page Heading</h1>
}
```

### Internal Links

인터넷은 링크로 연결되어 있습니다. 한 웹사이트에서 다른 웹사이트로 연결되는 링크가 없다면 인터넷은 아마 존재하지 않을 것입니다. 더 많은 링크를 수신하는 웹사이트는 사용자가 더 신뢰하는 웹사이트를 나타내는 경향이 있습니다.

PageRank알고리즘은 데이터베이스의 모든 링크를 통과하고 수신하는 링크의 수와 도메인에 따라 도메인의 점수를 매기는 알고리즘입니다. 하지만 스팸 웹사이트의 많은 링크는 가치가 거의 또는 전혀 없습니다.

그러나 대규모 웹사이트의 링크는 검색 엔진에 매우 유용할 수 있습니다. 이것이 링크가 중요한 이유이며 항상 페이지 내부와 외부 웹사이트에 모두에 링크를 포함해야 합니다. 링크는 PageRank계싼에 사용되기 위해 항상 `href`를 사용해야 합니다.
