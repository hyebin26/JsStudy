React가 리렌더링 될 때
1. State가 변경되었을 때
2. 부모의 Component가 리렌더링될 때 
3. 새로운 prop이 들어올 떄 
4. shouldComponenetUpdateTrue
...등등

React는 컴포넌트를 렌더링한 뒤, 이전 렌더링 결과와 비교하여 DOM 업데이트를 결정합니다. 만약 렌더 결과가 이전과 다르다면 React는 DOM을 업데이트합니다.

## useCallback
컴포넌트안에 함수들은 컴포넌트가 리렌더링 될 때 마다 새로 만들어집니다. 함수를 선언하는 것 자체는 사실 메모리도, CPU도 리소스를 많이 차지 하는 작업은 아니기 때문에 함수를 새로 선언한다고 해서 그 자체 만으로 큰 부하가 생길 일은 없지만, 한 번 모든 함수를 필요할 때만 새로 만들고 재사용 하는것은 여전히 중요합니다.

그 이유는 나중에 컴포넌트에서 props가 바뀌지 않았으면 Virtual DOM에 새로 렌더링하는 것 조차도 하지 않고 컴포넌트의 결과물을 재사용하는 최적화 작업을 한건데, 이 작업을 하려면 함수를 재사용하는 것은 필수입니다.

⇒ useCallback은 상하위 컴포넌트 관계에서 상위 컴포넌트가 넘겨주는 props를 핸들링하는 역할을 하는데 하위 컴포넌트가 그 부분을 신경쓰지 않고 상위 컴포넌트의 렌더링 여부에 따라 자동으로 렌더링이 된다면, 다시 말해 useCallback은 홀로 사용한다면 그저 무용지물입니다.

```jsx
const onLogout = useCallback(() => {
  authService.logout();
},[authService]);
```
위의 useCallback은 deps에 있는 authService의 값이 변경될 경우에만 리렌더링됩니다.

## memo
useCallback 사용만으로는 하위 컴포넌트의 리렌더링을 막을 수 없습니다. 하위 컴포넌트가 참조 동일성에, 의존적인, 최적화된 Purecomponent!이어야만 비로소 불필요한 리렌더링을 막을 모든 것이 완성됩니다.

memo는 이전의 값을 memoizing하여 결과를 재사용 하는 것으로, memo로 컴포넌트를 감쌀 경우 props의 얕은 비교가 기본 원칙이며, 다른 비교를 원할 경우 두 번쨰 인자 값에 별도의 비교함수를 주면됩니다.

얕은 비교란 원시 값들은 메모리에 값이 저장되어서 상관이 없으나 객체, 배열, 함수와 같은 참조 타입들은 실제 내부 값까지 비교하지 않고 동일 참조인지만을 비교하는 것을 뜻합니다.

```jsx
const myFunction(){
  //
}
const areEqual(prevProps,nextProps){
  //
}
React.memo(myFunction,areEqual);
```
 같은 props로 렌더링이 자주 일어나는 컴포넌트 일 경우는 사용하는 것이 성능의 향상을 가져올 수 있습니다. 하지만 memo를 사용할 경우 memoizing된 값이 캐쉬로 저장이 되기 때문에 변경이 잦은 부모컴포넌트에 사용시 오히려 성능이 저하 될 수 있습니다. 
 
 참고:https://velog.io/@yejinh/useCallback%EA%B3%BC-React.Memo%EC%9D%84-%ED%86%B5%ED%95%9C-%EB%A0%8C%EB%8D%94%EB%A7%81-%EC%B5%9C%EC%A0%81%ED%99%94
 
 참고: https://react.vlpt.us/basic/19-React.memo.html
