  # React Query Basic

### 요약

- React Query의 경우 server state를 관리해주는 라이브러리인데 server state의 경우 ownership이 서버에 있고, API구조가 계속되서 반복되므로 React Query를 사용하는 것이 좋다.
- 장점은 서버 상태를 관리하기 쉬워지고 메모리 성능을 높여주고, 코드를 간단하게 만들어준다.

### React Query

React Query는 종종 React에 대한 누락된 데이터 가져오기 라이브러리로 설명됩니다. 하지만 훨씬 테크니컬한 용어로, 이것은 React 앱에서 **fetching, caching, sychronizing and updating server state**하는 작업을 간단하게 만듭니다.

### 문제점

우선 상태란 ⇒ **주어진 시간**에 대해 시스템을 나타내는 것으로 언제든 변경될 수 있다.

즉 문자열, 배열, 객체 등의 형태로 응용 프로그램에 저장된 데이터

store는 전역 상태가 저장되고 관리되는 공간인데 **상태관리보단 API통신 코드?**

1. 스토어에 다 관리하는 것이 맞나?
- 기존의 상태 관리 라이브러리들은 API통신 관련 코드가 모두 Store에 저장되는 구조
- 또 반복되는 isFetching, isError 등 API관련 상태
- 또또 반복되는 비슷한 구조의 API통신 코드
1. 서버에서 받아야하는 상태들의 특성
- 클라이언트에서 제어하거나 소유되지 않은 원격의 공간에서 관리되고 유지됨 ⇒ **클라이언트에서 부여하지 않으므로 ownership이 다름**
- 예를 들어 주문이라고 생각하면 클라이언트에서 관리하는 것이 아니라 서버에서 관리하는 것임.
- fething 이나 updating에 비동기 API가 필요함.
- 다른 사람들과 공유되는 것으로 사용자가 모르는 사이에 변경될 수 있음 ⇒ 사용자가 주문을 기다리고 있는데 사장님이 완료를 한다면? 변경가능성이높음
- 신경쓰지 않는다면 잠재적으로 “옛날” 데이터가 될 가능성을 지님 ⇒ 난 아직 접수대기중인데 사장님이 서버에서 접수를 완료를 한다면? 신경쓰지 않는다면 옛날 데이터가 될 수 있음

### 장점

React Query는 서버 상태를 관리하기 위한 최고의 라이브러리 중 하나다. **zero-config로 즉시 사용가능하고** 놀라울 정도로 잘 작동하며 애플리케이션이 성장함에 따라 원하는 대로 사용자 정의할 수 있다.

React Query를 사용하면 서버 상태의 까다로운 문제와 장애물을 극복하고 앱데이터가 사용자 제어하는 것을 시작하기전에 앱 데이터를 제어할 수 있다.

좀 더 기술적인 설명에서는 React Query는 다음과 같은 작업을 수행할 가능성이 높다.

- 애플리케이션에서 복잡하고 오해할 수 있는 여러 줄의 코드를 제거하고 몇 줄의 React Query로직으로 대체할 수 있도록 도와준다.
- 새로운 서버 상태 데이터 소스 연결에 대한 걱정 없이 애플리케이션을 유지 관리하기 쉽고 새로운 기능을 더 쉽게 구출할 수 있다.
- 앱을 그 어느 때보다 빠르고 반응성이 좋게 만들어 최종 사용자에게 직접적인 영향을 미칩니다.
- 잠재적으로 대역폭을 절약하고 메모리 성능을 높이는 데 도움이 됩니다.

참고:[https://www.youtube.com/watch?v=MArE6Hy371c&t=1034s](https://www.youtube.com/watch?v=MArE6Hy371c&t=1034s)

[https://react-query.tanstack.com/overview](https://react-query.tanstack.com/overview)
