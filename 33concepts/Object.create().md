## Object.create() 와 new 연산자의 차이점

그럼 왜 두 개의 차이점을 아는 것이 중요할까 ?

1. 우리는 절대적으로 가장 최근의 코드를 실행하지 않는다.
2. 좋은 개발자가 되기 위해서는 모든 타입의 코드를 다루는 방법을 알아야 한다. 오래된 코드를 포함해서~
3. 보이지 않는 곳에서, ECMAScript6에서 소개된 class 키워드는 new연산자를 활용하고 있다.

Object.create의 예제를 보자.

 

```jsx
const dog= {
	eat: function(){
		console.log(this.eatFood)
	}
}

const maddie = Object.create(dog);
console.log(dog.isPrototypeOf(maddie)); //true
maddie.eatFood = "NomNomNom";
maddie.eat(); // NomNomNom
```

위의 예제를 순서대로 진행하고 정확히 무슨 일이 일어나는지 보자.

1. 한 개의 매소드를 가지고 있는 dog 객체를 만든다.
2. Object.create(dog)를 사용해서 maddie를 초기화하고, dog 프로토타입이 설정된 새로운 객체를 만든다.
3. maddie에 프로토타입이 dog인지 확인한다.
4. this.eatFood를 통해 문자열을 설정하자.
5. 새롭게 만든 객체 maddie로 eat 함수를 호출하자.
6. 자바스크립트는 프로토타입 체인을 경험할 것이고  this키워드가 maddie로 설정된 dog에서 eat 메소드를 찾을 것이다.
7. 콘솔에 NomNomNom을 출력할 것이다.

Object.create()는 완전하게 프로토타입이 dog로 설정된 새로운 객체 maddie를 만든다. 그 신선한 maddie객체는 dog에서 eat메소드에 접근해야만 한다.

그럼 new 연산자를 확인해보자.

```jsx
const Dog = function(){
	this.eatFood = "NomNomNom";
	this.eat = function(){
		console.log(this.eatFood)
	}
}

const maddie = new(Dog);
console.log(maddie instanceof Dog); // True
maddie.eat(); //NomNomNom
```

어떻게 new 연산자가 함수를 쓰는 지 그리고 이것이 무엇을 하는지를 알아보자.

1. maddie라는 새로운 객체를 만든다.
2. maddie는 생성자 함수에 프로토타입을 상속한다.
3. 1단계에서 생성된 객체에 "this"가 설정된 생성자를 실행한다.
4. 생성된 객체를 반환

Object.create() 와 new연산자의 차이는 무엇일까? 그들은 같은 것으로 하는 것처럼 보인다. 그들은 둘다 새로운 객체를 만들고 프토로타입을 상속한다.

이 예제는 혼란을 깨끗하게 해준다.

```jsx
function Dog(){
	this.pupper = 'Pupper';
}
Dog.prototype.pupperino = 'Pups.';
const maddie = new Dog();
const buddy = Object.create(Dog.prototype);

//Using Object create()
console.log(buddy.pupper); //undefined
console.log(buddy.pupperino); // Pups
//Using New keyword
console.log(maddie.pupper); // Pupper
console.log(maddie.pupperino) // Pups
```

위의 예제에서 기록해야 되는 것은 

```jsx
console.log(buddy.pupper); // undefined
```

buddy.pupper가 undefined가 출력이 되었다. 비록 Object.create()가 Dog에 프로토타입으로 설정되었더라도, buddy는 생성자 함수에 this.pupper에 접근할 수 없다. 이것은 New Dog는 생성자 함수를 실행하지만 Object.create는 생성자 함수를 실행할 수 없기 때문에 차이점은 중요하다.

이것은 어떻게 Object.create() 와 new 연산자가 작동하는 지 뿐만 아니라 그들의 차이점에 대한 조금의 시야를 제공할 것이다.

참고 : <a href="https://medium.com/@jonathanvox01/understanding-the-difference-between-object-create-and-the-new-operator-b2a2f4749358">https://medium.com/@jonathanvox01/understanding-the-difference-between-object-create-and-the-new-operator-b2a2f4749358</a>
