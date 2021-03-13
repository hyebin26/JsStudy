## Function Declaration vs Expression

함수는 자바스크립트에서 중요한 컨셉이며, 함수 만드는 컨셉을 아는 것은 상당히 중요하다.

자바스크립트는 다른 언어와 달리, 우리는 3가지 다른 방식으로 함수를 만들 수 있다.

### Function Declaration

```jsx
function [name](param1,param2, ...param3){
		// Function Body & Logic
}
```

함수 선언의 주요한 장점 중에 하나는 함수가 호이스팅(Hoisted) 된다는 것이다. 이것 때문에 우리는 함수를 선언하기 전에 실행할 수 있다.

이것은 일부 로직을 함수 본문으로 추상화하고 실제 실행이 나중에 될 때 유용하다.

```jsx
const num1 = 20;
const num2 = 10;

const result = add(num1,num2); ==> 30 (선언 전)

function add(param1, param2){
		return param1 + param2;
}
```

### Function Expression

다른 변수에 약간의 값을 할당하는 상태를 표현식(Expression)으로 여겨진다.

```jsx
const a = 100;
const b = "Hello World";

```

함수 표현식의 경우에, 함수는 특정한 이름없이 만들어지며, 변수에 할당된다. 그리고 함수 표현식은 호이스팅 되지 않는다.

```jsx
const [name] = function(param1, param2, ...param3){
		// Function Body & Logic
}
foo(1,2,3);
```

### 이름이 지어진 함수 표현식 → 두 개의 접근에 결합

표현식과 선언식을 이해하고, 두 개를 결합하면 무슨 일이 일어나는지 알아보자.

```jsx
const num1 = 10;
const num2 = 20;

const addVariable = function addFunction(param1, param2){
		return param1 + param2;
}
```

위에 예제에서,  함수가 더 이상 익명이 아니고, addFunction이라는 이름을 가진 것에 집중해보자. 또한 이것은 addVariable 이라는 변수에 할당됬다.

함수 키워드에 이름을 추가 한 것이(addFunction), 그 이름으로 실행된다는것을 의미하지는 않는다.

```jsx
const result = addFunction(num1, num2);
// ==> Error : Uncaught ReferenceError: addFunction is not defined
```

오히려, 변수 addVariable은 참조, 실행에 사용될 수 있다.

```jsx
const result = addVariable(num1,num2)
// => 30;
```
