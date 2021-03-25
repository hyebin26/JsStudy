## Class vs Factory function

ECMAScript 2015(ES6)에 class 문법이 나오면서, 우리는 객체를 만드는 데 두 가지 비교되는 방식을 가지고 있다. 그들을 비교하기 위해서, class 그리고 factory function으로 같은 객체를 정의해보겠다.

TodoModel as a Class

```jsx
class TodoModel{
		constructor(){
			this.todos = [];
			this.lastChange = null;
		}
		addToPrivateList(){
			console.log("addToPrivateList");
		}
		add(){ console.log("add") }
		reload(){}
}
```

Todomodel as a Factory Fucntion

```jsx
function TodoModel(){
		const todos = [];
		const lastChange = null;
		function addToPrivateList(){
				console.log("addToPrivateList");
		}
		function add(){ console.log("add") }
		function reload(){}
		return Object.freeze({
        add,
        reload
    });
}
```

### 캡슐화

우리가 알아야 할 첫 번째는 class객체에 있는 모든 members, field, method는 public이다.

```jsx
const todoModel = new Todomodel();
console.log(todoModel.todos); //[]
console.log(todoModel.lastChange) //null
todoModel.addToPrivateList(); //addToPrivateList
```

캡슐화에 결여는 안전상의 문제를 만들 수도 있다. 직접적으로 개발자 콘솔로부터 정의될 수 있는 전역 객체에 예를 들어보자.

factory function을 사용할 때, 오직 method는 public으로 노출될 것이고, 모든 것은 캡슐화가 될 것이다.

```jsx
const todoModel = TodoModel();
console.log(todoModel.todos); //undefined
console.log(todoModel.lastChagne); //undefined
todoModel.addToPrivateList(); //taskModel.addToPrivateList is not a function
```

### this

this 컨텍스트를 잃어버리는 문제는 클래스를 사용할 때 여전히 존재한다. 예를 들어서 this는 중첩 함수에서는 없어진다. 이것은 코딩을 하는 동안 화가 날뿐만아니라 지속적인 버그의 소스가 된다.

```jsx
class TodoModel{
		constructor(){
				this.todos = [];
		}
		reload(){
				setTimeout(function log(){
						console.log(this.todos); //undefined
				},0);
		}
}
todoModel.reload(); //undefined
```

또는 this는 method가 DOM이벤트처럼 콜백으로 사용될 때 콘택스트가 없어진다.

```jsx
$("#btn").click(todoModel.reload);    //undefined
```

factory function을 사용할 때는 이러한 문제는 없다. 왜냐하면 this를 사용하지 않기 때문이다.

```jsx
function TodoModel(){
		const todos = [];

		function reload(){
				setTimeout(function log(){
						console.log(todos); //[]
				}, 0);
		}
}

todoModel.reload(); //[]
$("#btn").click(todoModel.reload); //[]
```

### this and arrow function

화살표 함수는 부분적으로 클래스에서 this를 잃어버리는 문제를 해결해준다. 그러나 새로운 문제를 만든다.

- this는 더 이상 중복함수에서 컨텍스트를 잃어버리지 않는다.
- this는 method가 콜백으로 사용될 때 콘텍스트를 잃어버린다.
- 화살표 함수는 익명의 함수에 사용을 촉진한다.

화살표함수를 이용해 TodoModel을 리팩토링 해보겠다. 화살표 함수로 리팩토링 하는 과정에서 우리는 매우 중요한 가독성을 놓칠 수 있다.

```jsx
setTimeout(function renderTodosForReview(){
	//code
},0);

setTimeout(() => {
	//code
},0);
```
