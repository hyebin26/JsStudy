## DOM이란 무엇일까?

Document Object Model 혹은 DOM은 페이지에 인터페이스이다. DOM은 기본적으로 페이지에 대한 API로 프로그램들이 페이지의 내용, 구조, 스타일을 읽고 조작할 수 있게 한다.

### 어떻게 웹페이지가 만들어질까?

HTML 소스로부터 브라우저가 스타일과 상호작용 페이지를 어떻게 표현하는 지를 "Critical Rendering Path"라고 부른다. 비록 이 과정에서 몇 개의 순서가 생략될 수 있지만 , 이러한 과정들은 크게 두가지 순서로 그룹화 할 수 있다. 첫 번째 순서는 페이지에 렌더링 되는 지 결정하는 것과 관련이 있고 두 번째는 렌더링을 실행하는 것과 관련이 있다.

첫 번째 순서는 "Render tree"라고 불리며, 페이지에 렌더될 HTML elements 와 elements 들과 연관된 스타일들에 표현이다. 이 tree를 만들기 위해서는, 부라우저는 두 가지 것을 필요로 한다.

1. CSSOM :element와 연관된 스타일에 대한 표현
2. DOM :  element에 대한 표현

### 어떻게 DOM이 만들어지고, 어떻게 생겼을까?

DOM은 HTML document소스에 대한 object-based 표현이다.하지만 DOM은 HTML 소스와는 약간의 차이가 있다.

DOM에 객체 구조는 "node tree"라고 불린다. 왜냐하면 이것은 하나의  줄기를 가지고 나뭇가지, 나무잎이 뻗어가는 것처럼 보이기 때문이다.

이것은 HTML document의 예이다.

```jsx
<head>
   <title>My first web page</title>
  </head>
 <body>
    <h1>Hello, world!</h1>
    <p>How are you?</p>
  </body>
</html>
```

### 그렇다면 DOM이 아닌 것은?

DOM은 HTML document를 일대일로 상대하는 것처럼 그리고 우리가 우리의 Devtools를 보는 것처럼 보인다. 그러나 언급한 것처럼, 차이점이 있다. DOM을 이해하기 위해서, 우리는 DOM이 아닌 것을 확인 할 필요가 있다.

비록 DOM이 HTML document 의 소스로 부터 만들어졌을 지라도, 이것은 정확하게 똑같지 않다. HTML 소스로부터 DOM이 달라질 수 있는 두 가지의 사례가 있다.

1.HTML이 유효하지 않을 경우 

DOM은 유효한 HTML document의 인터페이스이다. DOM이 만들어지는 동안, 브라우저는 HTML코드에 무효한 점을 바로 잡을 수도 있다.

예제를 보면,

```jsx
<html>
Hello, world!
</html>
```

이 document는 유효한 HTML에 필수요소인 head와 body를 가지고 있지 않다. 만약 우리가 결과를 DOM tree에 결과를 본다면, 우리는 head와 body가 존재하는 알맞게 되어진 결과를 볼 수 있을 것이다.

2.DOM이 자바스크립트에 의해 정의된 경우 

```jsx
const newParagraph = document.createElement("p");
const paragrapghContent = document.createTextNode("I'm new!");
newParagraph.appendChild(paragraphContent);
document.body.appendChild(newParagraph);
```

위의 예제는 추가의 nodes를 자바스크립트를 사용해서 추가했다. 그럼 DOM은 업데이트가 되나 HTML document는 업데이트가 되지 않는다.

### DOM은 우리가 브라우저에서 보는 것이 아니다.

우리가 브라우저 화면에서 보는 것은 DOM과 CSSOM의 결합인 render tree이다. 

render tree는 오직 렌더링 되어지는 것에 대해서만 생각하기 때문에, 페이지에 보이지 않는 element들은 제외한다. 예를 들면 element가 display:none을 포함한 것이다.

```jsx
<head></head>
  <body>
    <h1>Hello, world!</h1>
    <p style="display: none;">How are you?</p>
  </body>
```

위의 예제에서 DOM은 P태그를 포함하지만 render tree는 p가 눈에 보이지 않기 때문에 포함하지 않는다.

### DOM은 DevTools가 아니다.

DevTools의 감시자가 우리의 브라우저에 가지고 있는 DOM과 거의 비슷한 것을 제공하기 때문에 차이는 매우 적다. 그러나 DevTools의 감시자는 DOM에는 없는 추가의 정보를 제공한다.

최고의 예는 CSS pseudo-element이다. 이것은 CSSSOM 그리고 render tree 부분에서 선택자 형태인 ::after, ::before을 사용함으로써 만들어 질 수 있다. 그러나 DOM에 기술적인 부분은 아니다. 왜냐하면 DOM은 style이 아닌 HTML document의 소스로부터 만들어지기 때문이다.

pseudo-element가 DOM에 한 부분이 아님에도 불구하고, 그들은 DevTools에서 볼 수 있다. 그러므로 pseudo-element는 자바스크립트에 타겟이 될 수 없고 DOM에 한 부분이 아니다.

### 요약

DOM은 HTML document의 인터페이스이다. 이것은 브라우저에 의해 무엇이 화면에 렌더링 될지 결정하는 것에 사용되고, 자바스크립트에 의해 내용,구조,스타일을 수정하는 데 사용된다.

비록 HTML document 와 유사하지만, DOM은 몇 가지 차이점이 존재한다.

- DOM은 항상 유효한 HTML이다.
- DOM은 자바스크립트에 의해 정의 될 수 있는 모델이다.
- DOM은 pseudo-element를 포함하지 않는다.(::after)
- DOM은 보이지 않는 element들을 포함한다.(display:none)

참고 : <a href="https://bitsofco.de/what-exactly-is-the-dom/">https://bitsofco.de/what-exactly-is-the-dom/</a>
