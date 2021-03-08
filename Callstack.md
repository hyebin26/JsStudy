### CallStack

- 함수의 호출을 기록하는 데이터 구조이다. 함수를 호출하면 콜스택에 함수를 밀어 넣고(push), 함수가 리턴되면 스택에서 삭제된다.
- 콜스택은 사이즈에 제한이 있으며, 16.000 프레임을 초과할 시 Max stack 에러를 던진다.
- Stack trace 에러는 기본적으로 현재의 콜스택 상태를 가르키며, 기본적으로 현재의 콜스택에서 스택에 잘못된 위치를 가르킨다.

### Heap

- Heap은 구조화 되어 있지 않은 곳으로, 객채와 변수들의 메모리 할당이 이루어지는 곳이다.

### Queue

- 자바스크립트 런타임은 실행에 연관된 Callback 함수들이 명단인 message queue를 포함한다. 스택이 충분한 공간을 가지고 있을 때, 스택은 큐에서 호출된 Callback함수와 연관된 메시지를 꺼내고 처리한다. message처리는 스택이 다시 비었을 때 끝난다.
- 일반적으로, Callback 기능이 주어진 경우, 메세지들은 비동기 이벤트에 대한 응답으로 대기열에 포함된다. 예를 들어, 사용자가 버튼을 클릭했을 때, 콜백함수가 제공되어 있지 않다면 어떠한 message도 큐에 들어가지 않는다.

### Event Loop

기본적으로, 함수가 성능을 느리게, 빠르게 만드는지 평가할 때 console.log 는 빠를 것이다. 반면에 많은 양의 roop, while 문을 처리하는 것은 느릴 것이고, 스택을 차지하고 막을 것이다. 이것을 Blocking script 라고 하며, 한번쯤은 들어봤을 것이다.

만약 용량이 큰 네트워크 요청이 동기적으로 처리된다면, 요청을 받을 때까지 스택을 차지할 것이고, 그렇다면 처리하는 동안 아무것도 렌더링 되지 않을 것이다. 멀티 쓰레드 언어인 Ruby는 이러한 것을 처리 할 수 있지만 , 싱글스레드인 자바스크립트는 이것을 처리 할 수 없을 것이다.

이것을 처리하는 가장 쉬운 방법은 비동기 함수 콜백(asynchronous callback)을 사용하는 것이다. 비동기 콜백을 사용한다는 것은 코드의 일정부분을 실행하고 이것을 나중에 실행할 콜백함수에 주는 것이다. 모든 비동기 콜백은 즉시 실행될수 없으며, 나중에 실행될 것이다. 그래서 동기 함수인 console.log처럼 스택에 즉시 들어갈 수 없다. 그럼 비동기 콜백은 어디로 가고 어떻게 다룰까?

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b13e30c4-048a-4152-a5eb-a851073cc6e0/__2021-03-08_170730.png](https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec)

응답에서의 호출자(Caller)의 분리는 자바스크립트에게 비동기 실행을 완료하고 Callback이 호출될 떄 까지 기다리는 동안 다른 일을 처리할 수 있게 해준다. 그래서 가장 나중에 호출된 console.log 인 동기 함수가 가장 먼저 출력된 것이다. console.log가 출력되고 브라우저 API들이 작동한다. 

브라우저 API는 DOM이벤트들, HTTP요청, setTimeout 등등 비동기 함수들을 다루기 위해 C++로 구성된 스레드들로 만들어졌다. 브라우저 웹 API는 스스로 스택에 들어갈 수 없으며, 만약 그런다면 코드 중간에 무분별하게 실행될 것이다.  웹 API에 어떤 것은 콜백을 큐안에 밀어 넣을 것이다. 웹API는 콜백이 실행이 완료될 때 큐에 콜백을 push한다. 

Event Loop는 큐안에 있는 콜백들 실행 그리고 스택이 비었을 때, 스택안에 콜백을 push하는 것에 책임이 있다. Event Loop의 기초적인 일은 스택과 일 큐(task queue)를 보고 스택이 비어있을 때 큐에 첫번째 것을 push하는 것이다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d0d09db1-81e8-4ebf-9000-15e1762d35a9/JavascriptEventLoop1.png](https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec)

웹브라우저에서, 메시지들은 이벤트가 발생했을 때 추가되고 이벤트 리스너가 붙어있다. 만약에 리스너가 없다면, 이벤트는 사라진다. 그래서 클릭 이벤트 핸들러를 가지고 있는 요소(Element)의 클릭은 다른 이벤트처럼 메시지에 추가된다. 이 콜백 함수의 호출은 첫번째 프레임의 역할을 한다. 그리고 자바스크립트가 싱글 스레드 이기 때문에, 추가의 메시지 폴링 그리고 처리는 스택에 모든 호출이 리턴되는 것을 기다리는 동안 중지된다. 후속 동기 함수 호출들은 새로운 호출 프레임 스택에 추가된다.
