## Currying

currying은 너에게 자바스크립트에 대해 깊은 이해를 줄 수 있다. 한번 Currying 에 대해서 확인해보자.

```jsx
multiply = (n,m) => (n * m)
multiply(3,4) === 12 // true

curryedMultiply = (n) => ( ((m) => multiply(n,m)) )
triple = curryedMultiply(3)
triple(4) === 12 // true
```

Currying은 하나 이상의 인자를 가진 함수의 인자를 줄이는 과정이다. 

```jsx
f(n,m) -->f'(n)(m)
```

```jsx
multiply = (n,m) => (n*m)
multiply(3,4) === 12 //true

curryedMultiply = (n) => ( (m)=> multiply(n,m) )
triple = curryedMultiply(3)
triple(4) === 12 // true
```

## Uncurrying

Uncurrying은 currying에 반대이다.

```jsx
f(n)(m) --> f'(n,m)
```

```jsx
curryedMultiply = (n) => (m) => n * m
curryedMultiply(3)(4) === 12

multiply = (n,m) => curryedMultiply(n)(m)
multiply(3,4) === 12
```

## 실용적인 예제

### Javascript Bind

Function.prototype.bind()는 실제로 부분 적용을 수행한다.

```jsx
increment = add.bind(undefined,1)
increment(4) === 5
```

### React and Redux

가장 단순한 사용하는 예는 react-redux connect()함수이다.

```jsx
export default connect(mapStateToProps)(TodoApp)
```

### Event Handling

Event handler는 다수의 환경에서 재사용 될 수 있다.

```jsx
const handleChange = (fieldName) => (event) => {
  saveField(fieldName,event.target.value)
}

<input type="text" onChange={handleChange('email')}.../>
```

## 결론

Curry라는 언어는 거의 40년 동안 살아왔고 커리는 lambda calculus에서 필수적인 변형이다.

JavaScript는 요즘 특히 네이티브 promise 및 화살표 함수와 함께 기능적 언어로 더 많이 사용되고 있다. 언어를 마스터하기 위해 너는 lambda calculus에 분야 그리고 함수적인 프로그래밍에 지식을 가져야 한다. 디자인 패턴을 넘어서 이것은 더 높고 더 나은 소프트웨어 구성을 구축하는 좋은 방법이다.

해석글: <a href="https://blog.benestudio.co/currying-in-javascript-es6-540d2ad09400">https://blog.benestudio.co/currying-in-javascript-es6-540d2ad09400</a>
