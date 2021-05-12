## Recursion

느슨하게 정의 된 재귀는 큰 문제를 가져와 같은 문제의 여러 작은 인스턴스로 세분화하는 프로세스이다.

실제로는 일반적으로 자신을 호출하는 함수를 작성하는 것을 의미한다. 아마도이 개념의 가장 고전적인 예는 팩토리얼(계승) 함수일 것이다.

너는 수학으로터 n의 계승(factorial)은 그 수의 모든 양의 정수보다 적거나 같은 수에 곱이라고 기억할 지 모른다. 다시말해, 5의 계승은 5 x 4 x 3 x 2 x 1 이다. 수학 표기법은 5! 이다.

흥미있는 것은 너가 알아차렸을 지 모르지만 패턴 5!이다.그것은 현실로 단지 5 x 4! 이다. 그리고 4!는 단지, 4 x 3!이다. 계속해서 내려가면 너는 1을 만날 것이다.

여기에 우리가 자바스크립트에서 어떻게 쓸 것인지 확인할 수 있다.

만약 이것이 혼란스럽게 보인다면, factorial (3)의 예제를 사용하여 코드를 살펴 보는 것이 좋습니다.

1. factorial(3)은 3 x factorial(2)
2. factorial(2)은 2 x factorial(2)
3. factorial(1)은 if 조건을 충족하므로 1이다.

여기에서 실제로 발생하는 것은 너가 1로 내려가면서 콜스택을 정리하는 중이고, 그리고나서 스택은 끝난다. 너가 콜스택을 끝남으로써 너는 각각의 결과를 곱한다. 1 x 2 x 3 은 6이고, 그것은 너의 리턴 값이다.

## 문자를 반대로하기

동료 중 한 명이 최근 인터뷰에서 질문을 받았던 화이트 보드 질문에 대해 말했고 나는 그것이 일종의 재미있는 문제라고 생각했다.

> 문자열을 받아들이는 함수를 작성해라. 재귀적으로.

만약 너가 야심이 있는 타입이라면, 나는 약간의 시간동안 격려하고, 이것을 스스로 풀게 할 것이다. 재귀에 핵심 원리를 생각하면서, 재귀는 큰 문제를 가져다가 작은 인스턴스로 분해하는 것이다.

만약 너가 가로막힌다면, 여기에 나의 해결책이 있다.

다시 한 번 말씀 드리지만 문제가 발생할 경우를 대비하여 간단한 예제를 보여 주겠다.우리는 시작 지점으로 reverse("bar")를 사용한다.

1. reverse('bar')은 reverse('ar') + 'b' 이다.
2. reverse('ar')은 reverse('r') + 'a'이다.
3. reverse('r')은 if조건을 충족하므로, 단지 r이다.

호출 스택이 풀리면‘r’+‘a’+‘b’로 끝납니다.

## 재귀적인 map함수를 사용하는 것

우리의 마지막 예제에서, 우리는 map함수를 사용할 것이다. 우리는 이것을 밑에 처럼 사용하기를 원한다.

다시말해, 나는 강하게 약간의 시간을 주고 이것을 스스로 풀어보게 할 것이다.여기에 몇개의 상기할 힌트가 있다.

1. map()은 항상 배열을 새로운 배열을 리턴한다.
2. 문제를 더 작은 덩어리로 나누어라.
3. reverse()예제를 기억해라.

여기에 어떻게 하는지가 있다.

1. 배열 ['a','b','c']를 map()을 사용하면서 호출해라.
2. fn (‘a’) 호출 결과를 보유하는 새 배열을 만들어라.
3. ['A'].concat( map( [ 'b', 'c' ] ) )을 리턴해라.
4. [‘b’,‘c’]를 사용하여 1 ~ 3 단계를 반복한다.
5. ['b']를 사용하여 1~3단개를 반복한다.
6. 결국 우리는 재귀가 끝나고 빈 배열로 map()을 호출한다.

너는 이것을 현실의 앱에서 사용하면 안된다. 큰 배열에서 스택을 날려 버리고 ,더 중요한 것은 새로운 개체를 너무 많이 인스턴스화하여 엄청난 양의 쓰레기를 생성한다는 것이다. 생산 코드에서는 Array.map을 사용해라.

## 결론

재귀에 대해 이해하기 위해 여전히 어려움을 겪고 있다면, 내가 드릴 수있는 가장 좋은 조언은 작은 예제로 시작하여 호출 스택을 추적하는 것이다. reverse (‘abc’)와 같은 것을 시도하고 단계별로 살펴봐라.
해석글 : <a href="https://www.freecodecamp.org/news/recursion-in-javascript-1608032c7a1f/">https://www.freecodecamp.org/news/recursion-in-javascript-1608032c7a1f/</a>