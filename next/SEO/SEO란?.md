## SEO가 중요할까?

더 높은 검색 순위 배치는 더 많은 유기적 방문자와 동일합니다. **검색 엔진 유기적 트래픽(검색엔진에서 결과를 클릭하여 사이트를 방문하는 방문자)** 는 다음 세 가지 이유로 비즈니스의 핵심입니다.

- **Qualitative** ⇒ 방문자가 고객이 될 기회가 증가합니다.
- **Trustable** ⇒ 브랜드에 더 높은 신뢰도
- **Low-Cost** ⇒ 소요된 시간과 노력을 제외하고 검색 엔진 순위를 높이는 좋은 SEO를 갖는 것은 무료입니다. 상위 검색 결과에 표시되는 데 들어가는 비용은 없습니다.

### 검색 엔진 최적화에 세 가지 조건

1. Technical ⇒ 크롤링과 웹 성능을 최적화해야 합니다.
2. Creation ⇒ 특정 키워드에 타겟을 한 컨텐츠 전략을 만들어야 합니다.
3. Popularity ⇒ 검색 엔진이 신뢰할 수 있는 출처임을 알 수 있도록 온라인에서 사이트의 인지도를 높여야 합니다. 이는 사이트로 다시 연결되는 제3자 사이트인 백링크를 사용하여 수행됩니다.
- 백링크: 다른 사이트에서 자신의 사이트로 올 수 있게 연결도니 링크로,  다른 사이트에서 자신의 사이트로 링크를 했다는 얘기는 그만큼 사이트의 내용이 가치가 있고, 유용하며 신뢰할 수 있다는 것으로 구글은 생각하고 구글검색랭킹이 올라가게 된다.
- 하지만 같은 사이트에서 받는 100개의 백링크보다 여러 사이트에서 받는 10개의 백링크가 더 강력하고, 자신의 사이트와 연관 주제를 가진 사이트에서 링크를 받는 것이 더 강력하는 등 여러 전략이 있다.

## Search Systems

검색시스템은 일반적으로 검색 엔진(Google, Bing, DuckDuckGo 등)이라고 합니다. 검색 엔진은 기술역사적으로 가장 큰 도전을 하는 거대하고 복잡한 시스템입니다.

검색 시스템의 주요 업무는 다음과 같습니다.

1. Crawling(크롤링) - 웹을 탐색하고 모든 웹사이트의 콘텐츠를 구문 분석하는 프로세스입니다. 
2. Indexing -크롤링 단계에서 수집된 모든 데이터를 저장할 위치를 찾아 액세스할 수 있습니다.
3. Rendering - 사이트의 기능을 향상하고 컨텐츠를 풍부하게 할 수 있는 JavaScript와 같은 페이지의 모든 리소스를 실행합니다. 이 프로세스는 크롤링되는 모든 페이지에 대해 발생하지 않으며 때로는 콘텐츠가 실제로 인덱싱되기 전에 발생합니다. 해당 시점에 작업을 수행하는데 사용 가능한 리소스가 없는 경우 인덱싱 후에 렌더링이 발생할 수 있습니다.
4. Ranking - 사용자 입력을 기반으로 관련 결과 페이지를 만들기 위해 데이터를 쿼리합니다. 여기에서 다양한 순위 기준이 검색 엔진에 적용되어 사용자에게 의도를 달성하기 위한 최상위 답변을 제공합니다.

### Googlebot의 작동순서

1. URL찾기: Google은 Google Search Console, 웹사이트 간 링크 또는 XML사이트 맵을 비롯한 여러 위치에서 URL을 제공합니다.
2. 크롤링 Queue에 추가: URL들은 구글봇이 처리하기 위해 크롤링 큐에 추가된다. 크롤링큐에 URL들은 일반적으로 몇 초 동안 지속되지만 경우에 따라, 특히 페이지가 렌더되거나 indexed되는 것을 필요로하거나 URL이 이미 indexed되었고 새로 고쳐야 하는 경우 최대 며칠이 소요될 수 있습니다. 그런 다음 페이지는 렌더 큐에 들어갑니다.
3. HTTP Request: 크롤러는 headers를 얻기 위해 HTTP리퀘스트를 만들고 반환된 status code에 따라서 행동합니다.
- 200 - HTML을 파싱하고 크롤링합니다.
- 30X - 리다이렉트를 합니다.
- 40X - 에러를 기록하고 HTML을 로드하지 않습니다.
- 50X - status code가 바뀌게 되면 체크하러 다시 돌아올 수도 있습니다.
4. Render Queue: 검색 시스템의 다양한 서비스 및 구성요소가 HTML을 처리하고 컨텐츠를 구문 분석합니다. 페이지에 일부 JavaScript 클라이언트 측 기반 콘텐츠가 있는 경우 URL이 Render Queue에 추가될 수 있습니다. Render Queue는 Javascript를 렌더링하는 데 더많은 리소스를 사용해야 하기 때문에 Google에 더 많은 비용이 듭니다.
5. Ready to be indexed: 모든 기준이 충족되면 페이지가 인덱싱되고 검색 결과에 표시될 수 있습니다.
