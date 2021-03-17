## Immediately-invoked Function Expression
함수를 이해하는 것, 그리고나서 현대에 어떻게 이용하는지 배우는 것은, 깨긋한 자바스크립트 코드 스킬에 중요하다.

자주 사용되어 지는 함수 코딩 패턴은 멋진 이름이 가졌다. IIFE라고 알려지고 "iffy"라고 불리는  Immediately-invoked Function Expression이다.

IIFE에 대해서 무엇인지, 왜 필요한지 이해하기 전에, 우리는 몇개의 근본적인 자바스크립트 함수 컨셉에 대해서 알 필요가 있다.

- Function Definition
- Function Expression
- Anonymous Function Expression
- Named Function Expression

### Named Function Expression

함수 표현은 이름을 가질 수 있다. 이 Named Function Expression 에 용도에 대해서 설명하는 것은 지루하다. 하지만 Named Function Expression 없이 IIFE를 이해할 수 있다.

```jsx
const fibo = function fibonacci(){
		// you can use "fibonacci()" here as this function expression has a name

}

//"fibonacci" here fails, but fibo() works.
```

위의 예제에서, 차이점은 함수 표현안에서 반복적으로 스스로 사용할 수 있는 "fibonacci"라는 이름을(name) 가지고 있다는 것이다.

### IIFE

이제 IIFE 시크릿 월드에 들어가보자. 그들은 몇개의 스타일리쉬한 변형을 가지고 있다. 이해하기 쉬운 첫 번째 변형에 대해서 보자.

```jsx
!function(){
		alert("Hello from IIFE!")
}();
//Shows the alert "Hello from IIFE!"
```

이 코드를 카피하고 브라우저 콘솔에 시도하면, 우리는 alert을 볼 수 있다.그리고 아무도 다시 이 alert를 할 수 없다. 이 함수는 함수가 실행되고 죽는 함수이다.

한번 이 함수에 대해서 이해해보자. 차이점은 함수 앞에 "!"이다.

1. 함수의 상태는 "function" 키워드로 항상 시작한다. 자바스크립트가 유효한 상태에서 첫 번째 단어로 "function" 키워드를 볼 때마다, 함수의 정의가 일어날 것이라고 기대한다. 그래서 이것의 발생으로부터 막기 위해, 우리는 "!"를 함수 키워드 앞에 붙인다.이것은 기본적으로, 자바스크립트에게 "!"뒤에 오는 모든 것을 처리하도록 강제한다.
2. 그러나 가장 흥미로운 부분은 우리가 즉시 함수표현을 실행한 세 번째라인에서 발생한다.

위에 스타일쉬한 변화는 "!" 를 "+","-" 또는 " ~ "로 대체해서 사용할 수 있따. 일반적으로 연산자는 사용이 가능하다.

또다른 빠른 변화는 밑에 예제이다.

```jsx
void function(){
		alert("Hello from IIFE")
}()
```

다시 말해, " void "는 기본적으로 함수가 표현으로 처리되도록 강제한다.

위에 패턴들은 IIFE로 부터 return에 관심이 없을 때 유용하다.

하지만 만약에 너가 IIFE로 부터 return 값을 원한다면, 어떻게 해야 할까?

### 기본적인 IIFE style

```jsx
// Variation 1
(function(){
		alert("I am an IIFE!");
}());

// Variation 2
(function(){
		alert("I am an IIFE,too!");
})();
```

두 개의 IIFE를 실행할 수 있다. 두 개의 차이점에 대해서 아는 것은 힘들 수 있지만 알아보자.

1. 1번 식에서, 함수에 ()는 외부의 ()에 포함된다. 다시 말해, 외부의 ()는 함수 표현 밖에 함수를 만드는데 필요하다.
2. 2번식에서 ()는 함수 표현에 ()를 덮은 것 밖에 있다.

두 개의 변화는 넓게 사용된다. 일반적으로 1번식이 선호되며, 어떻게 그들이 작동하는 지는 차이가 있다. 하지만 튜토리얼에선 이 두개의 방식 중 한가지만 사용하면 된다.

밑의 예제는 하나는 작동하고, 두 개의 예는 작동하지 않는다. 우리는 우리의  anonymouse function으로부터 IIFE를 이름을 붙이는 것을 시도해볼 것이다.

```jsx
// Valid IIFE
(function initGameIIFE(){
		// 작동가능
}())

// Invalid IIFE
function nonWorkingIIFE(){
		// syntax error!
}();

function(){
		// synctax error!
}();
```

우리는 함수 주위에 IIFE 패턴을 위한 형태로 필요로하는 ()가 없는 것이 이상하다는 것을 알게 될 것이다.

너는 IIFE형태의 함수 표현을 필요로 하다는 것을 기억해야 한다. 함수 상태/선언은 IIFE를 만드는 대에 사용되지 않는다.

### IIFEs and private variables

IIFE의 좋은 점 중에 한 가지는 함수 스코프를 만드는 능력과 관련이 있다.

IIFE안에 선언된 모든 변수들은 바깥의 세계에서 볼 수 없다. 밑에 예제를 보자.

```jsx
(function IIFE_initGame(){
		// Private Variables
		let lives;
		let weapons;

		init();

		function init(){
		// Private Variables
			lives = 5;
			weapons = 10;
		}
}())
```

위에 예제에서, 우리는 변수 두개를 선언했고 그들은 private하다. IIFE의 외부에 아무도 변수에 접근 할 수 없다.유사하게, 우리는 아무도 외부에서 접근할 수 없는 init 함수를 가지고 있다. 그러나 init 함수는 그들의 private 변수에 접근 할 수 있다.

우리가 모듈 패턴을 볼 때, 나는 IIFE 외부에 프라이빗한 변수에 어떻게 특권을 주는 지 그리고 접근을 통제하는지 설명할 수 있다. 그래서 비록 너가 IIFE에 익숙하더라도 밑에를 읽어 봐야 한다.

### IIFE에서 값을 리턴하기

만약 너가 IIFE에서 값을 리턴 할 필요가 없다면 계속해서 " ! ", 또는 연산자를 쓰는 첫 번째 방식을 사용하면 된다.

```jsx
const result = (function(){
		return "From IIFE";
}())

alert(result); // alert "From IIFE"
```

1. 위에 방식에서, 우리는 IIFE 리턴 상태를 가지고 있따.
2. 우리가 위에 방식을 실행할 때, alert에는 "From IIFE"가 출력된다.

일반적으로, IIFE는 즉시 실행되고 그리고 나서 리턴한 값은 result 변수에 할당된다.

이것은 우리가 볼 예제인 모듈 패턴에 매우 효과가 있다.

### IIFE with parameters

IIFE는 값을 리턴하는 것 뿐만 아니라 IIFE는 인자를 받을 수 있다. 빠르게 예제를 보자.

```jsx
(function IIFE(msg,times){
	for(let i =1; i <= times; i++){
			console.log(msg)	
	}
}("Hello",5))
```

1. 위의 예제에서, IIFE는 투개의 파라미터 msg,times를 가지고 있다.
2. 우리가 IIFE를 실행했을 때, 텅빈 () 대신에, 가득찬 ()은 IIFE에 인자로 통과되는 중이다.
3. 반복문은 이러한 인자를 IIFE안에서 사용한다.

IIFE의 이점

1. 자바스크립트는 항상 식별자를 찾기 전까지 현재의 함수 스코프 부터 찾고 계속해서  더 높은 스코프를 찾을 것이다. 우리가 위에 함수에서 반복문을 통과할 때, 로컬 스코프를 넘어서 찾는 것은 오직 한 번이다. 모든 IIFE에 참고는 IIFE에 로컬스코프를 넘어서 찾는 것을 필요로 하지 않는다. 이로 인한 성능 향상은 IIFE 코드가 얼마나 사소하거나 복잡한 지에 따라 크지 않을 수 있지만 여전히 알아두면 유용한 트릭이다. 

### 기본적인 자바스크립트 모듈 패턴

만약 너가 IIFE에 대해서 잘 알고 있다면, 모듈 패턴에 대해서 알아보자. 우리는 누구도 실수로 현재 값을 손상키시키지 않고 원할하게 작동하는 기본적인 singleton 객체를 실행할 것이다.

```jsx
const Sequence = (function sequenceIIFE(){
		let current = 0;

		return {
				getCurrentValue:function(){
						return current;
				},
		
				getNextValue: function(){
						current = current + 1;
						return current;
				}
		};		

}());

console.log(Sequence.getNextValue()); // 1
console.log(Sequence.getNextValue()); // 2
console.log(Sequence.getCurrentValue()); // 2
```

1. 위의 예제에서, 우리는 IIFE로부터 리턴하는 두 가지 함수를 추가했다.
2. getCurrentValue는 current 변수를 리턴한다.
3. getNextValue는 current 변수에 1을 더하고 current 변수를 리턴한다.

current 변수가 프라이빗한 IIFE이기 때문에, 함수에서 아무도 접근 할 수 없고, 변수는 closure를 통해서 current 변수에 접근 할 수 있다. 

이것은 유용한 자바스크립트 패턴이다. 이것은 IIFE와 closure의 결합이다.

이것은 배우 기본적인 모듈 패턴에 방식이다. 더 많은 패턴이 있지만 그것들 중에 대부분은 프라이빗한 closure스코프를 만들기위해 IIFE를 사용한다.

### ()의 생략

함수 표현 주위에 ()는 기본적으로 함수 상태 대신에 함수 표현이 되는 것을 강요한다.

그러나 자바스크립트 엔진은 함수 표현이 명백할 때, 기술적으로 ()로 함수를 감싸는 것을 필요로 하지 않는다.

```jsx
const result = function(){
		return "From IIFE";
}();
```

위에 예제에서, 함수 키워드는 첫 번째 단어가 아니다. 그래서 자바스크립트는 함수 정의/상태로 이것을 다루지 않는다. 유사하게, 너가 ()를 생략할 수 있는 장소가 존재한다.

그러나 나는 항상 ()를 사용하는 것을 선호한다. ()를 사용하는 것은 가독성을 증가시킨다. ()를 쓰면, 코드를 보는 사람은 IIFE를 확인하기 위해 코드를 마지막까지 내릴 필요가 없다.ㄷ그러나 나는 항상 ()를 사용하는 것을 선호한다. ()를 사용하는 것은 가독성을 증가시킨다. ()를 쓰면, 코드를 보는 사람은 IIFE를 확인하기 위해 코드를 마지막까지 내릴 필요가 없다.

참고 : <a href="https://vvkchandra.medium.com/essential-javascript-mastering-immediately-invoked-function-expressions-67791338ddc6">https://vvkchandra.medium.com/essential-javascript-mastering-immediately-invoked-function-expressions-67791338ddc6</a>
