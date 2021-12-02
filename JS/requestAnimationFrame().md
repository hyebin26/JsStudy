## window.requestAnimationFrame()

window.requestAnimationFrame는 브라우저에게 수행하기를 원하는 애니메이션 알리고 다음 리페인트가 진행되기 전에 해당 애니메이션을 업데이트하는 함수를 호출하게 한다. 이 메소드는 리페인트 이전에 실행할 콜백을 인자로 받는다.

> **Note:** 다음 리페인트에서 그 다음 프레임을 애니메이트하려면 콜백 루틴이 반드시 스스로 `requestAnimationFrame()`을 호출해야 한다.

콜백의 수는 보통 1초에 60회지만, 일반적으로 대부분의 브라우저에서는 W3C 권장사항에 따라 그 수가 디스플레이 주사율과 일치하게 된다.

콜백 메소에는 requestAnimationFrame이 대기된 콜백을 실행하는 시점을 나타내는 단일 인자가 전달된다. 따라서 모든 이전 콜백의 작업 부하를 계산하는 동안 시간이 자나갔음에도 불구하고 단일 프레임에서의 콜백은 각각 동일한 스탬프를 받는다. 이 타임스탬프는 밀리초 단위의 십진수지만, 최소 정밀도는 ims입니다. 

```jsx
(function () {
  let count = 1;
  let callback = function () {
    count++;
    if (count > 200) {
      console.log("End");
    } else {
      console.log(count);
      requestAnimationFrame(callback);
    }
  };
  requestAnimationFrame(callback);
})()
```
```jsx
for (let i = 1; i <= 200; i++) {
  console.log(i);
  if(i === 200) console.log("End);
}
```
위의 두 예제의 차이는 첫 번째 requestAnimationFrame은 최소 정밀도가 1ms이므로, 1ms시간 당 count의 값이 콘솔에 찍힌다. 하지만 for문으로 한 i의 값은 표시 가능한 주사율보다 빠르므로, 값은 순서대로 찍히지만 상당히 빠른 속도로 진행되서 200에 도달하게 된다.

표시 가능한 주사율로 시간을 조정해주기 떄문에 Canvas애니매이션을 할 떄 자주 사용된다.

## cancelAnimationFrame()
requestAnimationFrame을 취소하는 방법으로 cancelAnimationFrame을 사용한다. 마지 setTimeout의 clearTimeout처럼 동일하게 사용하고 동작한다. 변수로 requestid를 저장하고 매개변수로 넘기면 된다.
```jsx
let myReq = requestAnimationFrame(callback);
cancelAnimationFrame(myReq);
```

## 예제 
```jsx
const debounceFrame = (callback) => {
  let nextFrameCallback = 0;
  return () => {
    console.log("debounce");
    cancelAnimationFrame(nextFrameCallback);
    nextFrameCallback = requestAnimationFrame(callback);
  };
};
MyReact = {
_render: debounceFrame(() => {
    const { root, rootComponent } = MyReact.options;
    if (!root || !rootComponent) return;
    root.innerHTML = rootComponent();
    MyReact.options.currentStateKey = 0;
  }),
  ...
  ...
}
```
예제는 vanilla javasciprt로 useState를 구현할 때 작성한 코드로, useState의 값이 바뀌면 _render의 값이 호출된다. 하지만 만약에 하나의 이벤트안에서 백개의 state가 변경된다면 백 번의 re-rendering이 발생한다.

이를 방지하고자 웹에서 발생하는 이벤트 제어 기술인 디바운싱을 활용하면, 이벤트안에서 setState함수를 호출하면 state의 값을 변경하고 렌더링이 된다.

하지만 debounce 함수안에 requestAnimationFrame으로 1ms 시간안에 debounceFrame이라는 함수가 또 호출되면 cancelAnimationFrame으로 이전의 콜백을 취소하고 새로운 callback이 실행된다.

참고: https://developer.mozilla.org/ko/docs/Web/API/Window/requestAnimationFrame
참고: <a href="https://webisfree.com/2020-03-19/[%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8]-requestanimationframe()%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-%EB%B0%8F-%EC%98%88%EC%A0%9C">webisfree.com requestAnimationFrame</a>
