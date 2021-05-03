## 자바스크립트 Generators는 무엇일까?

Generator는 반복문을 제어할 때 사용할 수 있는 함수이다. 그들은 언제든지 일시중지 했다가 나중에 다시 시작할 수 있다.

만약 이해되지 않는다면, Generator가 무엇인지 설명하는 그리고 Generator와 Loop같은 반복문과의 차이를 비교하는 예제를 보자.

이것은 즉시 값을 리턴하는 for-Loop이다. 이 코드가 무엇을 할까? 단순하게 0부터 5까지 숫자를 반복한다.

```jsx
for (let i =0; i < 5; i +=1){
  console.log(i)
}
// 0, 1, 2, 3, 4 
```

그럼 Generator 함수의 예제를 보자.

```jsx
function * generatorForLoop(num){
  for(let i = 0; i <num; i += 1){
     yield console.log(i);
  }
}

const genForLoop = generatorForLoop(5);

genForLoop.next(); // console.log(0)
genForLoop.next(); // console.log(1)
genForLoop.next(); // console.log(2)
genForLoop.next(); // console.log(3)
genForLoop.next(); // console.log(4)

```

이것은 무엇일까? 사실상, 이것은 우리의 for-loop문을 약간의 변화와 함께 감싼 것이다. 그러나 가장 중요한 변화는 이것은 바로 시작되지 않는다는 것이다. 그리고 이것은 Generator에 가장 중요한 특징이다. 우리는 한번에 값을 얻는 것이 아니라 필요로 할 때만 다음 값을 얻을 수 있다. 그리고 약간의 상황에서 이것은 매우 편리할 수 있다. 

## Generator 문법

어떻게 우리는 generator함수를 선언할 수 있을까? 선언할 수 있는 방법의 리스트가 있따. 그러나 중요한 것은 함수 키워드 뒤에 별모양을 추가하는 것이다.

```jsx
function * generator(){}
function* generator(){}
function *generator(){}

let generator = function * () {}
let generator = function* () {}
let generator = function *() {}

let generator = *() => {} // SyntaxError
let generator = ()* => {} // SyntaxError
let generator = (*) => {} // SyntaxError
```

위의 예제에서 확인한 것처럼, 우리는 화살표함수로 Generator를 만들 수 없다.

다음으로, 메소드로서의 genrator이다. 이것은 함수와 같은 방법으로 선언할 수 있다.

```jsx
class MyClass{
  *generator(){}
  * genrator(){}
}

const obj = {
  *generator(){}
  * generator(){}
}
```

## Yield

이제 새로운 키워드인 yield를 확인해보자. 이것은 약간 return과 비슷하지만 return은 아니다. Return은 단순하게 함수가 호출된 후에 값을 리턴한다. 그리고 리턴은 리턴 상태이후에 다른 어떤 일을 하는 것을 허락하지 않는다.

```jsx
function withReturn(a){
  let b = 5;
  return a + b;
  b = 6; // b는 재할당 되지 않는다.
  return a * b; //  a + b는 리턴되지 않는다.
}

withReturn(6); //11
withReturn(6); //11
```

Yield는 다르게 작동한다.

```jsx
function * withYield(a){
  let b =5;
  yield a + b;
  b = 6; // 첫 번째 실행이후 재할당 될 수 있다.
  yield a * b;
}

const calcSix = withYield(6);

calcSix.next().value; //11
calcSix.next().value; //36
```

Yield는 값을 오직 한번만 리턴하고 다음 번에는 같은 함수를 호출하고 이것은 다음 yield상태로 움직일 것이다.

또한 genrator에서 우리는 항상 출력으로 객체를 얻는다. 이것은 항상 두 개의 프로터피 value 그리고 done을 가진다. 그리고 너가 생각한 것처럼, value는 value(값)을 리턴하고 done은 generator가 일을 끝냈는지 아닌지를 우리에게 보여준다.

```jsx
function * generator(){
  yield 5;
}

const gen = generator();

gen.next(); // {value: 5, done:false}
gen.next(); // {value: undefined; done:true}
gen.next(); // {value: undefined; done:true}
//다른 호출도 같은 값을 호출한다.
```

yield는 제너레이터에서 사용할 수있을 뿐만 아니라 return도 동일한 객체를 반환한다.  그러나 하지만 첫 번째 return 문에 도달하면 생성기가 작업을 완료한다.

```jsx
function * generator(){
  yield 1;
  return 2;
  yield 3;// 이 yield에 도달 할 수 없다.
}

const gen = generator();

gen.next(); // {value: 1, done:false}
gen.next(); // {value: 2, done:true}
gen.next(); // {value: undefined, done: true}
```

## Yield 위임

Yield는 또 다른 generator에게 작업을 위임할 수 있다. 이것은 너가 너가 원하는 만큼 많은 generator을 연결 할 수 있는 방법이다.

```jsx
function * anotherGenerator(i) {
  yield i + 1;
  yield i + 2;
  yield i + 3;
}

function * generator(i) {
  yield* anotherGenerator(i);
}

var gen = generator(1);

gen.next().value; // 2
gen.next().value; // 3
gen.next().value; // 4
```

## 메소드와 초기설정

generator은 재사용 가능하다. 그러나 너가 그들을 시작하는 것을 필요로 한다면, 다행히도 이것은 꽤나 단순하다.

```jsx
function * generator(arg = "Nothing"){
  yield arg;
}

const gen0 = generator(); //OK
const gen1 = generator("Hello"); // Ok
const gen2 = new generator(); // Not ok

generator().next();// 이것은 작동하나 언제나는 아니다.
```

그래서 gen 0 그리고 gen1 은 다른 것들에 영향을 끼치지 않을 것이다.그리고 gen2는 모든 것에서 작동하지 않을 것이다. 또한 너는 많은 에러를 얻을 것이다. 초기설정은 상태에 과정을 유지하기 위해 중요하다.

이제, 메소드를 확인해보자.

### Method next():

```jsx
function * generator(){
  yield 1;
  yield 2;
  yield 3;
}

const gen = generator();

gen.next(); //{value: 1, done: false};
gen.next(); //{value: 2, done: false};
gen.next(); //{value: 3, done: false};
gen.next(); //{value: undefined, done: true};

```

이것은 너가 가장 자주 사용할 메소드이다. 이것은 우리가 호출할 때마다 객체를 준다. 그리고 이것이 끝났을 떄, next()은 done을 true로 그리고 value를 undeifined로 설정한다.

next()는 반복 generator로 사용할 수 있을 뿐만 아니라 for-of Loop를 사용해서, 우리는 우리의 generator에서 모든 값을(객체가 아닌) 얻을 수 있다.

```jsx
function * generator(arr){
   for(const el in arr)
   yield el;
}

const gen = generator([0, 1, 2]);

for(const g of gen){
  console.log(g) // 0 , 1 , 2
}

gen.next(); // {value:undefined, done:true}
```

이것은 for-in loop에서는 작동하지 않고 너는 단지 숫자를 타이핑함으로써 프로퍼티에 접근할 수 없다.

### Methoed return():

```jsx
function * generator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = generator();

gen.return(); // {value: undefined, done: true}
gen.return('Heeyyaa'); // {value: "Heeyyaa", done: true}

gen.next(); {value: undefined, done: true}
```

Return()은 너가 가지고 있는 generator 함수 안에 코드를 무시할 것이다. 그러나 전달 된 인수를 기반으로 값을 설정하고 done을 true로 설정한다. return()후에 next()의 호출은 done객체를 리턴할 것이다.

### Method throw():

```jsx
function * generator(){
  yield 1;
  yield 2;
  yield 3;
}

const gen = generator();

gen.throw("Something bad"); // Error uncaught something bad
gen.next(); // {value:undefined, done:true}
```

이것은 간단하다. throw는 에러를 던진다. 우리는 이것을 try, catch를 사용해 다룰 수 있다.

## 커스텀 메소드에 실행

우리는 Generator 생성자에 직접적으로 접근 할 수 없다. 그래서 우리는 어떻게 새로운 메소드가 추가되는 지 이해하는 것을 필요로한다. 그것은 내가 하는것이고, 그러나 너는 다른 통로를 선택할 수 있다.

```jsx
function * generator() {
  yield 1;
}

generator.prototype.__proto__; // Generator {constructor: GeneratorFunction, next: ƒ, return: ƒ, throw: ƒ, Symbol(Symbol.toStringTag): "Generator"}

// generator는 전역 변수가 아니기 떄문에 우리는 이 처럼 써야만 한다.
generator.prototype.__proto__.math = function(e = 0) {
  return e * Math.PI;
}

generator.prototype.__proto__; // Generator {math: ƒ, constructor: GeneratorFunction, next: ƒ, return: ƒ, throw: ƒ, …}

const gen = generator();
gen.math(1); // 3.141592653589793
```

## generator에 사용

이전에, 우리는 숫자에 반복으로 generator를 사용했다. 그러나 얼마나 많은 반복문들이 필요로 되어지는 지 모른다면 어떨까? 이 문제를 해결하기 위해, generator함수안에 무한 루프 반복문을 만든다. 이 아래의 예제는 랜덤 숫자를 리턴하는 함수를 정의한다.

```jsx
function * randomFrom(...arr){
  while(true)
    yield arr[Math.floor(Math.random() * arr.length)];
}

const genRandom = randomFrom(1, 2, 5, 9, 4);

getRandom.next().value; // 랜덤 값을 리턴한다.
```

이것은 단순하다. 복잡한 함수의 예제를 보면, 우리는 함수에 throttled를 쓸 수 있다. 만약 우리가 이것에 대해서 모른다면, 이것에 대한 

<a href="[https://codeburst.io/throttling-and-debouncing-in-javascript-b01cad5c8edf](https://codeburst.io/throttling-and-debouncing-in-javascript-b01cad5c8edf)">좋은 기사가 있다.</a> 

## HTML에서 generator 사용

JavaScript에 대해 이야기하고 있기 때문에 generator를 사용하는 가장 확실한 방법은 HTML로 일부 작업을 수행하는 것입니다.

따라서 통과하려는 HTML 블록이 몇 개 있다고 가정하면 우리는 generator로 쉽게 이 작업을 수행 할 수 있지만 generator 없이도 이 작업을 수행 할 수있는 더 많은 방법이 있음을 명심해야 한다.

```jsx
const strings = document.querySelectorAll('.string');
const btn = document.querySelector('#btn');
const className = 'darker';

function * addClassToEach(elements, className){
  for(const el of Array.from(elements))
    yield el.classList.add(className);
}

const addClassToStrings = addClassToEach(strings, className);

btn.addEventListener("click",(el)=>{
  if(addClassToStrings.next().done)
    el.target.classList.add(className);
})
```

## 결론

generator를 사용하는 많은 방법이 있다. 예를들면, 그들은 비동기 작업과 함께 사용할 떄 유용할 수 있다.

번역글: <a href="[https://codeburst.io/what-are-javascript-generators-and-how-to-use-them-c6f2713fd12e](https://codeburst.io/what-are-javascript-generators-and-how-to-use-them-c6f2713fd12e)">[https://codeburst.io/what-are-javascript-generators-and-how-to-use-them-c6f2713fd12e](https://codeburst.io/what-are-javascript-generators-and-how-to-use-them-c6f2713fd12e)</a>
