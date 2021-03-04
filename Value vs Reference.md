## Value vs Reference

자바스크립트는 값에 의한 전달( passed by value)이 일어난 데이터 타입5개 (boolean, Null, Undefined, String, Number)을 가지고 있다. 우리는 이러한 데이터 타입을 원시타입( Primitive Type)이라고 부룬다.

또 자바스크립트는 참조에 의한 전달(passed by reference)이 일어나는 3가지의 데이터 타입(Array, Object, Function)을 가지고 있지만 크게 보자면 전부 객체로 볼 수 있다.

---

### 원시타입

어떠한 원시타입이 변수에 할당되면, 우리는 그 변수를 원수타입을 가진 변수라고 생각할 수 있다.

```jsx
const x = 10;
const y = 30;
const z = null;
const a = x;

console.log(x,y,z,y) // -> 10,30,null,10
```

이 변수들을 다른 변수에 "=" 이라는 키워드를 이용하여 할당할 때, 우리는 새로운 변수에 값을 복사(Copy)하게 된다. 복사된 값이므로 변수를 바꾸더라도 영향이 없다.

---

### 객체(Object)

원시타입이 아닌 값이 할당된 변수들은 그 값으로 향하는 참조(Reference)를 갖게 된다. 객체는 우리의 컴퓨터 메모리 어딘가에 생성된다. 

만약 arr=[] 를 작성했을 때 우리는 메모리 내부에 배열을 만든 것이고, 변수 arr이 갖는 것은 그 배열이 위치한 주소이다.

```jsx
const arr = [];
arr.push(1)
```

위의 예제는 메모리 내부에 있는 배열의 값은 변하지만 변수의 값(주소)는 변하지 않는다. arr을 사용할 때 이를테면 값의 push같은 일을 할때 자바스크립트 엔진은 메모리 속의 arr의 위치로 가고 거기에 저장된 정보를 이용해 작업을 수행한다.

---

### 참조재할당

```jsx
let obj = {first : "reference"}
obj = {second : "Hello" }

console.log(obj) // -> {second : "Hello"}
```

위에 객체는 obj안에 저장됐던 주소 값이 변경된다. 첫번째 겍체는 아직 메모리상에 표기가 된다. 하지만 자바스크립트 엔진은 가비지 콜레터를 발동시켜 객체를 가르키는 참조가 없을 시 객체를 삭제한다. 

---

### 참조할당

```jsx
const reference = [1];
const refCopy = refernce;

reference.push(2)
console.log(reference, refCopy) // -> [1,2],[1,2]
```

객체와 같은 참조 타입의 값이 `=`과 같은 키워드를 이용하여 다른 변수로 복사될 때, 그 값의 주소는 실제로 복사된다. 각각의 변수는 같은 배열로 향하는 래퍼런스를 갖게 된다.

---

### 순수 함수(Pure Function)

함수 중에 함수 바깥 스코프에 아무런 영향도 미치지 않는 함수를 우리는 순수 함수라고 한다. 함수가 오직 원시 값들만을 파라미터로 이용하고 주변 스코프에서 어떠한 함수도 이용하지 않는다면 그함수는 자연스레 순수함수가 된다. 안에서 만들어진 모든 변수들은 함수에서 반환이 실행되는 즉시 가비지 콜렉션 처리가 된다.

```jsx
function changeAge(person){
		person.age = 25;
		return person;
}

const hyebin = {
		name: "Hyebin",
		age:27
}

const changeHyebin = changeAge(hyebin);

console.log(hyebin); ->// {name : "Hyebin" , age: 25};
console.log(changeHyebin);// -> {name : "Hyebin" , age: 25};
```

비순수 함수는 객체를 받아서 age 프로퍼티를 25라는 값으로 바꾼다. 객체를 받아온 값에 그대로 명령을 실행하기 때문에 이 함수는 hyebin객체를 직접적으로 바꾼다. 즉, hyebin, changeHyebin은 같은 참조를 가진다.

```jsx
function changeAgePure(person){
		const newPersonObj= JSON.parse(JSON.stringify(person));
		newPersonObj.age = 25;
		return newPersonObj;
}

const hyebin = {
		name : "Hyebin",
		age : 27
}

const changePureHyebin = changeAgePure(hyebin);

console.log(hyebin); // -> {name:"Hyebin", age: 27}
console.log(changePureHyebin); // -> {name:"Hyebin", age: 25}
```

위에 예제는 객체를 받아서 사용하는 것이 아니라 직접 newPersonObj라는 객체를 만들어서 사용한다. 이 새로운 객체는 age프로퍼티를 변경할 때 원본에 영향을 받지 않는다. 위에 함수는 순수함수로, 바깥스코프에 영향을 받지 미치지 않고 심지어 인자로 받은 객체까지도 영향을 받지 않는다.

---

### 테스트

```jsx
function changeAgeAndReference(person) {
	person.age = 25;
	person = {
		name: 'John',
		age: 50
    };
	return person;
}

var personObj1 = {
	name: 'Alex',
	age: 30
};

var personObj2 = changeAgeAndReference(personObj1);

console.log(personObj1); // -> ?
console.log(personObj2); // -> ?
```

<a href="https://velog.io/@jakeseo_me/2019-04-01-1904-%EC%9E%91%EC%84%B1%EB%90%A8-2bjty7tuuf">https://velog.io/@jakeseo_me/2019-04-01-1904-%EC%9E%91%EC%84%B1%EB%90%A8-2bjty7tuuf</a>
