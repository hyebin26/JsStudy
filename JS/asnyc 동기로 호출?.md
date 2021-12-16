## 참고
이번에 바닐라자바스크립트로 SPA를 만드는 프로젝트를 하면서 로그인 과정을 구현할 때 access_token을 검증하고 만약 만료되었을 경우, DB에 refresh_token의 존재 유뮤를 확인하고 존재할 경우에 토큰을 재발급 받는 순서로 진행하려고 했는데 서버에서 DB의 정보를 가져올 때 동기가 아닌 비동기로 가져와 return값이 undefined이 나와서 이러한 에러를 해결하기 위해 작성하게 되었습니다. 

참고: https://stackoverflow.com/questions/14220321/how-to-return-the-response-from-an-asynchronous-call

## 동기 vs 비동기
동기와 비동기는 프로그래밍 언어에서 중요한 개념이다.
- 동기: 요청 후 응답을 받아야 다음 동작을 실행하는 방식
- 비동기: 요청을 보낸 후 응답과 관계없이 다음 동작을 실행하는 방식
- 참고: https://poiemaweb.com/js-ajax

밑의 예제를 보면, he의 값이 10이 나온걸 볼 수 있다. 이유는 promise함수를 보면 setTimeout이라는 비동기 함수로 nums의 값을 변경하므로 return을 할 때는 setTimeout을 기다리지 않고 바로 값을 리턴하기 떄문이다. 
```jsx
function promise(callback) {
  let nums = 10;
  setTimeout(function () {
    nums = 20;
  }, 2000);
  return nums;
}
const he = promise(foo); 
console.log(he); // 10
```
이러한 방식을 해결해보면,
```jsx
function onComplete(a){ // When the code completes, do this
    alert(a);
}

function getFive(whenDone){
    var a;
    setTimeout(function(){
         a=5;
         onComplete(a);
    },10);
}
```
한 가지 해결법은 너의 비동기 함수가 계산이 끝났을 때 다른 함수를 호출하는 것이다. 예를 들어 위의 예제를 보면, setTimeout이 10ms초가 지날 경우 onComplete라는 함수를 호출한다. 이러한 방법을 <a href="https://en.wikipedia.org/wiki/Continuation-passing_style">CPS</a>라고 부르며, 일반적으로 이벤트(AJAX호출, 혹은 timeout 등)가 끝났을 때 어떻게 반응할 것인지 설정하는 것이다.
