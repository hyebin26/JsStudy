# React Portal

```jsx
ReactDOM.createPortal(child, container)
```

React의 공식 문서를 보면 **Portal은 부모 컴포넌트의 DOM 계층 구조 바깥에 있는 DOM노드로 자식을 렌더링하는 최고의 방법을 제공한다**라고 나와있다. 잘 이해가 가지 않는다.

첫 번째 인자(child)는 엘리먼트, 문자열, 혹은 fragment와 같은 어떤 종류이든 렌더링할 수 있는 React자식이다. 두 번째 인자는(container)는 DOM엘리먼트이다.

간단하게 말해 Portal은 게임에서 접할 수 있는 Portal처럼 렌더링할 수 있는 React자식 컴포넌트를 React의 기본적인 흐름구조(Tree)를 무시하고 해당 DOM로 이동시켜준다 

### 예제

```jsx
import React, { useState } from "react";
import ReactDOM from "react-dom";
import NotPortal from "./notPortal";

const Portal = ({ children }) => {
  const globalPortal = document.getElementById("global-portal");
  return ReactDOM.createPortal(children, globalPortal);
};

const NotPortal = ({ children }) => {
  return (
    <div>
      <h1>Not Portal</h1>
      {children}
    </div>
  );
};

export default function App() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <h1>{count}</h1>
      <Portal>
        <button onClick={() => setCount(count + 1)}>Portal +1</button>
      </Portal>
      <NotPortal>
        <button onClick={() => setCount(count + 1)}>NotPortal +1</button>
      </NotPortal>
    </div>
  );
}
```

![0003](https://user-images.githubusercontent.com/67263146/151485694-c9de4e89-fff1-4d80-b67f-f08e45338c9a.png)

위의 코드를 보면 `Portal` 은 index.html에 `<div id=”global-portal”></div>`  div컨테이너를 만들고 Portal로 연결해서 children을 렌더링하였다. 그리고 Portal밑에 있는 NotPortal 컴포넌트는 App컴포넌트 위에서 확인할 수 있다. 그럼`Portal+1` 버튼과 `NotPortal+1`두 버튼 다 정상적으로 동작할까?

두 버튼은 정상적으로 동작하지만 차이가 있다. 바로 컴포넌트 위치에 차이이다. React Portal로 연결한 Portal컴포넌트는 NotPortal컴포넌트와 같이 App컴포넌트 안에 코드가 쓰여있지만 렌더링 될 때 <div id=”root”> 가 아니라 <div id=”global-portal”> 즉, 다른 컨테이너에 존재하는 걸 볼 수 있다.

![0002](https://user-images.githubusercontent.com/67263146/151485795-f7028509-4f32-4669-8d69-619cc0924164.png)

위에서 볼 수 있듯이 React Portal의 해당 DOM 노드가 아니라 외부의 DOM노드에서도 해당 DOM노드에 존재하는 것처럼 상태를 공유할 수 있다는 장점이 있어 모달에 사용이 자주된다. 또한 Portal을 이용해도 해당 DOM노드에 존재하는 것처럼 이벤트가 버블링된다.


참고: [https://ko.reactjs.org/docs/portals.html](https://ko.reactjs.org/docs/portals.html)
