### Next.js 렌더링
Next.js는 SSR을 기반으로 하지만, 페이지가 로드된 이후에 React에서 CSR을 이용하는 방식을 사용합니다.

1. 페이지는 서버가 그립니다. pages/안에 폴더를 만들면 해당 라우팅의 페이지들은 서버측에서 먼저 로드해줍니다.
2. 페이지가 그려진 이후에 페이지 내부에 동적인 데이터를 패치하는 과정은 CSR의 방식을 따릅니다. 이 때의 데이터들은 일단 페이지가 로드된 이후에 클라이언트 측에서 다시 렌더되며 불러와집니다. 그렇기 때문에 SEO에 걸리지 않습니다.

그렇기 때문에 만약 페이지가 로드될 때 함께 데이터가 패칭되어야 하는 상황이라면 사전렌더링 데이터 패칭 방식을 이용해 첫 렌더에 데이터가 패칭될 수 있도록 처리를 해야 합니다.

### ServerSide Cycle

1. Next Server가 GET요청을 받습니다.
2. 요청에 맞는 Page를 찾습니다.
3. _app.tsx의 getInitialProps가 있다면 실행합니다.
4. Page Component의 getInitialProps가 있다면 실행합니다. pageProps들을 받아옵니다.
5. document.tsx의 getInitialProps가 있다면 실행합니다. pageProps들을 받아옵니다.
6. 모든 props들을 구성하고 _app.js > Page Component순서로 렌더링합니다.
7. 모든 Content를 구성하고 _document.js를 실행하여 HTML형태로 출력합니다.

모든 페이지에 공통적인 데이터 패칭이 필요하다면 _app.tsx에서 미리 데이터 패칭을 하면되고, 페이지마다 다른 데이터가 필요하다면 페이지마다 데이터 패칭을 해주면 됩니다.
