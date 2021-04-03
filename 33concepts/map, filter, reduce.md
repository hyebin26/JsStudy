### 왜 map, filter, reduce를 알아야 하는가?

 함수 지향 프로그래밍에서 기초 중에 하나는 항목들의 사용과 항목 연산이다. 자바스크립트에는 초기 배열을 제공한 모든 함수가 map,filter,reduce를 가지고 있다. 

---

### Map

때때로 우리는 각각의 객체에 추가하고 변형하고 싶은 배열을 가지고 있다.다른 때에 우리는 소문자로 바꾸고 싶은 문자열의 배열을 가질 수도 있다. 정말 수도 없이 많은 상황이 있다. map은 너의 구원이며, 정말 쓰기 쉽다.

```jsx
const array= [1,2,3,4];
const arrayDouble= myArray.map((value,index)=>{
	return value * 2
})

console.log(myArray); // [1,2,3,4]
console.log(arrayDouble); // [2,4,6,8]
```

map 메소드는 배열 내의 모든 요소 각각에 대하여 주어진 함수를 호출한 결과를 모아 새로운 배열을 반환한다. 두 번째 인자인 index는 처리할 현재 요소의 인덱스이다.

```jsx
arr.map(callback(currentValue[, index[, array]])[, thisArg])
```

index는 처리할 현재 요소의 인덱스

array는 map을 호출한 배열

thisArg 는 callback을 실행할 떄 this로 사용되는 값

---

### filter

```jsx
const words = ['spray','limit','exuberant','destruction'];
const result = words.filter(word => word.length > 6);

console.log(result);// ['exuberant','destruction'];
```

filter 메소드는 주어진 함수의 테스트를 통화하는 모든 요소를 모아 새로운 배열로 반환한다.

```jsx
arr.filter(callback(element[, index[, array]])[, thisArg])
```

index는 처리할 요소의 인덱스

array는 filter를 호출한 배열

thisArg callback을 실행할 때 this로 사용되는 값

---

### reduce

```jsx
const array1 = [1, 2, 3, 4];
const reducer = (accumulator, currentValue) => accumulator + currentValue;

// 1 + 2 + 3 + 4
console.log(array1.reduce(reducer));
// expected output: 10

// 5 + 1 + 2 + 3 + 4
console.log(array1.reduce(reducer, 5));
// expected output: 15
```

 reduce 메소드는 배열의 각 요소에 대해 주어진 리듀서 함수를 실행하고 하나의 결과 값을 반환한다.

```jsx
arr.reduce(callback[, initialValue])
```

callbakc은 배열의 각 요소에 대해 실행할 함수로 4개의 매개변수를 가진다.

- accumulator : 콜백의 반환값을 누적한다. 콜백의 이전 반환값 또는 콜백의 첫 번째 호출이면서 initialValue를 제공한 경우 intialValue의 값이다.
- currentValue : 현재 처리할 요소
- currentIndex(Option) : 처리할 현재 요소의 인덱스 initialValue를 제공한 경우 0 아니면 1부터 시작한다.
- array(option) : reduce를 호출한 배열

참고 : <a href="https://developer.mozilla.org/ko/">https://developer.mozilla.org/ko/</a>
<a href="https://medium.com/@joomiguelcunha/learn-map-filter-and-reduce-in-javascript-ea59009593c4">https://medium.com/@joomiguelcunha/learn-map-filter-and-reduce-in-javascript-ea59009593c4</a>
