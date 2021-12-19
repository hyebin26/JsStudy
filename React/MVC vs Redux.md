## MVC패턴

MVC패턴이란, 개발을 할 때 3가지 형태로 역할을 나누어 개발하는 방법론

- Model: 정보들의 가공을 책임지는 컴포넌트, Data가 주로 들어가 있음.
- View: 사용자들이 보여지는 부분, 즉, 유저 인터페이스를 담당
- Contorller: 사용자의 입력을 받아 Model을 조작, View를 업데이트함.

그러나 MVC패턴이 대규모 어플리케이션 개발에는 관리하기 어렵다고 느끼기 시작했다. 프로젝트가 단순하면 간단하게 표현할 수 있다. 하지만 프로젝트 규모가 커지면 데이터 자료의 양과 화면이 많아지면서 Model과 View가 급격히 늘어나고 그에 따라 각각의 모듈들이 어떤 식으로 연결되어있는지 파악하기 어려워지기 마련이다.

## Flux

페이스북 프론트엔드 팀에서 Flux라는 새로운 패턴에 대해서 발표했따. Flux패턴은 Action,dispatch, store, view로 이루어져 있다. **MVC패턴은 어떤 action이 발생하면, 데이터 상태가 변경되고 그에 따라 디스플레이를 변경하는데 상태가 변경되었다는 정보를 View와 Model이 서로 양뱡향으로 주고 받는 형태이다.**

![군함조](https://user-images.githubusercontent.com/67263146/145005805-3ca6c948-6669-4025-9e7a-fa95f2866bd0.png)

그러나 Flux패턴은 action이 발생하면 dispatcher에 의해 store에 변경된 사항이 저장되고 그 저장된 사항에 의해 view가 변경되는 단뱡향 패턴을 보이고 있다. 

- dispatch: 스토어의 내장 함수로, 액션을 발생시키고 스토어는 리듀서 함수를 실행시킨다.
- 이러한 패턴의 가장 큰 장점은 개발 흐름이 단방향으로 흐르기 떄문에, 훨씬 파악하기 쉽고 코드의 흐름이 예측(Predictable)하다는 것이다.

이러한 Flux 패턴의 혁신적인 탄생과 함께 2015년 7월 React Europe 컨퍼런스에서 Dan Abramov라는 사람에 의해 Flux 패턴과 Reducer개념을 도입한 Redux가 세상에 태어났다.

Flux패턴은 위 그림에서 볼 수 있듯이 어떤 Action이 발생하면 dispatcher에 전달되고 Store에 저장되며 그것에 의해 View가 변경되는 일련의 과정이 끊임없이 발생한다. 이런 방식이 마치 Reduce함수와 비슷하다고 생각하여 Reducer함수의 개념을 도입한 것이 Redux이다.

## Redux

리덕스는 일종의 상태 관리 라이브러리이다. 컴포넌트에는 state라는 상태가 존재하며, 상위 컴포넌트에서 하위 컴포넌트로 props로 전달하여 관리된다. 그런데 리덕스를 사용한다면 컴포넌트끼리 상태를 공유할 때 여러 컴포넌트를 거치지 않고 손쉽게 전달할 수 있게 된다.

![타조](https://user-images.githubusercontent.com/67263146/145005811-14701803-64a5-4743-9eec-8ffa71ec3318.png)

Redux는 Flux와 달리 스토어가 하나이고, 리듀서가 존재한다. 

-리듀서(Reducer)는 Flux에는 없는, Redux에서만 찾을 수 있는 용어로, 액션은 어떤 일이 일어났는지는 알려주지만 애플리케이션의 상태를 어떻게 바꾸어야 할지는 알려주지 않는데, Redux 프레임워크에서는 리듀서가 이 역할을 담당한다.
- 리듀서는 두 가지 인자를 받는데, 첫 번째는 이전 상태 정보가 들어오고, 두번째 인자는 아까 위에서 발생한 액션 객체가 들어온다. 리듀서 함슈가 상태를 업데이트하면 그에 따라서 render가 다시 일어나 화면이 바뀌게 된다.

```jsx
export default function counter(state = initialState, action) {
  switch (action.type) {
    case INCREASE:
      return {
        ...state,
        number: state.number + state.diff,
      };
    case DECREASE:
      return {
        ...state,
        number: state.number - state.diff,
      };
    default:
      return state;
  }
}
```

참고: [https://im-developer.tistory.com/158](https://im-developer.tistory.com/158)
