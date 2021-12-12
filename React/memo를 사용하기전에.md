## 참고 
이 글은 <a href="https://overreacted.io/before-you-memo/">Dan Abramov</a>의 글을 해석한 글입니다.

# 😎memo를 사용하기 전에
React 퍼포먼스 최적화에 대하여 쓴 기사는 많다. 일반적으로, 상태 업그레이드가 느리다면, 다음을 수행해야 한다.

1. 너가 실행하는 production build를 확인해라. (Development builds는 의도적으로 더 느리고, 심지어 극단적인 경우에는 몇 배 이상차이가 난다.)
2. 너의 상태를 필요이상으로 높은 트리에 두지 않았는지 확인해라. (예를 들어, 중앙집권적인 스토어에 input state를 두는 것은 좋은 아이디어가 아닐수도 있다.)
3. React DevTools Profiler를 실행해서 무엇이 리렌더링되는지 확인하고, 가장 비싼 subtrees를 메모로 감싼다. (그리고 useMemo()를 필요한 곳에 추가한다.)

이 마지막 단계는 컴포넌트 사이에서는 특히 성가시고, 이상적으로 미래에는 컴파일러가 너를 위해 이러한 것을 할 것이다.

이 포스트에서, 나는 두 가지 다른 기술을 공유하기를 원한다. 놀라울 정도로 기본적이기 때문에 사람들은 렌더링 성능을 향상시킨다는 사실을 거의 깨닫지 못한다.

이러한 기술은 너가 이미 알고 있는 것과 상호보완적이다. 그들은 memo 또는 useMemo를 재배치하는 것이 아니고, 그것들은 처음으로 시작하는 것이 좋다.

## 느린 컴포넌트(의도적으로)

의도적으로 심각한 렌더링 퍼포먼스를 발생시키는 컴포넌트 예제를 보자.

```jsx
import { useState } from 'react';

export default function App() {
  let [color, setColor] = useState('red');
  return (
    <div>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p style={{ color }}>Hello, world!</p>
      <ExpensiveTree />
    </div>
  );
}

function ExpensiveTree() {
  let now = performance.now();
  while (performance.now() - now < 100) {
    // Artificial delay -- do nothing for 100ms
  }
  return <p>I am a very slow component tree.</p>;
}
```

문제는 App안에 있는 color가 바뀔때마다, 우리는 우리가 인위적으로 느리게 한 ExpensiveTree컴포넌트를 리렌더링 할 것이다.

나는 memo()를 여기다가 적용하고 글을 끝마칠 수 있지만 이것에 대해 많은 글이 존재하기 때문에 나는 여기다가 시간을 쏟지 않을 것이다. 나는 다른 해결책 두 개를 보여주고 싶다.

### Solution 1: Move State Down

너가 렌더링 코드를 자세히 본다면, 너는 반환된 트리의 일부만 현재의 color의 변경에 관심이 있다는 것을 알아채릴 수 있을 것이다.

```jsx
export default function App() {
  let [color, setColor] = useState('red'); // color 
  return (
    <div>
      <input value={color} onChange={(e) => setColor(e.target.value)} /> // color
      <p style={{ color }}>Hello, world!</p> // color
      <ExpensiveTree />
    </div>
  );
}
```

그럼, Form 컴포넌트안에 그 부분을 추출하고 상태를 Form 컴포넌트 안으로 내려보자.

```jsx
export default function App() {
  return (
    <>
      <Form />
      <ExpensiveTree />
    </>
  );
}

function Form() {
  let [color, setColor] = useState('red');
  return (
    <>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p style={{ color }}>Hello, world!</p>
    </>
  );
}
```

위의 예제를 보면 컬러가 변해도 오직 Form 컴포넌트만 리렌더링 된다.

### Solution 2: Lift Content Up

위의 해결책은 비싼 트리위에 사용되는 상태에서는 효과가 없다. 예를 들어, 우리가 부모인 div에 color를 두는 것에 대해여 이야기 해보자.

```jsx
export default function App() {
  let [color, setColor] = useState('red');
  return (
    <div style={{ color }}>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p>Hello, world!</p>
      <ExpensiveTree />
    </div>
  );
}
```

이제 또 다른 컴포넌트에서 사용하지 않는 color의 부분을 "추출"할 수 없는 것처럼 보인다. 왜냐하면 여기에는 ExpensiveTree 컴포넌트가 포함될 상위 <div>가 포함되기 때문이다. 이제는 memo()를 피할 수 없지 않을까?

그 대답은 매우 간단하다.

```jsx
export default function App() {
  return (
    <ColorPicker>
      <p>Hello, world!</p>
      <ExpensiveTree />
    </ColorPicker>
  );
}

function ColorPicker({ children }) {
  let [color, setColor] = useState("red");
  return (
    <div style={{ color }}>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      {children}
    </div>
  );
}
```

우리는 App 구성 요소를 둘로 나눌 수 있다. 색상에 의존하는 부분은 색상 상태 변수 자체와 함께 ColorPicker로 이동되었다.

color에 대하여 관심이 없는 부분은 App에 머물고, 우리가 children prop으로 알고 있는 JSX content로 ColorPicker를 통과했다.

color가 변경될 때, ColorPicker는 리렌더링된다. 그러나 이것은 마지막에 App으로부터 받은 같은 children props를 가지고 있으므로, 리액트는 subtree를 방문하지 않는다.

그 결과로, ExpensiveTree는 리렌더링되지 않는다.

### 그게 무슨 말이야?

우리가 memo 또는 useMemo를 이용해서 최적화를 진행하기 전에, 변경되는 부분과 변경되지 않는 부분을 나눌 수 있는지 확인하는 것이 합리적일 수 있다. 

이러한 접근의 흥미로운 부분은 그들이 그 자체로는, 퍼포먼스 향상에 아무 효과도 없다는 것이다. children prop을 사용해서 컴포넌트를 나누는 것은 일반적으로 데이터의 흐름을 더 쉽게 따라갈 수 있고 트리를 통해 연결되는 props의 수를 줄일 수 있다. 이와 같은 경우에 향상된 성능은 최종 목표가 아니라 마지막 과제이다.

흥미롭게도 이 패턴은 미래에 더 많은 성능 이점을 제공한다.

예를 들어 서버컴포넌트가 안정적이고, 채택할 준비가 되면 우리의 ColorPicker 컴포넌트는 서버로부터 이것들의 자식을 받을 수 있다. 전체의 ExprensiveTree 또는 해당 부분이 서버에서 실행될 수 있고 최상위 React 상태 업데이트는 이러한 부분을 클라이언트에서 생략할 것이다.

그것은 심지어 memo가 할 수 없는 것이다. 그러나 다시 말하면, 이러한 두 가지 접근은 상호보완적이다. 상태를 내리는 것(moving state down)을 방치하면 안된다.(그리고 lifting content up 또한)

그리고 나서, 이것으로 충분하지 않는다면, Profilter를 사용하고 memo를 사용해라.

이것은 새로운 아이디어가 아니다. 이것은 자연스러운 React composition model에 자연스러운 결과이다. 이것은 인정을 덜 받을 만큼 간단하며, 더 많은 사랑을 받을만한 가치가 있다.
