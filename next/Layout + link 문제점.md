## Layout Component + next/link 에러
```jsx
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <>
      <NavBar />
      <>{children}</>
      <Logo />
      <Footer />
    </>
  )
}
```

Next.js로 프로젝트를 하면서 재사용하는 `NavBar` ,`Footer` , `Logo` 컴포넌트를 공통레이아웃으로 묶어서 성능을 향상하려고 했다. 문제는 NavBar였다. 

`NavBar` 컴포넌트에서는 미디어쿼리를 사용해서 화면이 작아질 경우 햄버거 메뉴가 나오게 구현했다. 햄버거 메뉴는 `useState` 를 사용해서 액티브 상태를 관리했다. 

하지만, 햄버거 메뉴가 액티브한 상태로 `next/link` 를 사용해서 링크를 이동했을 때 `Layout component`로 기존의 상태를 유지해서 햄버거 메뉴가 계속해서 액티브한 에러가있었다.

해결방법정리

- Shallow Routing ..?
- next/link 설정 ⇒ state넘겨주기? ⇒ 쿼리로 넘겨받는 것은 효율적이지 않음
- Layouts 컴포넌트 제거 ⇒ 햄버거 메뉴가 액티브 되는 것은 취소되나 성능, 효율적으로 별로라고 생각함
- 결국 주먹구구 식이지만... Link에 이벤트를 추가해서 클릭했을 때 햄버거 메뉴가 액티브한 상태면 상태값을 false로 변경

### Shallow Routing

Shallow Routing은 `getServerSideProps`, `getStaticProps`, `getInitialProps`과 같은 데이터 fetching메소드를 다시 실행하지 않고 URL을 바꿔주는 라우팅이다.

즉 불필요한 서버 연산을 최소화하고 필요한 상태 값(pathname, query)을 라우터에 넣어서 전달하게 되는 것이다.

```jsx
import { useEffect } from 'react'
import { useRouter } from 'next/router'

// Current URL is '/'
export default function Page() {
  const router = useRouter()

  useEffect(() => {
    // Always do navigations after the first render
    router.push('/?counter=10', undefined, { shallow: true });
  }, [])

  useEffect(() => {
    // The counter changed!
  }, [router.query.counter])
}
```

최초 렌더링 이후 라우터는 `/?counter=10` 으로 업데이트 될 것이다.  

**Shallow Routing 오로지 현재의 페이지에서 URL을 변경하는 것에만 효과가 있다.** 예를 들어 또 다른 페이지인 `pages/about.js` 로 이동한다고 가정해보자.

```jsx
router.push('/?counter=10', '/about?counter=10', { shallow: true })
```

새로운 페이지이므로, 이것은 현재의 페이지를 로드하지 않을 않고 새로운 것을 로드한다. 그리고 비록 shallow routing을 요청했을 지라도 데이터 패칭을 기다린다.

```jsx
import { useRouter } from 'next/router'
import React, { useEffect } from 'react'

export default function Test() {
  const router = useRouter()
  useEffect(() => {
    setTimeout(() => {
      router.push('?counter=10', '?counter/10', { shallow: true })
    }, 3000)
  }, [])

  console.log('render!')

  return (
    <div>
      <h2>Test</h2>
    </div>
  )
}
```

그럼 콘솔에 렌더는 몇 번 찍히게 될까. 정답은 2번이다.

shallow routing이 데이터 패칭없이 URL을 변경하는 라우팅이지만 페이지의 리렌더링은 발생시킨다.
