## useState 올바르게 쓰기 
리액트에서 useState hook을 사용할 때 `setState((prevState)=> prevState + 1)` 이러한 방식으로 setState를 호출하다가 왜 이러한 방식으로 사용할까에 대해서 정리한 글입니다.

### 확인
```jsx
const [count,setCount] = useState(1);
const [test,setTest] = useState(1);

const onClickCount = ()=>{
  setCount(count+1);
}

const onClickTest = ()=>{
  setTest((prev) => prev+1);
}
```
위의 count버튼을 누르면 count값이 1이 증가하고, test버튼을 누르면 test값이 1이 증가한다. 위의 코드는 정상적으로 작동한다. 하지만 밑의 예제를 확인해보자.
```jsx
const [count, setCount] = useState(1);
const [test, setTest] = useState(1);

const clickTest = () => {
    setTest((prev) => prev + 1);
    setTest((prev) => prev + 1);
    setTest((prev) => prev + 1);
};
const clickCount = () => {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
};
```
이 코드는 버튼을 누르면 1이 아니라 3씩 증가하는 코드이다. 하지만 위의 코드에서 count버튼을 클릭하면 count의 값은 1씩 증가하고 test버튼을 누르면 test의 값은 3씩 증가한다. 이유는 useState가 비동기로 작동하기 때문이다. 비동기로 작동하기 때문에 clickCount함수에서 count의 값은 계속해서 1이기 때문에 `setCount(count+1)`를 하더라도 count값은 1+1인 것을 확인할 수 있다.

https://github.com/hyebin26/JsStudy/blob/main/React/UseState가async인이유.md
