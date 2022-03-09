### Code Splitting

번들링은 훌륭하지만 여러분의 앱이 커지면 번들도 커집니다. 특히 큰 규모의 서드 파티 라이브러리를 추가할 때 실수로 앱이 커져서 로드 시간이 길어지는 것을 방지하기 위해 코드를 주의깊게 살펴야합니다.

번들이 거대해지는 것이 방지하기 위해 좋은 해결방법은 번들을 “나누는” 것입니다. 코드 분할은 여러분의 앱을 “지연 로딩” 하게 도와주고 앱 사용자에게 획기적인 성능 향상을 하게 합니다. 앱의 코드 양을 줄이지 않고도 **사용자가 필요하지 않은 코드를 불러오지 않게 하며 앱의 초기화 로딩에 필요한 비용을 줄여줍니다.**

### React.lazy

`React.lazy` 함수를 사용하면 동적 import를 사용해서 컴포넌트를 렌더링할 수 있습니다.

```tsx
// before
import OtherComponent from "./OtherComponent";
// after
const OtherComponent = React.lazy(()=> import(".OtherComponent"));
```

`MyComponent`가 처음 렌더링 될 때 `OtherComponent`를 포함한 번들을 자동으로 불러옵니다.

`React.lazy`는 동적 `import()`를 호출하는 함수를 인자로 가집니다. 이 함수는 React 컴포넌트를 포함하며 default export를 가진 모듈로 결정되는 `Promise`로 반환해야 합니다.

lazy 컴포넌트는 `Suspense` 컴포넌트 하위에서 렌더링되어야 하며, `Suspense`는 lazy 컴포넌트가 로드되길 기다리는 동안 로딩 화면과 같은 예비 컨텐츠를 보여줄 수 있게 해줍니다.

```tsx
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

`fallback`prop은 컴포넌트가 로드될 때까지 기다리는 동안 렌더링하려는 React 엘리먼트를 받아들입니다. `Suspense`컴포넌트는 lazy 컴포넌트를 감쌉니다. 하나의 `Suspense`컴포넌트로 여러 lazy 컴포넌트를 감쌀 수도 있습니다.

## Routes-based code splitting

코드분할을 어느 곳에서 도입할 지 결정하는 것은 조금 까다롭습니다. 번들을 균등하게 분배하기 좋은 장소는 라우트입니다. 웹 페이지를 불러오는 시간은 페이지 전환에 어느정도 발생하며 대부분 페이지를 한번에 렌더링하기 때문에 사용자가 페이지를 렌더링하는 동안 다른 요소와 상호작용하지 않습니다.

React.lazy를 React Router라이브러리를 사용해서 애플리케이션에 라우트 기반 코드 분할을 설정하는 예시입니다.

```tsx
import React, { Suspense, lazy } from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Suspense>
  </Router>
);
```

### 참고 && 요약

React.lazy 함수를 사용하면 동적 import를 사용해서 컴포넌트를 렌더링을 할 수 있습니다.(코드 스플리팅) 동적 import는 해당 컴포넌트가 사용될 때 해당 리소스를 import하는 것인데 사용자가 필요로 하지 않은 코드를 나누어 앱의 성능이 향상됩니다.

참고: [https://ko.reactjs.org/docs/code-splitting.html](https://ko.reactjs.org/docs/code-splitting.html)
