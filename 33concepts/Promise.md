## Promise는 무엇일까?

Promise는 미래에 한 개의 값(해결한 값 또는 해결되지 않은 이유)을 생산 할 수 있는 객체이다. promise는 3개의 상태( fulfilled, rejected, pending ) 중에 하나가 될 수 있다. Promise 사용자는 fulfilled 값 또는 rejection의 이유를 다룰 수 있는 콜백을 붙일 수 있다.

Promise는 열렬하고 의미가 있다. 즉, promise가 호출되는 즉시 promise가 어떤 작업을 수행하기 시작한다.

## 어떻게 Promise가 작동할까 ?

Promise는 비동기 함수로부터 동기 함수를 리턴할 수 있는 객체이다. 이것은 3개의 상태중에 하나가 될 것이다.

- Fulfilled  : onFulFilled()가 호출될 것이다.
- Rejected : onRejected()가 호출될 것이다.
- Pending : 아직 fulfilled 혹은 rejected가 되지 않은 상태

Promise는 만약 이것이 pending상태가 되지 않는다면 안정적이다. 가끔 사람들은 not pending과 같은 의미로 resolved 그리고 settled를 사용한다.

한번 안정화되면, promise는 다시는 재안정화되지 않는다. resolve() 또는 reject()를 다시 호출하면, 영향을 끼치지 않을 것이다. 안정화된 promise에 불변성은 중요한 특징이다.

```jsx
const wait = time => new promise((resolve)=> setTimeout(resolve,time))

wait(3000).then(()=> console.log("Hello!"));
```

wait(3000)은 3000ms초를 기다릴 것이고 그리고 나서 log에 "Hello"를 출력할 것이다. 모든 promise는 resolved 또는 rejected 값을 컨트롤 할 수 있는then() 메소드로 정의한다.

ES6 promise 생성자(constructor)는 함수를 받는다. 그 함수는 두 개의 인자resolve()그리고 reject()를 받는다. 위의 예제를 보면, 우리는 오직 resolve만을 사용한다. 그리고나서 우리는 setTimeout()의 지연을 만들고 이것이 끝날 때 resolve()를 호출한다.

너는 선택적으로 resolve() 그리고 reject()를 할 수 있다. 내가 reject()를 했을 때, 나는 항상 Error 객체를 지나간다. 일반적으로 나는 일반적인 행복한 흐름 또는 일반적인 행복한 흐름을 막는 예외적인 흐름 두개의 상태를 원한다.  Error객체를 지나가는것은 예외적인 것을 만든다. 

## Promise 규칙에 중요성

Promise의 표준은 <a href="[https://promisesaplus.com/implementations](https://promisesaplus.com/implementations)"/>Promises/A+ specification</a>커뮤니티에 의해 정의되었다. JavaScript 표준 ECMAScript promise를 포함하여 표준을 따르는 많은 구현이 있습니다.

Promise는 정해진 특정한 규칙을 따라야만 한다.

- Promise 또는 "thenable"은 표준을 따르는 .then() 메소도를 제공하는 객체이다.
- pending promise는 fulfilled 그리고 rejected 상태로 이행될 수 있다.
- fulfilled 또는 rejected promise는 안정적이고 다른 상태로 이행되면 안된다.
- 한번 promise 가 안정되면, 이것은 값을 가지고 있어야 한다(undefined일 수도 있다). 그 값은 변화해서는 안된다.

## Promise Chanining

.then()은 항상 새로운 promise를 리턴하기 때문에, 이것은 오류를 처리하는 방법과 위치를 정확하게 제어하여 promise를 연결(chaining)할 수 있습니다. Promise는 일반적인 동기 코드의 try/catch를 모방할 수 있다.

동기 코드처럼, chaining은 직렬로 실행하는 시퀀스를 생성한다. 다시말해,, 밑의 예제처럼 할 수 있다.

```jsx
fetch(url)
  .then(process)
  .then(save)
  .catch(handleErrors)
;
```

각각의 함수들을 가정하면, fetch(), process()그리고 save()는 promise를 리턴한다. process()는 시작하기전에 fetch()를 기다릴 것이고 save()는 시작하기 전에 process()를 기다릴 것이다. handleError()는 이전의 promise가 거절될때만 실행될 것이다.

## Error Handling

promise는 성공과 error handler를 가지고 있는것을 기억하고 이것은 코드에서 공통적으로 볼 수 있다.

```jsx
save().then(
  handleSuccess,
  handleError
)
```

만약 handleSuccess()가 에러를 던진다면 무엇이 발생할까? Promise는 거절될 것이라는 것을 .then()으로부터 리턴한다. 그러나 거절(rejection)을 다루는 것이 존재하지 않는다. — 이것의 의미는 너의 app에 에러가 잡아먹힐 수도 있다는 것을 의미한다.

이러한 이유로, 일부 사람들은 위의 코드를 안티패턴으로 생각하고, 밑의 예제의 패턴을 추천한다.

```jsx
save()
  .then(handleSuccess)
  .catch(handleError)
;
```

차이점은 감지하기 힘들지만 중요하다. 첫 번째 예제에서, save() 실행에서 시작되는 에러는 잡힐 것이다. 하지만 handleSuccess()에서 시작한 에러는 잡아먹힐 것이다. 나는 모든 promise chanining에 마지막에는 .catch()로 끝나는 것을 추천한다.

## 결론

- Promise는 비동기 작업을 맞이할 미래의 3개의 상태 중에 하나를 생산할 수 있는 객체이다.(fulfilled, rejected, pending)
- Promsie chanining을 할 때는 마지막에 에러를 다루기 위해 .catch로 끝내야 한다.
번역글: <a href="https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261">https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261</a>
