### == vs ===

> "두 가지의 변수를 동등한 지 결정하는 것은 프로그래밍에서 가장 중요한 연산중에 하나이다." ESLint 오픈소스 프로젝트에 참여하고 몇개의 프로그래밍 책을 집필한 프로그래머가 책에서 말한 내용이다.

```jsx
if(x == y){
 // do something here
}

if(x === y){
 // do something here
}
```

위의 예제에서 두 가지의 차이는 두 번째 예제는 "strict equals" 또는 "identically equals"라 불리는 3개의 등호 연산자를 를 가지고 있다는 것이다.

완벽한 연습을 고수하려는 자바스크립트 초심자들은 두 개의 등호 연산자가 아닌 "triple-equals"를 사용하려고 할지도 모른다. 하지만 두 개의 차이를 완벽하게 이해를 못하거나 왜 "triple-equals"를 사용하는 것이 중요한 지를 이유를 모를 수도 있다.

### What's the Difference?

double-equals 연산자를 사용해서 비교함에 있어서, 만약 비교하는 것들이 같다면 true를 리턴할 것이다.그러나 한 가지 중요한 점이 있다. 만약 비교하는 것들이 다른 타입이라면, 타입 변화(type coercion)가 일어날 것이다.

자바스크립트의 값(value)은 특정한 타입에 속해있다. 이러한 타입은 Number, String, Boolean, Function, Object, Symbol이다. 만약 예를 들어 문자열과 숫자를 비교한다면,  브라우저는 비교전에 문자열을 숫자로 변환할 것이다. 유사하게, 만약 true 또는 false를 숫자와 비교하면, true, false 값은 1 또는 0 으로 변환 될 것이다.

```jsx
console.log(99 == "99"); //true
console.log(0 == false); //true
```

비록 이것이 좋아 보인다고 하더라도, 이것은 밑에 예제처럼 많은 문제를 야기 할 수 있다.

```jsx
console.log(" \n\n\n" == 0); // true
console.log("" == 0); // true
```

대부분의 자바스크립트 전문가들은 double-equals를 절대 사용하지 말고 항상 triple-equals 연산자를 사용하는 것을 추천한다.

triple-equals 연산자는 절대 타입 변환을 하지 않는다. 그래서 그것을 사용할 때마다, 현재의 값을 정확하게 비교하는 것이다. 이것은 값이 "strictly equal" 또는 "indentically equal" 하다는 것을 보장한다.

이것의 의미는 위에 예제들이 올바른 결과들을 생산한다는 것이다. 

```jsx
console.log(99 ==="99"); //false
console.log(0 === false); //false
console.log('\n\n\n' ===0); //false
console.log(0 === ""); //false
```

### What About inequality?

같지 않음을 표현할 때도 같은 룰이 적용된다. triple equals vs double equals 대신에, double equals vs single 을 사용한다. 

밑에는 위에있는 예제와 같은 예제지만 ! = 연산자를 사용했다.

```jsx
console.log(99 != "99"); //false
console.log(0 != false); //false
console.log('\n\n\n' != 0); //false
console.log('' != 0);//false
```

우리는 true의 값이 나오길 원했지만 대신, false가 나왔다. 이유는 타입 변환(type coercion)이 일어났기 때문이다. 

밑의 예제는 double-equals로 바꾼결과이고 올바른 결과를 얻을 수 있다.

```jsx
console.log(99 !== "99"); // true
console.log(0 !== false); // true
console.log(' \n\n\n' !== 0); // true
console.log(' ' !== 0); // true
```

### Conclusion

아마도, 대부분의 사람들이 이미 triple equals를 사용할 것이다. 자세히 알아보고 쓰는 것 또한 중요하다. 내가 생각하기에 최고의 요약은 "이것은 너의 코드에 데이터 타입 진실성을 유지하는데 도움이 된다"이다.

참고 : <a href="https://www.impressivewebs.com/why-use-triple-equals-javascipt/">https://www.impressivewebs.com/why-use-triple-equals-javascipt/</a>
