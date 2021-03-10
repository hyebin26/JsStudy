## Type Coercion

### 왜 이렇게 되는 것일까?

- Example 1

```jsx
const a = [ 1, 2, 3 ];
const b = [ 1, 2, 3 ];

console.log(a + b); // -> 1,2,31,2,3 
```

- Example 2

```jsx
const nope = Array(5).join("nope" - 1) + " Batman!";
console.log(nope) // NaNNaNNaNNaN Batman!
```

- Example 3

```jsx
const x = [];
const y = [];

console.log(x + y); // [object Object]
```

### 왜 잘못되는 것일까?

이유는 단순하게 역사이다. 자바스크립트가 만들어진 이후 바뀌지 않은 모든 것들은 한가지 이유를 가지고 있다.  웹을 망가뜨리지 않는다. 이것은 typeof null === 'object' 같은 이상한 것들이 존재하는 이유이다.

### 어떻게 발생할까?

이것은 상당히 단순하다. 기대되지 않은 연산자와 값의 연산을 요청했을 때 (예를들면 객체에 숫자를 추가하는 것)  또는 서로 연산이 되지 않는 값을 요청했을 때(숫자에 문자열 추가하기), 자바스크립트는 이것을 효과적으로 만들기 위해 타입의 변화를 시도한다.

그러나 이것은 규칙이 존재한다. 만약 컴퓨터가 실수(에러)를 만든다면, 적어도 그것은 일정하다는 것이다.

자바스크립트는 단순하게 Object혹은 Primitive's 프로토타입에서 적합한 메소드를 낚아챈다. 두가지는 toString() 과 valueOf() 이다. 배열에 concat 을 하고 , 문자열에 "+" 연산자를 사용했을떄, Array.prototype.toSting 메소드는 배열을 문자열로 변환한다.

배열은 객체이다. 배열 프로토타입은 Syntax sugar 이다. 객체를 활용함으로써 자바스크립트에서 스스로만의 배열 타입을 만들 수 있따. 배열에 property를 할당함으로써 명백하다. 

```jsx
let arr = [];

arr[0] = "Zero";
arr[-1] = "Minus One";
arr['prop'] = true;
arr.says = "Hello";

console.log(arr); // [ "Zero", -1:"Minus one", prop:true,says:"Hello"]

```

이 예제처럼 배열은 자바스크립트 객체이다. 근데 왜 다르게 바뀔까? 왜냐하면 다른 내장함수를 가지고 있기 때문이다.(toString() 그리고 valueOf()  메소드)

```jsx
Object.prototype.toString =function(){
	return JSON.stringify(this);
}

const obj = {"foo" : "bar" };
const obj2 = {"baz" : "foobar"};

console.log( obj + obj2 ); // {"foo":"bar"}{"baz":"foobar"}

```

그리고 위에 예제처럼 [object object][object object] 가 나오는 값을 toSting을 치환하므로써 기본값을 변경할 수 있다.

그럼 언제 toString()이 호출되고 valueOf()가 호출 될까?

가능한 언제든지, toString이 항상 처음으로 호출된다. 끝나고, valueOf가 호출된다. String() 그리고 Number() 생성자들은 또한 메소드를 따를 것이다.

### Boolean

Boolean type은 ToBoolean을 호출한다. 자바스크립트에서, 우리는 이것을 설정할 수 없다. ToBoolean은 단순하며, "true" 와 "false" 에 의존한다.

- 객체, 배열, Symbol 은 항상 true이다.
- null , undefined 는 항상 false이다.
- 0 , -0, +0 은 false이며, 다른 숫자들은 true이다.
- 빈 문자열은 false이며, 다른 문자열은 true이다.

참고 : <a href="https://thedevs.network/blog/type-coercion-in-javascript-and-why-everyone-gets-it-wrong">https://thedevs.network/blog/type-coercion-in-javascript-and-why-everyone-gets-it-wrong</a>
