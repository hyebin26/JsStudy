[https://velog.io/@danmin20/Next.js-Typescript-Styled-component-쉽게-구축하기](https://velog.io/@danmin20/Next.js-Typescript-Styled-component-%EC%89%BD%EA%B2%8C-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0) 참고

Next.js + styled-component를 사용하면서 서버 사이드 렌더링을 할 떄 styled-component가 존재하지 않아서 CSS가 적용되지 않고 컴포넌트가 렌더링되는 경우가 발생했고 해결하기 위해 공부한 글입니다.

### _document

커스텀 `document` 는 `<html>` 태그 그리고 `<body>`태그를 사용하여 렌더하는 페이지에 업데이트할 수 있습니다. 이 파일은 오직 서버에서만 사용되고, 그래서 `onClick`같은 이벤트 핸들러는 `_document` 에서 사용할 수 없습니다.

### getInitialProps

getInitialProps ⇒ 페이지에서 SSR을 하고 초기 데이터를 설정할 수 있게 해줍니다. 그 의미는 서버로 부터 미리 받은 데이터를 함께 페이지에 보냅니다. 이것은 SEO에 유용합니다.

`getInitialProps`는 `static method` 로 모든 페이지에 추가될 수 있는 비동기 함수입니다.

`getIntialProps` 는 일부 데이터를 비동기적으로 가져오는데 사용되고 `props` 로 밀집된다

첫 페이지의 로딩에서, `getInitialProps` 는 오직 서버에서 동작합니다. 그리고나서 `getInitialProps` 는 사용자가 링크를 타고 다른 컴포넌트로 이동했을 때 혹은 router를 이용해서 이동했을 때 클라이언트에서 동작합니다. 그러나 `getIntialProps` 가 커스텀`_app.js` 에서 사용되거나 이동한 페이지가 `getServerSideProps` 를 실행한다면, `getInitialProps` 는 서버에서 동작합니다.    

### .babelrc vs babel.config.js

- babel.config.js: 프로젝트 전체 구성시 사용되는 babel.config.json, babel.config.js등은 여러 패키지 디렉토리를 가진 프로젝트에서 하나의 바벨 설정을 할 때 사용
- .babelrc: 파일/ 디렉토리 하위 집합에서 특정 변환/플러그인을 실행하려는 경우에 유용하다. 프로젝트 내에 서드파티 라이브러리가 바벨에 의해 변환되지 않기를 원할 수도 있기 때문입니다.
- .babelrc를 사용한 이유는 getInitialProps로 클라이언트가 아닌 서버에서 렌더링을 할 때 styled-component를 추가했습니다. 그리고 SSR을 할 때의 className과 CSR의 ClassName의 이름이 매치되지 않아서 바벨을 추가해서 매칭시켰습니다.
