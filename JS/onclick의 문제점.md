## **문제** 
이번에 vanilla javascript로 SPA만드는 프로젝트를 하다가 리액트에서 이벤트를 등록할 때처럼 <button onclick="clickBtn">이런 형식으로 함수를 등록했는데 분명히 함수를 정의했는데 not defined가 나왔다. 이유를 찾아보자.

**에러가 나온 이유는 clickBtn 함수가 전역으로 선언되어 있지 않기 때문이다.** 이것이 바로 onclick이벤트를 attribute-style로 등록하지 않는 이유 중에 하나이다. 등록된 함수는 오로지 전역으로만 실행된다.
```jsx
window.clickTestBtn = ()=>{
  console.log("test");
}
<button onclick="clickTestBtn()">Test</button> // click시 test
```

## **해결방법**
1. 전역으로 이벤트 등록하기 (위의 예처럼)
- 하지만 자바스크립트는 파일이 분리되어 있어도 하나의 전역 스코프를 공유하므로 추천하지 않음
2. 이벤트를 등록할 요소에 class,id를 사용하여 이벤트 등록하기

