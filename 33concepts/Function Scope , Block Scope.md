### var

ES6 나오기전에, 자바스크립트에서 변수를 선언하는 방법은 var밖에 없었다.

var는 함수 스코프(function scope)이다. 이 스코프 타입은 블록 스코프(block scpoe)와는 조금 다르게 행동한다.

var로 변수 선언을 하는 것은 함수 스코프가 될것이고, 이것의 의미는 선언한 함수 안에 변수가 존재한다는 것이다.

```jsx
function myFunc(){
		var name = "Luke";
		console.log(name) // Luke
}

myFunc()

console.log(name); // name is not defined
```

 위에 예제에서 본 것처럼, 함수안에 선언된 var 변수는 바깥 함수에 도달하지 못한다.

다른 블록 타입에서는, 스코프로 여겨지지 않는다.

```jsx
if(true){
	var name = "Luke"
}

console.log(name); // Luke
```

변수 name은 선언된 밖에서도 사용가능하다. 이것의 이유는 같은 스코프로 인식되기 때문이다.

그러나 ES6에서, 변수 선언하는 두 가지 방법이 소개되었다.

### let and const 블록스코프

ES6에서, let 그리고 const 는 블록 스코프로 변수를 선언하는 방법으로 소개되었다. 

블록스코프에서, 어떤 블록은 스코프가 될 것이다. 이것은 보다 일관적인 행동을 제공한다.

이것은 함수가 여전히 var와 함께 유요한 스코프라는 것을 의미한다.

```jsx
function myFunc(){
	let name = "Luke"
	console.log(name); // Luke
}

meyFunc();

console.log(name); // name is not defined
```

그러나 밑의 예제처럼 다른 스코프도 스코프로 인지된다.

```jsx
if(true){
	let name = "Luke"
}

console.log(name); // name is not defined
```

### When function scope gets confusing

우리는 블록 스코프와 함수 스코프의 차이를 명백하게 할 수 있다. 하지만 스코프는 혼란을 줄 수도 있다.

함수 밖에 있는 변수와 함수 안에 잇는 변수가 같은 이름을 가지는 것은 완벽하게 괜찮다.

 

```jsx
var name = "Luke";

const fun = () =>{
	var name = "Phil";
	console.log(name); // Phil
}

func();

console.log(name); // Luke
```

위의 예제에서, 변수 name은 함수가 실행을 한 후에 첫 번째 선언한 "Luke"를 계속해서 가지고 있다.

그러나 문제는, 함수 스코프는 다른 타입의 블록들을 처리하지 못하고 오직 함수 스코프만 처리하기 때문에, 우리는 다른 블록들에는 다른 결과를 얻을 수 있다.

```jsx
var name = "Luke";

if(true){
		var name = "Phil";
		console.log(name); // "Phil"
}

console.log(name); // Phil
```

이번 경우에서, "Phil"은 두 곳에서 출력된다. 이것의 이유는 두변수가 같은 소크프에 있어서 변수에 할당이 된 것이다.

우리는 이것이 많은 복잡성을 가져올 것이라고 예측 할 수 있다.

### Bringing consistency with blocked scope

블록 스코프인 let을 보면, 우리는 모든 블록에 지속성을 유지해준다.

```jsx
let name = "Luke";

const func = () =>{
		let name = "Phil";
		console.log(name); // Phil
}

func();

console.log(name); // Luke
```

```jsx
let name = "Luke";

if(true) {
		let name = "Phil";
		conosole.log(name); // Phil
}

console.log(name); // Luke
```

### What about Loop?

다른 결과를 얻을 수 있는 또 다른 예가 있다.

만약 우리가 반복문에서 출력되는 숫자로 배열을 push하는 반복문을 만든다면 어떻게 될까?

```jsx
for(var i =0; i < 3; i++){
		printToBeExecuted.push(()=>console.log(i))
}

printToBeExecuted.forEach(f=>f());
// 3,3,3
```

왜 0,1,2가 아닌 3, 3, 3이 출력된 것일까?

이유는 단순하다. 반복문이 var를 쓸 때는 스코프가 아니기 때문이다.그래서 각각의 변수인 i가 증가하는 것 대신에, 이것운 결국 모든 함수에 마지막 값만 출력된 것이다.

만약에  let을 사용한다면, 우리가 예측한 정확한 값을 출력 할 수 있을 것이다.

```jsx
var printsToBeExecuted = [];

for(let i = 0; i < 3; i++){
	printsToBeExecuted.push(() => console.log(i));
}

printsToBeExecuted.forEach(f => f());
// 0, 1, 2
```

참고 : <a href="https://www.deadcoderising.com/2017-04-11-es6-var-let-and-const-the-battle-between-function-scope-and-block-scope/">https://www.deadcoderising.com/2017-04-11-es6-var-let-and-const-the-battle-between-function-scope-and-block-scope/</a>
