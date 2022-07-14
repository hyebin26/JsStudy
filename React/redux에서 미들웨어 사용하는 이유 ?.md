# why do we need middlewre for async in Redux

### 계기

이 글을 작성하게 된 이유는 프로젝트에서 Redux-toolkit을 사용하던 중 thunk함수안에서 data를 받아오는 것이 안되서 다른 방법을 찾던 중 왜 API로 데이터를 불러올 때 컴포넌트에서 불러오는 것이 아니라 middleware를 사용해서 불러오는지에 대한 궁금증 해결

[https://stackoverflow.com/questions/34570758/why-do-we-need-middleware-for-async-flow-in-redux](https://stackoverflow.com/questions/34570758/why-do-we-need-middleware-for-async-flow-in-redux)를 참고하였습니다.

## 문제

redux문서를 보면 미들웨어를 사용하지 않으면, 리덕스 스토어는 오직 동기적인 데이터 흐름만 지지한다고 되어있다.</a> 그렇다면, container component에서 async API를 호출하고 dispatch 액션을 하는 것은 왜 되지 않는 것일까?

## Solution

위의 문제에서 잘못된 것은 없다. 이것은 단지 거대한 앱에서 불편하다. 왜냐하면 너는 같은 액션을 수행하는 컴포넌트를 가질 것이고, 너는 아마 debounce되기를 원하거나 자동 증가 ID와 같은 일부 지역 상태를 action creators에 가깝게 유지하고 싶을 수 있다. 그래서 유지 관리 관점에서 action creators를 별도의 기능으로 추출하는 것이 더 쉽다.

Redux Thunk 또는 Redux Promise같은 미들웨어는 thunks또는 Promise을 위한 "syntax sugar"이다. 그러나 이것을 너는 사용하지 않아도 된다.

큰 차이는 존재하지 않는다. 한 가지 middleware로 데이터를 받는 것에 장점은 components는 action creator가 async인지 신경쓰지 않는다. components는 어떻게 action creator가 구현되는지 모르고 너는 컴포넌트의 변경 없이 다른 async의 접근(Redux thunk, Redux Promise, Redux Saga)을 바꿀 수 있다. 반면에 이전의 접근방식을 사용하면, 너의 components는 특정 호출이 async이고 특정 규칙( 예:as async parameter )에 의해 전달되어야 한다는 것을 알것이다. 

또한 리덕스를 만든 사람인 Dan Abramov는 "미들웨어는 중앙 집권적인 접근이고, 이것은 컴포넌트 유지하는 것을 더 단순하고 일반화하고  한 장소에서 데이터의 흐름을 관리할 수 있게 합니다. 만약 큰 앱을 사용하면 middleware를 사용하는 것이 좋습니다."

