메모리 게임 프로젝트를 진행하면서 9개의 박스 중에 하나를 선택하면 상태값이 변하고 그 상태값으로 animation을 발생시키고 animation이 끝나면 상태값을 초기화하고 싶었는데 여러 방법을 고민하던 중 useLayoutEffect에서 알게 되어서 정리한 글입니다.

참고: [https://merrily-code.tistory.com/46](https://merrily-code.tistory.com/46)

참고: [https://medium.com/@jnso5072/react-useeffect-와-uselayouteffect-의-차이는-무엇일까-e1a13adf1cd5](https://medium.com/@jnso5072/react-useeffect-%EC%99%80-uselayouteffect-%EC%9D%98-%EC%B0%A8%EC%9D%B4%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C-e1a13adf1cd5)

![다운로드](https://user-images.githubusercontent.com/67263146/153022994-7ccba8f5-3132-46bd-8b35-e70a0cdc6744.png)

이 사진은 리액트 hook flow입니다. 사진을 보면 React updates DOM 다음으로 Run LayoutEffects가 실행되는 것을 볼 수 있습니다.

## useLayoutEffect vs useEffect

useEffect는 컴포넌트들이 render와 paint된 후 **비동기적**으로 실행됩니다. paint된 후 실행되기 때문에, **useEffect 내부에 dom에 영향을 주는 코드가 있을 경우** **약간 불평한 사용자 경험으로 이어질 수 있습니다.**

```jsx
import { useEffect, useState } from "react";

function App() {
  const [age, setAge] = useState(0);
  const [name, setName] = useState("");
  
  useEffect(() => {
    setAge(72);
    setName("혜빈");
  }, []);
  
  return (
    <>
      <div className="App">{`그의 이름은 ${name} 이며, 나이는 ${age}살 입니다.`}</div>
    </>
  );
}

export default App;
```

useEffect를 사용하면 위의 코드는 다음 순서대로 작동합니다.

1. <div>그의 이름은 이며, 나이는 0살 입니다.</div>
2. 이펙트 내부의 setNumber, setName 호출
3. 재렌더링 수행 → <div>그의 이름은 혜빈이며, 나이는 72살 입니다.</div>

간단한 DOM구조라면 금방 렌더링이 가능하나 화면이 복잡해지면 체감할 수 있을 정도로 렌더링시간이 증가하게 됩니다. 

useLayoutEffect 훅은 이러한 문제를 해결하기 위해 등장한 훅입니다.

useLayoutEffect은 컴포넌트들이 render된 후 실행되며, 그 이후에 paint가 됩니다. 이 작업은 **동기적으로** 실행됩니다. paint가 되기전에 실행되기 때문에 dom을 조작하는 코드가 존재하더라도 **사용자는 깜빡임을 경험하지 않습니다**.

```jsx
import { useLayoutEffect, useState } from "react";

function App() {
  const [age, setAge] = useState(0);
  const [name, setName] = useState("");
  
  useLayoutEffect(() => {
    setAge(72);
    setName("혜빈");
  }, []);
  
  return (
    <>
      <div className="App">{`그의 이름은 ${name} 이며, 나이는 ${age}살 입니다.`}</div>
    </>
  );
}

export default App;
```

레이아웃 이펙트는 브라우저가 화면에 DOM을 그리기 전에 이펙트를 수행합니다. 따라서 위 코드의 실행 순서도 달라지게 됩니다.

1. 레이아웃 이펙트 내부의 setNumber,setName 호출
2. <div>그의 이름은 혜빈이며,. 나이는 72살입니다.</div> 를 페인트

### 데이터 받아오기

쉽게 접할 수 있는 예제로 유저가 메인화면에 들어오면 data를 서버에서 받고 클라이언트에서 보여줘야 할 때 useEffect와 useLayoutEffect중에 어떤 것을 사용하는 것이 좋을까

useLayoutEffect는 동기적으로 실행되고 내부의 코드가 모두 실행된 후 painting작업을 거칩니다.  따라서 로직이 복잡할 경우 사용자가 레이아웃을 보는데까지 시간이 오래걸린다는 단점이 있습니다. 다음의 항목은 

- 데이터 fetch
- event handler
- state reset

useEffect 사용을 권장합니다.

로직이 복잡하지 않고 첫 상태값에 따라 화면을 다르게 보여줘야 할 경우 useLayoutEffect를 사용하는 것이 좋습니다.
