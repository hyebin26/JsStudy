## 참고
https://github.com/acdlite/react-fiber-architecture 의 번역글입니다.
# React Fiber Architecture
우선 React Fiber의 목적은 애니매이션, 레이아웃 및 제스처 같은 영역에 적합성을 높이는 것이다. Fiber의 대표적인 특징은 **Increment rendering: chunk단위로 렌더링 작업을 나누거나, 여러 프레임에 분산시키는 기능.**

다른 주요한 특징에는 새로운 업데이트가 들어올 때 작업을 일시 중지, 중단 또는 재사용하는 기능이 있다. 다른 유형의 업데이트에 우선 순위를 할당하는 기능; 그리고 새로운  동시성 우선순위(concurrency primitives).

### Prerequisites(전제조건)

이 문서를 읽기 전에 밑의 항목들에 대해 알고 있는 것을 강하게 추천한다.

- [React Components, Elements, and Instances](https://facebook.github.io/react/blog/2015/12/18/react-components-elements-and-instances.html) - "Component"는 자주 많은 뜻으로 오해가 되는 용어이다. 하지만 이러한 용어의 정확한 이해는 매우 중요하다.
- [Reconciliation](https://facebook.github.io/react/docs/reconciliation.html) - 리액트의 재조정 알고리즘에 수준 높은 설명
- [React Basic Theoretical Concepts](https://github.com/reactjs/react-basic) - 구현 부담이 없는 React의 개념적 모델에 대한 설명으로, . 이 중 일부는 처음 읽을 때 이해가 되지 않을 수 있지만 많지만 시간이 해결해줄 것이다.
- [React Design Principles](https://facebook.github.io/react/contributing/design-principles.html) - 특별하게 스케줄링 부분에 주의하자. 이것은 React Fiber의 이유를 잘 설명하고 있다.

## Review

만약 너가 준비된 상태가 아니라면, 전제 조건 부분을 체크해야 한다.

우리가 새로운 것에 깊게 들어가기전에, 몇개의 컨셉을 확인해보자.

### What is Reconciliation?

***reconciliation***

- 이 알고리즘은 하나의 트리와 또 다른 트리를 비교하고 이 부분이 변화가 필요한지 결정하는 것이다.

***update***

- React 앱을 렌더링하는 데 사용되는 데이터의 변경 사항이다. 일반적으로 `setState`의 결과로, 결국 다시 렌더링된다.

React API의 핵심 아이디어는 업데이트가 전체 앱을 다시 렌더링하는 것처럼 생각하는 것이다. 이를 통해 개발자는 앱을 특정 상태에서 다른 상태로 효율적으로 전환하는 방법에 대해 걱정하지 않고 선언적으로 추론할 수 있다.

현실적으로, 각각의 변화에 전체의 앱을 리렌더링하는 것은 오직 사소한 앱에서만 효과가 있다. 현실적인 앱 세계에서, 이것은 성능면에서 엄청나게 큰 비용을 담당한다. 리액트는 최고의 성능을 유지하면서 전체 앱이 다시 렌더링되는 모습을 만드는 최적화 기능을 가지고 있다. 이러한 최적화의 대부분은 **reconciliation**이라는 프로세스의 일부입니다.

Reconciliation은 많은 사람들이 “virtual DOM”으로 이해하고 있는 알고리즘의 부분이다. 너가 리액트 앱을 렌더했을 때, 그 앱을 설명하는 노드들의 하나의 트리는 생성되고, 메모리에 저장된다. 그런 다음 이 트리는 렌더링 환경으로 진행된다. 예를 들어 브라우저 앱에서, 이것은 DOM 작업의 세트로 환된다. 앱이 useState로 업데이트 되었을 때, 새로운 트리는 생성된다. 그리고 새로운 트리는 이 작업(update)이 렌더된 앱에 업데이트가 필요로 되어지는 지 측정하기 위해 이전의 트리와 비교된다.

비록 Fiber는 재조정의 기초적인 재작성이지만, React 문서에 설명된 고급 알고리즘은 대체로 동일하다. 키포인트는 다음과 같다.

- 다른 컴포넌트의 타입은 주로 다른 트리를 생성할 것이라고 추측된다. 리액트는 그들의 비교를 시도하지 않고, 이전의 트리를 완벽하게 교체할 것이다.
- list들의 비교는 keys를 이용해서 실행된다. Keys는 “안정적이고, 예층가능하고, 유니크”해야만 한다.

### Reconciliation vs rendering

DOM은 단지 리액트가 랜더할 수 있는 환경 중에 하나 이다. 다른 주요한 타겟들은 native IOS 그리고 안드로이드, React Native 이다. (이것은 왜 “virtual DOM”이 부정확한 단어인지에 대한 이유이다.)

많은 타겟들을 지원할 수 있는 이유는 재조정과 렌더링이 분리된 단계로 디자인되었기 때문이다. 재조정기(reconciler)는 트리가 변화하는 부분을 측정하는 작업을 한다. 그런 다음 렌더러는 해당 정보를 사용하여 렌더링된 앱을 실제로 업데이트한다.

이러한 분리는 React DOM과 React Native가 React core로써 제공되는 같은 재조정기를 공유하는 동안 그들의 렌더러를 사용할 수 있다는 것을 의미한다.

Fiber는 재조정기를 다시 구현한다. 이것은 주로 렌더링과 관련이 없지만, 렌더러는 새로운 아키텍쳐를 지원하기 위해(그리고 이용하기 위해) 변화하는 것을 필요로 할 것이다.

### Scheduling

***Scheduling***

- 언제 해당 작업이 실행되야 되는지 결정하는 프로세스

***work***

- 반드시 실행되야 하는 것의 계산. Work는 일반적으로 update에 결과이다.(setState)

React의 [Design Principles](https://facebook.github.io/react/contributing/design-principles.html#scheduling) 문서는 이 주제에 적합하고 나는 단지 인용만 할 것이다.

 

> 현재 구현에서 React는 트리를 재귀적으로 걷고 단일 틱 동안 업데이트된 전체 트리의 렌더 함수들을 호출한다. 그러나, 미래에 이것은 프레임 드롭을 피하기 위해, 약간의 업데이트들을 딜레이하는 것을 시작할 수도 있다.
> 

> 이것은 리액트 디자인에서 공통적인 테마이다. 일부 인기 있는 라이브러리는 새 데이터를 사용할 수 있을 때 계산이 수행되는 "Push" 접근 방식을 구현한다. 그러나 리액트는 필요할 때까지 딜레이 될 수 있는 계산 접근 방식인 “Pull”을 고수한다.
> 

> 리액트는 포괄적인 데이터 처리 라이브러리가 아니다. 리액트는 user interface를 위해 만들어졌다. 우리는 어떤 계산이 현재 관련이 있고 어떤 것이 그렇지 않은지 앱에서 알기 위해 고유하게 배치되었다고 생각한다.
> 

> 만약에 어떤 화면이 offscreen이라면, 우리는 이것에 연관된 로직을 딜레이 할 수 있다. 또한 데이터가 프레임 속도보다 더 빨리 도착한다면, 우리는 합치고 batch update(일괄 업데이트)를 할 수 있다. 우리는 프레임 드롭을 피하기 위해 중요하지 않은 백그라운드 작업(예를 들어, 네트워크에서 방금 로드한 새 콘텐츠 렌더링) 보다 유저 interaction으로 부터 오는 작업(예를 들어, 버튼 클릭으로 발생하는 애니메이션) 을 우선순위를 둘 것이다.
> 

키포인트는 아래의 항목이다.

- UI에서 모든 업데이트를 즉시 적용할 필요는 없다. 사실상, 그렇게 하면 프레임이 떨어지고 사용자 경험이 저하되는 낭비가 될 수 있다.
- 업데이트들에 다른 타입들은 다른 우선순위를 가지고 있다. 예를 들어 애니매이션 업데이트는 데이터 스토어의 업데이트 보다 더 빠르게 완료하는 것을 필요로 한다.
- Push-based 접근 방식을 사용하려면 앱(프로그래머)이 작업 일정을 결정하는 방법을 결정해야 합니다. Pull-based 접근은 프레임워크(React)가 똑똑해지고 이러한 결정을 하는 것을 할 수 있게 한다.

React는 현재 상당한 방법으로 스케줄링을 활용하지 않는다. 업데이트 결과 전체 하위 트리가 즉시 다시 렌더링된다. 스케줄링을 활용하기 위해 React의 핵심 알고리즘을 정밀 검사하는 것은 Fiber의 원동력이다.

이제 Fiber 구현에 대해 알아볼 준비가 되었다. 다음 섹션은 지금까지 논의한 것보다 더 기술적인 것이다.계속 진행하기 전에 이전 자료에 익숙해졌는지 확인해야한다.

## What is a Fiber

우리가 설정한 Fiber의 목표는 리액트가 스케줄링을 가능하게 하는 것이다. 구체적으로, 우리는 밑의 항목을 필요로 한다.

- 작업을 멈추고 나중에 다시 돌아오는 것
- 다른 타입의 작업에 우선순위를 할당하는 것
- 이전의 완료한 작업을 재사용하는 것
- 더 이상 필요로 하지 않다면 일을 중단시키는 것

이러한 것들을 하기 위해서, 먼저 작업을 단위로 나누는 방법이 필요하다. 어떤 의미에서 그것이 바로 **Fiber**이다. Fiber는 **작업 단위(unit of work)** 를 나타낸다.

더 나아가, 공통적으로 밑의 예제처럼 표현되는 [React components as functions of data](https://github.com/reactjs/react-basic#transformation)의 관점으로 되돌아가면, 

```jsx
v = f(d)
```

따라서 React 앱을 렌더링하는 것은 body에 다른 함수에 대한 호출 등을 포함하는 함수들을 호출하는 것과 유사하다. 이 유사점은 Fiber에 대해 생각할 때 유용하다.

컴퓨터가 일반적으로 프로그램의 실행을 추적하는 방법은 call stack을 사용하는 것이다. 함수가 실행될 때, 새로운 stack frame은 스택에 추가된다. 그 stack frame은 그 함수에 의해 실행되는 작업을 나타낸다.

UI에 대해서 다룰 때, 문제는 너무나 많은 작업이 한번에 실행된다는 것이다. 이것은 뚝뚝 끊어져 보이고, 프레임 드롭을 발생시킬 수 있다. 더욱, 그 작업의 약간은 최근의 업데이트에 의해 대체된다면, 필요하지 않을 수도 있다. components는 일반적인 함수보다 더 구체적인 관련이 있기 때문에 UI 구성 요소와 기능 간의 비교가 분류된다.

최신 브라우저(및 React Native)는 이 정확한 문제를 해결하는 데 도움이 되는 API를 구현했다. `requestIdleCallback` 은 가동되지 않고 있는 기간동안 우선순위가 낮은 함수가 호출되도록 예약하고, `requestAnimationFrame` 은 다음 애니매이션 프레임에 호출되는 우선 순위가 높은 함수를 예약한다. 이러한 API들을 사용하기 위해서, 문제는 이러한 API를 사용하려면 렌더링 작업을 incremental units(증분 단위)로 분할하는 방법이 필요하다는 것이다. 만약에 오직 call stack에 의존한다면, 이것은 그 스택이 빌때까지 계속해서 진행할 것이다.

우리가 UI의 렌더링을 위해 콜스택의 최적화를 변경할 수 있다면 좋지 않을까? 또한 우리가 마음대로 호출 스택을 중단하고 스택 프레임을 수동으로 조작할 수 있다면 좋지 않을까?  

React Fiber의 목적은 그것이다. Fiber는 React components를 위한 전문화된 스택의 재구현이고, 너는 **virtual stack frame**로서 하나의 fiber를 생각 할 수 있다.

스택의 재구현의 장점은 너는 메모리에 스택프레임을 계속해서 가질 수 있고, 너가 원하는대로(언제든지) 실행할 수 있다는 것이다. 이것은 우리가 스케줄링(scheduling)이라는 목표를 이루기 위해 중요하다.

스케줄링외에도, 스택 프레임을 수동으로 처리하면 동시성 및 오류 경계와 같은 기능의 가능성이 열린다. 향후 섹션에서 이러한 주제를 다룰 것이다.

## Structure of a fiber

구체적으로 말하면, Fiber는 component의 input과 output에 대한 정보를 포함하는 자바스크립트 객체이다.

Fiber는 stack frame에 해당하지만, 또한 이것은 컴포넌트 인스턴스에도 해당한다.

다음은 Fiber에 속하는 몇 가지 중요한 필드이다. (이 목록은 완전한 것이 아니다.)

### `type` 그리고 `key`

fiber의 type과 key는 React element와 동일한 목적으로 사용된다.(사실상 element로 부터 fiber가 만들어질 때, 이러한 두 가지 필드는 직접적으로 복사된다.)

Fiber의 type은 이것이 해당하는 컴포넌트를 설명한다. 복합 컴포넌트의 경우, type은 함수 또는 클레스 component 자체이다. 주인 컴포넌트의 경우(div, span, etc..) type은 string이다.

개념적으로, type은 stack frame에서 실행을 추적하는 함수이다.

type에 따라서, key는 재조정(reconciliation)동안 fiber가 재사용 될 수 있는지 결정하는 것에 사용된다.

### `child` and `sibling`

이 필드는 다른 fiber를 가리키며 fiber의 재귀 트리 구조를 설명한다.

이 child fiber는 컴포넌트의 render method에 의해 리턴된 value와 일치한다. 밑의 예제를 보면

```jsx
function Parent(){
	return <Child />
}
```

`Parent`의 child fiber는 `Child`와 일치한다.

Sibling 필드는 render가 다수의 children들과 리턴되는 경우를 설명한다.

```jsx
function Parent(){
	return [<Child1/>,<Child2/>]
}
```

Child Fiber는 head가 첫 번째 child인 하나의 연결된 리스트(singly-linked list)를 형성한다. 위의 예제에서는, `Parent` 의 child는 `Child1`이고, `Child1` 의 sibling은 `Child2`이다.

우리의 function의 유사점으로 돌아가서, 우리는 하나의 [tail-called function](https://en.wikipedia.org/wiki/Tail_call)로서 child fiber을 생각 할 수 있다.

### `return`

return fiber는 프로그램이 현재의 것을 처리한 후에 return되어야만 하는 것을 위한 fiber이다. 이것은 개념적으로 스택 프레임의 반환 주소(return address)와 동일하다. 또한 이것은 parent fiber로 생각될 수 있다.

만약에 fiber가 다수의 child fibers를 가지고 있다면, 각각의 child fiber가 반환한 fiber는 parent이다. 우리의 예제에서, `Child1`과 `Child2` 가 반환한 fiber는 `Parent` 이다.

### `pendingProps` 와 `memoizedProps`

개념적으로, props는 함수의 인자이다. Fiber의 `pendingProps` 는 이것의 실행 시작 부분과 세트이고, `memoizedProps` 는 끝부분과 세트이다.

들어오는 `pendingProps`가 `memoizedProps`와 같으면, Fiber의 이전 출력을 재사용할 수 있다는 신호를 보내 불필요한 작업을 방지합니다.

### `pendingWorkPriority`

숫자는 fiber에 의해 나타내는 작업에 우선순위를 가르킨다. [ReactPriorityLevel](https://github.com/facebook/react/blob/master/src/renderers/shared/fiber/ReactPriorityLevel.js) 모듈은 다른 우선 순위 수준과 해당 수준이 나타내는 것의 리스트를 작성한다.

0인 `NoWork`를 제외하고 숫자가 클수록 우선 순위가 낮다. 예를 들어, 다음 기능을 사용하여 Fiber의 우선 순위가 최소한 주어진 수준만큼 높은지 확인할 수 있다.

```jsx
function matchesePriority(fiber, priority){
	return fiber.pendingWorkPriority !== 0 &&
         fiber.pendingWorkPriority <= priority
}
```

이 기능은 설명용으로, 실제 React Fiber 코드베이스의 일부가 아니다.

### `alternate`

***flush***

Fiber를 플러시하는 것은 출력을 화면에 렌더링하는 것입니다.

***work-in-progress***

Fiber가 아직 완성되지 않음. 개념적으로, 스택 프레임이 아직 반환되지 않았다.

언제나, Components 인스턴스에는 이에 해당하는 최대 두 개의 섬유가 있다. 현재 플러시된 fiber 그리고 work-in-progress fiber.

현재 Fiber의 대체는 work-in-progress 작업이고, 진행 중인 작업의 대체는 current Fiber이다.

Fiber의 대체는 `cloneFiber` 로 불리는 함수를 이용하여 느리게 만들어진다. 항상 새 객체를 생성하는 대신 `cloneFiber`는 Fiber의 대체가 존재한다면 이를 재사용하여 할당을 최소화한다.

`alternate` 필드를 구현 세부사항으로 생각해야 하고, 이것은 코드베이스에서 자주 나타나므로 여기에서 논의하는 것이 좋다.

### `output`

***host component***

리액트 앱에 입사귀 노드. 그들은 렌더링 환경을 구체화한다. (브라우저 앱을 예로 들면, 그들은 div, span, etc 이다.) JSX에서, 그들은 소문자 태그 이름을 사용하는 것으로 나타낸다.

개념적으로, fiber의 output은 함수에 리턴된 값이다.

결국 모든 fiber는 output을 가지고 있으나 output은 **host components**에 의해 입사귀 노드에서 만들어진다. 그리고나서 output은 트리의 위로 이동한다.

Output은 렌더링 환경에 대한 변경 사항을 플러시할 수 있도록 렌더러에 최종적으로 제공되는 것이다. Output이 생성되고 업데이트되는 방법을 정의하는 것은 렌더러의 책임이다.

## Future sections

지금은 이것이 전부이지만 이 문서는 거의 완성되지 않았다. 향후 섹션에서는 업데이트 수명 주기 전반에 걸쳐 사용되는 알고리즘에 대해 설명한다. 다룰 주제는 다음과 같다.

- 어떻게 스케줄러가 다음 실행될 작업을 찾는 지
- 어떻게 우선순위가 추적되고 fiber tree에 의해 전파되는 지
- 어떻게 스케줄러가 작업이 중단될 때와 다시 시작될 때를 아는 지
- 어덯게 작업이 플러쉬되고 완료로 표시되는 지
- 어떻게 사이드 이펙트가 작업되는지
- Coroutine이 무엇이고 어떻게 이것이 컨텍스트와 레이아웃같은 기능을 구현으로 coroutine이 재사용될 수 있는 지

## 요약
- 재조정(Reconciliation)은 **다음 트리와 이전의 트리를 비교하고 변화가 필요한지 결정하는 것이다.**
- Fiber의 목표는 리액트가 **스케줄링**을 가능하게 하는 것이다. 이런 것들을 하기 위해 먼저 작업을 단위로 나누는 방법이 필요하다. Fiber는 작업 단위(unit of work)를 나타낸다.
- 일반적으로 프로그램의 실행을 추적하는 방법은 call stack을 이용하는 것이지만 너무나 많은 작업이 실행된다면 UI에 관점에서 보면 프레임 드롭을 유발할 수 있다. 그러므로 Fiber는 React component를 위한 전문화된 스택의 재구현이고 v**irtual stack frame로서 하나의 Fiber을 생각 할 수 있다.**
