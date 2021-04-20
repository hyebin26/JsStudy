## Higher Order Functions in JS

고차함수(Higher-order function)는 처음보면 겁을 줄 수 있다. 그러나 그들은 배우는 것이 어렵지 않다. 고차함수는 또 다른 함수를 매개변수로 받는  또는 결과로 함수를 리턴하는 함수이다. 이것은 자바스크립트에서 가장 유용한 패턴이고 함수 프로그래밍에서 큰 중요성을 가지고 있다.

우리는 너가 친근하게 다를 수 있는 예제인 Array.map()부터 시작할 것이다. 예제를 확인해보자.

```jsx
const characters = [
 {name:'Han Sole'},
 {name:'Luke SkyWalker'},
 {name:'Leia Organa'}
];

const names = characters.map(character => character.name);
console.log(names); // 'Han Sole' 'Luke SkyWalker' 'Leia Organa'

const names2 = characters.map(function(character) {
  return character.name;
});
console.log(names2); // 'Han Sole' 'Luke SkyWalker' 'Leia Organa'
```

어떻게 map 매소드가 매개변수로 또 다른 함수를 받는 지 본 적이 있는가? 그것은 고차함수의 정의와 맞지 않은가? 고차 함수는 결국 그렇게 무섭지 않다. 사실상, 너는 이것을 깨닫지 않고 사용해 봤을 것이다.

그럼, 함수 프로그래밍에서 더 연관 있는 또다른 것을 확인해보자. 함수 합성은 고차함수의 아이디어를 받고 매우 강력한 방법으로 함수합성을 만드는 것을 허락한다. 우리는 작고, 집중된 기능을 취할 수 있고 복잡한 기능을 구축하기 위해 그들을 결합할 수 있다. 계속해서 이러한 "building block"함수를 작게 함으로써, 그들은 테스트하기 더 쉽고, 이해하기 쉽고 재사용하기 쉽다.

또 다른 예제를 확인해보자. 만약 처음에 그 코드가 조금 복잡할 지라도 걱정하지마라. 

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

/* Logs out the following array
   [ "http://example.com/img/luke.jpg",
     "http://example.com/img/han.jpg",
     "http://example.com/img/leia.jpg"
   ]
*/
```

이것을 순서대로 확인해보고 이와 같은 기능이 어떻게 유용 할 수 있는지 명확하게 확인하겠다. 

1. 두 번째 라인에서 우리는 객체에 배열을 선언하는 중이다. 이러한 방식으로 데이터 (예 : API 호출의 결과)를 받고 작업하는 것은 매우 일반적이다.
2. 그리고나서 우리는 우리가 humans로 부르는 첫 번째 함수를 선언했다. 이 함수에 대해 기록하는 것에서 중요한 점은 이것은 순수 함수라는 것이다.  이것의 의미는 같은 입력을 준다면, 이것은 같은 결과를 항상 리턴할 것이다. 이것은 함수 프로그래밍에서 중요한 컨셉이다. 이것은 함수를 테스트, 리팩토링, 이해하기 쉽게 만들어준다.
3. 그리고나서 우리는 두 번째 함수 image를 선언했다. map안에 지나가는 콜백함수를 보자.

```jsx
function(character){
  return character.img;
}
```

나는 코드를 더 쉽게 만들기 위해 "character"를 사용해왔다. 하지만 만약 대신에 "item"또는 어떤 것을 사용하면, 너는 어떻게 남은 코드로 부터 함수를 분리시키는지 완벽하게 볼 수 있다. 이것은 너가 img 특성을 추출하기 원하는 객체에 배열에 사용될 수 있다.

4. 다음으로, 우리는 두 개의 함수를 매개변수로 받고 또 다른 함수를 리턴하는 compose 고차 함수를 가지고 있다. 이 함수는 왼쪽에서 오른쪽으로 호출 된 두 함수의 결과를 결합하는 데에도 재사용 할 수 있다. 데이터를 전달하고 함수가 필요한 방식으로 데이터를 변환하도록 한다.

5. 그리고나서 우리는 humans 그리고 images 함수들을 지나가는 함수를 호출할 것이고 displayCharacterImages 변수에 이것을 할당할 것이다. 만약 우리가 displayCharacterImages의 값을 로그에 호출한다면, 우리는 compose에 의해 리턴되는 내부의 함수를 볼 것이다.

```jsx
function(data){
  return func2(func1(data));
}
```

6. 우리가 마지막으로 displayCharacterImages를 호출했을 때, 우리는 func1을 통과하는, characters 배열에 지나간다. 이 함수는 humans 특성만 포함하는 객체 배열을 반환하고 즉시 데이터 세트에있는 humans 특성의 이미지 배열을 반환하는 이미지 함수 인 func2에 전달한다. 우리가 만든 팬 사이트의 일부에 해당 캐릭터의 이미지를 표시하기 위해 해당 배열을 사용할 수 있다고 상상할 수 있다.


바라건대, 이것은 고차함수에 대한 그리고 너가 복잡한 행동을 구축하기 위해 작은 함수들을 결합하는 것에서 어떻게 고차함수를 사용할 것인지에 대한 깔끔한 소개였으면 좋겠다. 만약 너가 이것을 즉시 이해하지 못한다면, 걱정하지마라. 이것은 함수 프로그래밍을 하면서 점차 익숙해질 것이다. 그러나 한번은 너는 이것을 다룰 것이고, 매우 강력해질 것이고 더 적은 에러와 함께 유연한 코드를 쓰는 것을 허용할 것이다.

## 결론

쉽게 말해 고차함수는 함수를 매개변수로 받고 함수를 리턴할 수 있는 함수이다.
