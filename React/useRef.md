# useRef

React에서 Ref는 Reference의 줄임말이고, DOM요소를 참조한다고 해서 붙인 이름 같습니다. 일반적으로 우리는 DOM에 접근하기 위해 Ref를 사용해왔습니다. 하지만 React Hook을 이용해 useRef라는 API가 공개됐습니다. 이전 버전의 createRef를 단순히 hook으로 처리했다라고만 알고 있었습니다. 그런데 react-redux의 useSelector소스코드에서 useRef를 적극적으로 사용하는 것을 보고 의문이 생겼습니다.

코드에서는 공식문서에 설명된 목적(DOM노드나 React 엘리먼트에 접근하기 위한 방법)이 아니라 일종의 변수를 관리하기 위한 목적으로 사용하고 있었습니다. useRef의 문서에 따르면 DOM뿐만 아니라 어떤 값이든 저장할 수 있는 **일반적인 자바스크립트 객체**라는 표현을 찾을 수 있었습니다. 또한 부연설명으로 **매번 렌더링할 때 동일한 객체를 제공한다는 점과 값이 변경될 때 리렌더링이 발생하지 않는다는 점도 써있었습니다.**

일반적인 프로그래밍 언어는 heap영역과 stack영역에서 메모리를 관리합니다. stack공간은 자바스크립트 single thread call stack을 말하는 그 stack으로, 함수가 실행될 때 메모리에 할당됐다가 종료되면서 한꺼번에 해채됩니다.

반면 heap은 전역변수와 참조타입의 변수를 할당하고 가비지 컬렉터를 이용해 사용하지 않은(=참조되지 않은)메모리를 해체시킵니다. 즉 우리가 자바스크립트 객체로 만드는 변수들은 heap공간에 할당되었다가 해체됩니다.

다시 useRef의 설명으로 돌아가보겠습니다.

1. useRef는 일반적인 자바스크립트 객체입니다.

- 즉 heap영역에 저장되는 변수입니다.

2. 매번 렌더링할 때 동일한 객체를 제공합니다.

- heap에 저장되어 있기 때문에 애플리케이션이 종료되거나 가비지 컬렉팅될 때 까지 참조 할떄마다 같은 메모리값을 가진다고 할 수 있습니다.

3. 값이 변경되어도 리렌더링이 되지 않습니다.

- 같은 메모리 주소를 갖고 있기 때문에 자바스크립트의 === 연산이 항상 true를 반환합니다. 즉 변경사항을 감지할 수 없어서 리렌더링을 하지 않는다는 뜻입니다.


```jsx
import { useRef } from "react";

export default function App() {
  const countRef = useRef(0);

  const handle = () => {
    countRef.current++;
    console.log(`Clicked ${countRef.current} times`);
  };

  console.log("render");
  return (
    <>
      <button onClick={handle}>Click me</button>
    </>
  );
}
```

위의 예제는 버튼을 클릭하면 countRef.current의 값을 1씩 증가시킵니다. 하지만 useState와는 달리 값이 변해도 리렌더링이 되지 않기 때문에 클릭을 해도 console.log(”render”)는 출력되지 않는 것을 확인할 수 있습니다. 

함수형 컴포넌트는 인스턴스를 리턴하는 클래스형 컴포넌트와 다르게 작동합니다. 렌더링 될 때 마다 매번 새로운 변수를 스택에 할당해 값이 초기화되기도 하고, 불필요한 성능낭비를 하게 될 수 있습니다. 클래스 컴포넌트는 인스턴스를 생성해서 렌더링 메소드만 재실행하는 구조였다면, 함수형 컴포넌트는 매번 함수(=함수형 컴포넌트의 렌더링)를 실행하기 때문입니다.

함수형 컴포넌트에서 변수를 다루기 쉽게 하기 위해 만들어진 API입니다. 

1. hook기반의 useState혹은 useContext로 선언
- 이렇게 선언한 변수들은 값이 바뀔 때 마다 re-rendering을 유발합니다. 렌더링과 상관없는 변수를 선언하기에 적당하지 않습니다.
2. 함수형 컴포넌트 내부에 const,let으로 선언
- 렌더링 될 때 마다 값이 초기화됩니다. 컴포넌트의 생애주기 동안 관리해야 하는 변수를 선언하기에 적당하지 않습니다.
3. useRef를 이용한 선언
- useRef를 통해 선언된 변수는 리렌더링을 유발하지도 않고 리렌더링 될 때도 이전의 값을 기억하고 있으며, 컴포넌트마다 각각의 값을 가질 수 있습니다.

## 결론

useRef는 클래스의 인스턴스 프로퍼티와 같다고 생각하시면 됩니다. 컴포넌트 내부에서 관리하는 변수인데, 값이 바뀔 때마다 렌더링이 필요하면 useState를 쓰면 되고 아닐 경우 useRef를 써야한다고 생각하시면 간단할 것 같습니다.

## 참고
https://dev.to/dylanju/useref-3j37?fbclid=IwAR0fl7xMjn_Hp6sImCU-EQt4gJ0ob_YY6hS3cwn4ARyClTUYD2KN0R6X-O0

https://genglog.me/blog/210507
