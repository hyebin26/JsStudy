### Primitive Type

- 우선 자바스크립트는 느슨한 타입(Loosely typed)언어 혹은, 동적(dynamic)언어이다. 그 말은, 변수의 타입을 미리 선언할 필요가 없다. 타입은 프로그램이 처리되는 과정에서 자동으로 파악될 것이다. 또한 그말은 같은 변수에 여러 타입의 값을 넣을 수 있다는 뜻이다.

```jsx
let foo = 42; //foo는 number
let foo = "bar"; // foo는 이제 string
let foo = true; // forr는 이제 boolean
```

- Primitive Type은 String, Number, Boolean, Symbol, Undefined, Null 타입이 있다.
- Boolean 타입은 true와 false 값을 가질 수 있고, Undefined는 값을 할당하지 않은 변수는 undefined값을 가진다. 그리고 Null타입은 딱 한가지 값 null을 가질 수 있따.
- Number타입은 숫자를 말하며, 세 가지 의미있는 몇가지 상징적인 값들도 표현할 수 있다. 이 값은 +무한대, -무한대 그리고 NaN(숫자가아님)이 있다. 숫자가 보통 값만 으로 표현되긴 하지만, 비트 마스킹 기법으로 한 숫자 안에 여러 Boolean값을 저장하는 데도 있으나, 코드가 복잡해져서 크기를 최대한 줄여야 하는 상황에서만 사용을 고려해야 한다.
- String타입은 텍스트 데이터를 나타내는데 사용하며, 16비트 부호없는 정수값 요소들의 집합이다.
- Symbol 타입은 ECMAScript6에서 추가되었다. Symbol은 유일하고 변경불가능한(immutable) 기본값이다. value는 "Symbol value"라고 부르는 Symbol 데이터 타입을 가지고 있다. 자바스크립트 엔진에서 Symbol value는 동적으로 이름이 없고, 유니크한 value를 제공해주는 Symbol 함수를 호출하면서 만들어진다. Symbol은 객체에 property로 사용되어 질 수 있다. Symbol 값은 고유한 식별자를 가르킨다.

```jsx
let Sym1 = Symbol("Sym")
let Sym2 = Symbol("Sym")

console.log(Sym1 === Sym2) return false;
// Symbol은 유니크함을 보중한다.
// 비록 우리가 Symbol이 같은 값(description)으로 만들었더라도  
// 그들은 다른 값이다.

```
### "abc".length의 값

Javascript 는 primitive 타입과 object사이를 강제로 바꿀 수 있다. 문자 값인 경우 property length의 값에 접근하기 위해 String 객체로 강제로 바뀐다. 그리고 String 객체는 잠깐동안 사용되고 garbage collection에 희생된다.
