## Function composition은 무엇일까?

Function composition은 다수의 단순한 함수들을 하나의 함수로 복잡하게 만들기위해 결합하는 매커니즘이다. 이러한 함수들의 결과는 다음을 지나간다. 수학적인 면에서, 우리는 자주 f( g(x) )와 같은 것을 썼다. 그래서 이것은 f를 지나가는 g(x)의 결과이다. 프로그래밍에서 우리는 유사한 것을 사용함으로써 합성(composition)을 할 수 있다. 내가 다음의 연산을  하는 것을 필요로 한다고 가정해보자: 2 + 3 * 5 너가 알고있겠지만, 곱셈은 덧셈을 넘어 우선순위가 있다. 그래서 너는 3 * 5부터 시작할 것이고 다음으로 결과에 2를 더할 것이다. 자바스크립트에서 이것을 써보자.

```jsx
const add = (a,b) => a + b;
const mult = (a,b) => a * b;
add(2, mult(3,5));
```

이것은 add 함수를 지나가는 곱셈에 결과이기 때문에 함수 합성의 형태이다. 한 발짝 더 나아가보고 매우 유용한 함수 합성에 또 다른 경우를 확인해보자. 내가 유저들에 리스트를 가지고 있고 모든 성인 유저들에 이름을 추출하는 것을 필요로 한다고 가정해보자. 그럼 나는 개인적으로 이처럼 쓸 것이다.

```jsx
const users = [
  {name:"Jiyoung",age:14},
  {name:"Youngseo",age:27},
  {name:"Hyebin",age: 25}
]
const filter = (cb,arr) => arr.filter(cb);
const map = (cb,arr) => arr.map(cb);

map(u => u.name,filter(u => u.age >= 18, users));
// ["Youngseo","Hyebin"]
```

이것은 좋으나 만약 우리가 합성을 자동적으로 할 수 있다면 더 좋아보인다. 적어도 이것은 훨씬 읽기 쉬울 수 있다.

## 함수 합성에 자동화

이 부분에서 우리의 목표는 두 가지 혹은 훨씬 많은 함수들 그리고 그들을 합성하는 고차함수(high order function)를 만드는 것이다. 그럼 우리 미래의 함수에 마지막 특징을 정의해보자.

```jsx
compose(function1, function2,..., functionN): Function
```

우리는 밑의 예제처럼 호출하기를 원할 것이다.

```jsx
compose(add1,add2)(3) // 6
```

위의 예제에 함수의 구현은 이렇게 될 것이다.

```jsx
const compose = (...functions) => args => functions.reduceRight((arg,fn)=>fn(arg),arg)
```

위의 한줄은 너에게 어떤 합수를 결합하는 것을 허용한다. 무슨 일이 일어나는지 설명해보자.

- compose는 고차함수이다. 이것은 또 다른 함수를 리턴하는 함수이다.
- compose는 인자로 다수의 함수들을 받고 우리는 spread연산자를 이용해서 함수들의 배열안에 그것들을 변환한다.
- 그리고 우리는 단순하게 우리의 함수에 reduceRight를 적용한다. 콜백에 첫 번째 매개변수는 현재의 인자이다. 그 두 번째 인자는  현재의 함수이다. 그리고나서, 우리는 현재의 인자와 함께 함수를 호출하고 결과는 다음의 호출에서 사용한다.

우리는 이전의 에제 함수에 이것을 사용할 수 있다. 나는 자발적으로 map 그리고 filter함수들을 대입할 수 있다. 이것은 훨씬 읽기 쉽다.

```jsx
const filter = cb => arr => arr.filter(cb);
const map = cb => arr => arr.map(cb);

compose(
  map(u => u.name),
  filter(u => uage >= 18)
)(users) //["Youngseo","Hyebin"]
```

너에게 지난 예제를 보여줬다. 전통의 MapReduce를 구현해보자.

## MapReduce 함수 결합

MapReduce에 원리는 단순하다. 이것은 단지 데이터에 세트에 map을 적용하고 하나의 결과를 생산하기 위해 그 결과를 축소시킨다. 이것은 전형적인 함수 결합의 원리이다. 예를 들면, 우리는 단어들의 수를 세는 word counter를 구현할 수 있다. Map은 값을 마주쳤을 때 단지 하나만 보내는 책임이 있고 reduce는 결과를 생산하기 위한 마지막 배열을 요악할 것이다.

```jsx
const reduce = cb => arr => arr.reduce(cb);

const mapWords = map(()=> 1);
const reduceWords = reduce((acc,curr)=> acc += curr)(0)

compose(reduceWords, mapWords)(['foo','bar','baz'])
```

## 연결(Pipe) 또는 결합

나는 Yeiber Cano가 내가 처음에 구현한 compose 대신에 pipe를 언급한 이후로 이 부분을 추가했다. 

compose와 pipe의 주된 차이는 결합의 순서이다. Compose는 오른쪽에서 부터 왼쪽으로 수행한다. 반면에 Pipe는 왼쪽에서 부터 오른쪽으로 결합을 수행한다. pipe 고차함수를 적어보자.

```jsx
const pipe = (...functions) => args => functions.reduce((arg,fn)=>fn(arg),args)
```

 이 예제에서, 우리는 reduce를 왼쪽에서 부터 오른쪽으로의 결합을 수행하기 위해 reduceRight대신해서 사용한다.

다음으로, 우리는 이전의 예제에서 우리가 최근에 만든 함수에 적용할 수 있다.

```jsx
pipe(
  filter(u => u.age >= 18),
  map(u => u.name)
)(users) // ["Youngseo","Hyebin"]

```

약간의 사람들은 읽기 쉽기 때문에 compose를 대신해 pipe를 사용하는 것을 선호할 수 있다. 적어도, 우리는 이것이 훨씬 자연스럽다고 동의 할 수 있다.

## 고차함수(Higher Order Functions)

고차함수는 또 다른 함수를 매개변수로 받는 또는 결과로 함수를 리턴하는 함수이다. 

```jsx
const characters = [
  {name: 'Luke Skywalker', img: 'http://example.com/img/luke.jpg', species: 'human'},
  {name: 'Han Solo', img: 'http://example.com/img/han.jpg', species: 'human'},
  {name: 'Leia Organa', img: 'http://example.com/img/leia.jpg', species: 'human'},
  {name: 'Chewbacca', img: 'http://example.com/img/chewie.jpg', species: 'wookie'}
];

const humans = data => data.filter(character => character.species === 'human');
const images = data => data.map(character => character.img);
const compose = (func1, func2) => data => func2(func1(data));
const displayCharacterImages = compose(humans, images);

console.log(displayCharacterImages(characters));
/*
[ "http://example.com/img/luke.jpg",
     "http://example.com/img/han.jpg",
     "http://example.com/img/leia.jpg"
 ]
*/
```

## 결론

여기에 있는 예제는 사소한 예제이다. 하지만 너는 이것을 훨씬 복잡한 것에 사용할 수 있다는 것을 알아야 한다. 함수의 결합은 lodash 또는 ramda같은 가장 함수적인 라이브러리에 구현되어 졌다. 너는 함수 결합의 변종을 발견할 수 있다. 예를 들어 Ramda는 프라미스를 리턴하는 함수들을 결합하는 것을 허용하는 composeP함수를 보여준다.

참고글: <a href="https://www.codementor.io/@michelre/use-function-composition-in-javascript-gkmxos5mj">https://www.codementor.io/@michelre/use-function-composition-in-javascript-gkmxos5mj</a>
