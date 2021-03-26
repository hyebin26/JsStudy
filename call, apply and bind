## call, apply and bind

자바스크립트안에서 객체지향프로그래밍은 객체가 전부다. 왜냐하면 모든 것이 객체이기 때문에, 함수에 대한 추가 속성을 설정하고 접근 할 수 있다는 것을 이해하게 되었다.

프로토타입을 거처 추가의 메소드와 함수에 프로퍼티를 설정하는 것은 정말로 놀랍다. 그러나 그것들에 어떻게 접근할까?

우리는 this 키워드에 대해 알고 있다. 우리는 모든 함수들은 자동적으로 this 프로퍼티를 가진다고 배웠다. this키워드에 익숙해지는데 시간이 조금 걸리지만, 한번 우리가 이해하기 시작하면 이것이 얼마나 유용한지 깨닫게 될 것이다. this는 함수 안에 사용되고 항상 한 개의 객체를 참고한다. 

그러나 삶은 완벽하지 않다. 때때로, 우리는 this를 잃어버린다. 그 일이 일어났을 때, 우리는 결국 우리의 참조를 구하기 위해 혼란스러운 해킹을 사용한다.

```jsx
// 만약에 카트에 상품이 있다면 delete버튼을 첨가하기
if(this.isEmpty()){
	const deleteBtn = document.createElement("button");
	deleteBtn.innerHTML = "Empty Cart";
	deleteBtn.className = "delete";

	const myCard =this;
	// deleteBtn 매소드가 호출될때, "this"는 deletebtn이 되고 우리는
	// myCard에 콘텍스트를 잃을 것이다.
	// 이것이 "this"에 참조를 구해야 되는 이유이다.
	deleteBtn.addEventListener("click",function(){
		myCart.clearCart();
	});
	cardDOM.appendChild(delteBtn)
}
```

그래서 왜 우리가 this 참조를 구출하는 것을 필요로 할까? 왜냐하면 deleteBtn.addEventListener안에 this는 deleteBtn 객체를 참조한다. 그럼 더 좋은 해결책이 있을까?

### bind()

공식 문서에 bind 메소드는 호출될 때 this키워드가 제공된 값으로 설정된 새로운 함수를 만든다. 이것은 매우 파워풀하다. 이것은 우리에게 명백하게 함수가 호출될 때 this값을 정의할 수 있게 한다. 코드를 보자.

```jsx
const pokemon ={
	firstname: "Pika",
	lastname: "chu",
	getPokeName:function(){
		const fullname = this.firstname + '' + this.lastname;
		return fullname;
	}
};

const pokemonName = function(){
	console.log(this.getPokeName() + "I choose you!");	
}

const logPokemon = pokemonName.bind(pokemon);

logPokemon(); // Pika Chu I choose you
```

bind메소드를 사용할 때:

1. 자바스크립트 엔진은 새로운 pokemonName 인스턴스를 만들고 이것의 this변수로 pokemon에 바인딩한다. 이것이 pokemonName function을 카피한다는 것을 이해하는 것은 중요하다.
2. pokemonName 함수에 복사본을 만든 후에, 이것은 logPokemon()으로 사용할 수 있다. 비록 이것에 이름이 pokemon객체는 아니지만, 이것은 객체의 프로퍼티 그리고 매소드로 인식할 것이다.

그리고 좋은 점은, 우리가 bind를 한 후에 값은 다른 일반적인 함수처럼 사용할 수 있다. 심지어, 우리는 함수를 인자를 받고 업데이트 할 수도 있다.

 

```jsx
const pokemon = {
	firstname:"Pika",
	lastname:"Chu",
	getPokeName: function(){
		const fullname = this.firstname + '' + this.lastname;
		return fullname;
	}
}

const pokemonName = function(snack, hobby){
	console.log(this.getPokeName() + 'I choose you!');
	console.log(this.getPokeName() + "loves" + snack + ' and ' + hobby);
};

const logPokemon = pokemonName.bind(pokemon);

logPokemon("sushi", "algorithms"); // Pika Chu loves sushi and algorithms
```

### call, apply

공식 문서에 call 메소드는 함수와 주어진 this 값과 매개변수를 호출한다.

이것의 의미하는 것은 우리가 모든 함수를 호출 할 수 있고, 호출 함수 내에서 참조해야 하는 것을 명백하게 지정할 수 있다는 것이다. bind메소드와 정말 유사하다. 

하지만 bind와 call의  차이는 존재한다.

1. 둘 다 추가의 인자를 받을 수 있다.
2. 호출될 때 함수가 바로 실행된다.
3. call매소드는 이것이 호출될 때 복사본을 만들지 않는다.

call 그리고 apply는 정확히 같은 목적을 제공한다. 그들이 어떻게 작동하는지의 차이는 call은 모든 매개변수가 개별적으로 전달 될 것으로 예상하는 반면 apply는 모든 매개변수의 배열을 기대한다는 것이다.

```jsx
const pokemon = {
    firstname: 'Pika',
    lastname: 'Chu ',
    getPokeName: function() {
        const fullname = this.firstname + ' ' + this.lastname;
        return fullname;
    }
};

const pokemonName = function(snack, hobby) {
    console.log(this.getPokeName() + ' loves ' + snack + ' and ' + hobby);
};

pokemonName.call(pokemon,'sushi', 'algorithms'); // Pika Chu  loves sushi and algorithms
pokemonName.apply(pokemon,['sushi', 'algorithms']); // Pika Chu  loves sushi and algorithms
```

이러한 내장 매소드는 매우 유용하다. 비록 너가 현재의 프로그래밍에 사용하지 않을 지라도 너는 다른 사람의 코드를 읽을 때 마주칠 것이다.

참고: <a href="https://medium.com/@omergoldberg/javascript-call-apply-and-bind-e5c27301f7bb">https://medium.com/@omergoldberg/javascript-call-apply-and-bind-e5c27301f7bb</a>
