# React Hooks

Hook은 함수 컴포넌트에서 React state와 컴포넌트의 생명주기(Life cycle) 기능을 연동(hook into)할 수 있게 해주는 함수이다.

## 동기

1. 컴포넌트 사이에서 상태 로직을 재사용하기 어렵다.
2. 복잡한 컴포넌트들은 이해하기 어렵다.
    
    - **생명주기 메서드를 기반으로 쪼개는 것보다는 Hook을 통해 서로 비슷한 것을 하는 작은 함수의 묶음으로 컴포넌트를 나누는 방법을 사용할 수 있다.**
    
    - 이러한 로직의 추적을 쉽게 할 수 있도록 리듀서를 활용해 컴포넌트의 지역 상태 값을 관리하도록 할 수 있다.
    
3. Class는 사람과 기계를 혼동시킵니다.

## Effect Hook

React는 컴포넌트 안에서 데이터를 가져오거나 구독하고, DOM을 직접 조작하는 작업을 이전에도 종종 해봤을 것이다. 우리는 이런 모든 동작을 "side effects"(또는 "effect")라고 한다. 왜냐하면 이것은 다른 컴포넌트에 영향을 줄 수도 있고, 렌더링 과정에는 구현할 수 없는 작업이기 때문이다.

Effect Hook, 즉 useEffect는 함수 컴포넌트 내에서 이런 side effects를 수행할 수 있게 해준다. React class의 componentDidMount나 componentDidUpdate, componenetWillUmount와 같은 목적으로 제공되지만, 하나의 API로 통합된 것이다.

parameter로 넣은 함수는 또 다른 함수를 return할 수 있는데, 이를 Clean-up함수라고 부른다.

⇒ Component의 unmount이전 / update직전에 작업을 수행하고 싶다면 Clean-Up함수를 반환해 주어야 한다.

class에 익숙하다면 왜 effect정리(clean-up)가 마운트 해제되는 떄에 한 번만이 아니라 모든 리렌더링 시에 실행되는 지가 궁금할 것이다. 이러한 디자인이 버그가 적은 컴포넌트를 만드는 데에 어떻게 도움이 되는지 다음의 예시를 통해 알아보자.

```jsx
function FriendStatus(props) {
  // ...
  useEffect(() => {
    // ...
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```

 Clean- up 함수 작동순서를 보면, re-render → 이전 effect clean-up → effect순으로 진행되는 것을 볼 수 있다.

```jsx
// { friend: { id: 100 } } state을 사용하여 마운트합니다.
ChatAPI.subscribeToFriendStatus(100, handleStatusChange);     // 첫번째 effect가 작동합니다.

// { friend: { id: 200 } } state로 업데이트합니다.
ChatAPI.unsubscribeFromFriendStatus(100, handleStatusChange); // 이전의 effect를 정리(clean-up)합니다.
ChatAPI.subscribeToFriendStatus(200, handleStatusChange);     // 다음 effect가 작동합니다.

// { friend: { id: 300 } } state로 업데이트합니다.
ChatAPI.unsubscribeFromFriendStatus(200, handleStatusChange); // 이전의 effect를 정리(clean-up)합니다.
ChatAPI.subscribeToFriendStatus(300, handleStatusChange);     // 다음 effect가 작동합니다.

// 마운트를 해제합니다.
ChatAPI.unsubscribeFromFriendStatus(300, handleStatusChange); // 마지막 effect를 정리(clean-up)합니다.
```

버그가 적다 ⇒ 컴포넌트가 화면에 표시되어 있는 동안 prop이 변한다면?

```jsx
componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentDidUpdate(prevProps) {
    // 이전 friend.id에서 구독을 해지합니다.
    ChatAPI.unsubscribeFromFriendStatus(
      prevProps.friend.id,
      this.handleStatusChange
    );
    // 다음 friend.id를 구독합니다.
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
```

이러한 방식으로 동작하는 것이 일관성을 유지해주며, 클래스 컴포넌트에서는 흔히 업데이트 로직을 뺴먹으면서 발생할 수 있는 버그를 예방합니다.

만약 Effect를 건너뛰고 싶다면, useEffect의 선택적 인수인 두 번째 인수로 배열을 넘기면 된다.

```jsx
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // count가 바뀔 때만 effect를 재실행합니다.
```

이것이 의미하는 것은 count의 값이 변화가 없다면, React는 배열안에 count값을 그 다음 렌더링 때의 값과 비교하고 같을 경우 effect를 건너뛰게 된다.

effect가 너무 자주 변경되는  state의 경우 함수 컴포넌트의 업데이트 폼을 이용해야 한다.

[https://dodokim.medium.com/setstate-를-함수형으로-사용하기-763402cbc3e5](https://dodokim.medium.com/setstate-%EB%A5%BC-%ED%95%A8%EC%88%98%ED%98%95%EC%9C%BC%EB%A1%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-763402cbc3e5)
