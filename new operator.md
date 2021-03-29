## 'new' operator

### 4가지 규칙

new 연산자를 이해하는 가장 쉬운 방법은 이것의 하는 일을 이해하는 것이다. 너가 new 를 사용할 때 , 4가지 것들이 발생한다.

1. 새롭고 비어있는 객체를 생성한다.
2. 우리의 새롭게 만들어진 객체에 this를 바인딩한다.
3. 우리의 새롭게 만들어진 객체에 "__proto__"라고 불리고 생성자함수의 프로토타입 객체를 가리키는 프로퍼티를 추가한다.
4. 함수의 마지막에 return this를 추가한다. 그래서 만들어진 객체는 함수에서 리턴된다.

아직 모든 규칙을 이해하지 못해도 괜찮다. student라고 불리는 생성자 함수를 만들어보자. 이 함수는 두 가지의 파라미터 name, age를 받을 것이다.

```jsx
function Student(name,age){
		this.name = name;
		this.age = age;
}
```

우리의 생성자 함수와 new 연사자를 연관시켜보자. 우리는 두 가지의 매개변수 "hyebin" and 27을 통과시킬 것이다.

```jsx
const first = new Student("hyebin",27);

console.log(first.name); // hyebin
console.log(first.age); // 27
```

그래서, 우리가 위의 코드를 실행할 때 무슨 일이 일어날까?

1. fisrt 객체가 생성된다.
2. this는 우리의 first 객체에 묶여있다. 그래서 this에 참조는 first를 가리킨다.
3. 우리의 "__proto__"는 추가된다. "first.__proto__"는 현재 Student.prototype을 가리킨다.
4. 모든 것들이 끝난 후에, 우리의 new first 객체는 우리의 new fisrt변수에 의해 리턴된다. 

### Prototypes

모든 자바스크립트 객체는 프로토타입을 가지고 있따. 자바스크립트에 모든 객체들은 그들의 프로토타입으로 부터 메소드와 프로퍼티를 상속한다.

예를 확인해보자. Chrome Developer Console을 열고 Student 함수를 타이핑해보자.

```jsx
function Student(name,age){
		this.name = name;
		this.age = age;
}

Student.prototype;
```

모든 객체들이 프로토타입을 가지고 있따는 것을 증명하기 위해 이렇게 타이핑해보자.

객체들이 리턴되었다. 새로운 strudent를 만들어보자.

```jsx
const second = new Student("Jeff",50);
```

우리는 우리의 두 번째 객체를 만드는데 Student 생성자 함수를 사용했다. 그리고 우리가 new 연산자를 사용한 후에, "__proto__" 프로퍼티는 우리의 두 번째 객체에 추가되었어야만 한다. 이것은 부모의 생성자를 가르켜야한다. 한번 확인해보자.

```jsx
second.__proto__ === Student.prototype; // true
```

마침내, 우리의 Student.prototype.constructor은 우리의 Student생성자 함수를 가르킨다.

```jsx
Student.prototype.constructor; //fucntion Student
```

이것은 복잡하다. 밑에 이미지를 보고 도움을 얻어보자.


![https://miro.medium.com/max/743/1*RIY6LBqQAbXPzMXMwH5d-Q.png](https://miro.medium.com/max/743/1*RIY6LBqQAbXPzMXMwH5d-Q.png)

우리의 Student 생성자 함수는( 다른 생성자 함수들 또한) prototype이라고 불리는 프로퍼티를 가지고 있따. 이 프로토타입은 생성자 함수를 다시 가리키는 constructor라는 객체가 있다. 그리고나서, 우리가 새로운 객체를 만들기 위해 new 연산자를 사용할 때, 각각의 객체는 새로운 객체를 다시 Student.prototype으로 되돌리는 링크인 "__proto__"프로퍼티를 가지고 있다.

왜 이것이 중요할까?

 

> 이것이 중요한 이유는 상속때문이다. 프로토타입 객체는 생성자 함수와 함께 창조된 모든 객체들사이에서 공유된다. 이것의 이미는 우리는 우리의 객체들의 전부를 사용할 수 있는 프로토 타입에 프로퍼티와 함수를 추가 할 수 있다.

위에 예제에서, 우리는 두 가지 객체를 만들었고 그러나 2개가 아니라 20.000 만들어야 한다면? 갑자기, 우리는 각 학생 객체가 아닌 프로토 타입에 공유 함수를 배치함으로써 처리하는 능력을 엄청나게 절약하고 있다.

이 아이디어를 얻는 예제를 들어보자. 

```jsx
Student.prototype.sayInfo = function(){
		console.log(this.name + 'is' + this.age + 'years old');
}
```

다시, 우리가 하는 것은 Student 프로토타입에 함수를 추가하는 중이다. 우리가 만든 모든 student는 새로운 sayInfo함수에 모두 접근 할 수 있다.

```jsx
second.sayInfo(); //Jeff is 50 years old
```

다시 새로운 학생을 추가해보자.

```jsx
const third = new Student("Tracy",15);

third; //Student{name :"Tracy", age: 15}
third.sayInfo() // Tracy is 15 years old
```

그리고 이것은 상속때문에 작동한다. 자바스크립트 객체를 사용하면, 객체가 먼저 호출하는 프로퍼티를 가지고 있는지 볼 것이다. 만약 이것이 없다면, 이것은 위쪽으로 갈 것이고, 이것은 프로토 타입으로 이동하여, 이 프로퍼티를 가지고 있냐고 물을 것이다. 이 동일한 패턴은 해당 속성을 찾거나 전역 객체에서 프로토 타입 체인의 끝에 도달 할 때까지 계속된다.

상속은 과거에 toString ()과 같은 함수를 사용할 수 있었던 것과 같은 이유이다. 이것에 대해 생각해보자. 너는 toString()을 사용하지 않았다. 그러나 너는 그것을 잘 사용할 수 있었다. 그 이유는 메소드와 다른 내장 JS 메소드가 Object 프로토타입에 있기 때문이다. 우리가 만든 모든 객체들은 궁극적으로 Object 프로토타입을 대표한다. 그리고 확실하게, 우리는 다음과 같이 메소드를 덮어 쓸 수 있다.

```
const name = {
  toString: function(){
    console.log('Not a good idea');
  }
};
name.toString(); // Not a good idea
```

우리의 객체는 처음으로 프로토타입으로 움직이기 전에 메소드를 가지고 있는지 확인한다. 메소드가 있으므로, 실행되고 상속이 필요하지 않다. 하지만 이것은 좋은 생각이 아니다. 전역 메서드를 그대로두고 함수 이름을 다른 것으로 지정한다.

### 결론

새로운 개발자로서 이것은 이해하는데 매우 어려운 개념 일 수 있지만 일단 그렇게하면 훨씬 더 나은 코드를 작성할 수 있습니다. 프로토타입을 상요하면 수백, 수천 개의 객체에서 동일한 코드를 빠르고 휴과적으로 공유할 수 있다.
