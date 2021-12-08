## 1. webpack

우선 웹팩이 나오게 된 배경부터 살펴보면, 자바스크립트를 사용한 웹 서비스 어플리케이션의 복잡도가 증가하면서, 자바스크립트 코드의 양이 많아지고, 이러한 코드를 유지 보수하기 쉽게 만들려고 모듈로 관리하는 방법이 필요하게 되었다.  ES2015에서 자바스크립트는 표준 모듈 시스템을 제안했다. 우리가 자주 사용하는 export/import방식이다.

하지만 모든 브라우저가 ES2015방식의 모듈 시스템을 지원하지 않는다. 크롬의 경우 버전61부터 모듈 시스템을 지원한다. 그래서 개발자들은 브라우저와 버전에 상관없이 이러한 편리한 모듈 시스템을 사용하기를 원했고, 이러한 필요에 의해 나온 툴이 웹팩(webpack)이다.

**웹팩은 여러 파일을 하나로 합쳐주는 자바스크립트 번들러이다.** 하나의 entry point에서 시작하여 의존하는 모듈을 찾아내고, 하나의 결과물로 만들어 낸다. 웹팩은 모듈 시스템을 구성하는 기능 이외에도 컴파일 속도를 빠르게 해준다거나, 로더를 사용할 수 있다는 장점을 가지고 있다.

이러한 설정들은 보통 webpack.config.js파일에서 하나의 설정파일로 관리를 한다.

```jsx
module.exports = {
  mode: "development",
  entry: {
    login: "./src/javascript/login.js",
    signup: "./src/javascript/signup.js",
    edit: [
      "./src/javascript/edit_review.js",
      "./src/javascript/user.js",
      "./src/javascript/image_uploader.js",
    ],
    add: [
      "./src/javascript/add_review.js",
      "./src/javascript/user.js",
      "./src/javascript/image_uploader.js",
    ],
    category: ["./src/javascript/user.js", "./src/javascript/category.js"],
    index: ["./src/javascript/user.js", "./src/javascript/index.js"],
    profile: ["./src/javascript/user.js", "./src/javascript/profile.js"],
    update: [
      "./src/javascript/user.js",
    "./src/javascript/update.js",
      "./src/javascript/comment.js",
    ],
    search: ["./src/javascript/user.js", "./src/javascript/search.js"],
  },
  output: {
    path: __dirname + "/src/dist",
    filename: "[name].js",
  },
};
```

위의 코드를 보면, entry point가 여러개가 있으며, 해당 경로에 있는 value가 key값으로 번들링 된 파일로 출력되며(ex,. login.js, add.js) 그 위치는 /src/dist인 것을 확인할 수 있다.

웹팩은 모든 파일을 로더로 바라본다. JS로 만든 모듈 뿐만 아니라 css. 이미지. 폰트 등 전부 모듈로 보기 때문에 import를 통해 JS코드 안으로 가져올 수 있다. 이를 가능하게 하는 것이 로더이다. **로더는 다양한 리소스를 JS에서 바로 사용할 수 있는 형태로 로딩하는 기능이다.(ex. 타입스크립트 같은 언어를 JS 문법 변환 등)**

```jsx
module.exports = {
  module: {
    rules: [{
      test: /\.css$/, // .css 확장자로 끝나는 모든 파일
      use: ['style-loader', 'css-loader'],
    },
    {
      test: /\.png$/, // .png 확장자로 마치는 모든 파일
      loader: 'file-loader',
      options: {
        publicPath: './dist/', // prefix를 아웃풋 경로로 지정 
        name: '[name].[ext]?[hash]', // 파일명 형식 
      }
    }]
  }
}
```

웹팩에서 알아야 하는 또 다른 개념은 플러그인이다. 로더는 파일을 해석, 변환 하는 과정에 참여하여 모듈을 처리하는 반면, **플로그인은 번들된 결과물을 처리하여 기능을 추가하고 싶을 때 사용한다.**

플러그인은 직접 만들 수 있지만, 기본 플러그인과 서드 파티 라이브러리를 사용할 수 있따.

- BannerPlugin: 기본 플러그인으로, 결과물에 빌드 정보나 커밋 버전같은 내용들을 추가할 수 있다.
- DefinePlugin: 기본 플러그인으로, 여러 환경에서(개발/운영)할 때 환경에 따라 API주소가 다를 수 있는데 이러한 환경 의존적인 정보를 관리하기 위한 플러그인이다.
- CleanWebpackPlugin : 서드 파티 라이브러리로, 빌드 이전 결과물을 제거하는 플러그인이다.
## Babel

먼저, 배경은 브라우저 혹은 플랫폼마다 보여지는 모습이 다른 경우가 빈번했다. 일부 최신 브라우저에서만 사용 가능한 것을 그렇지 않은 브라우저에서 구현할 경우, 기능을 단순화하거나 생략해야 하는 경우도 발생하고 코드의 일관성도 망가트리고 혼란스럽게 만드는 요소중에 하나이다.**(크로스 브라우징)** 이러한 차이를 최소화하여, 웹 서비스를 사용할 수 있게 하기 위해 바벨이 나오게 되었다.

**바벨은 같은 언어를 다른 실행 환경에서도 실행할 수 있도록 형태만 바꾸는 작업을 진행한다.이와 같은 작업을 하는 프로그램을 트랜스 파일러라고 한다.**

바벨은 주로 세 단계로 빌드를 진행한다.

1. 파싱: 코드를 읽고 추상 구문 트리로 변환하는 단계
2. 변환: 추상 구문트리로 변경
3. 출력: 변경된 결과물을 출력

파싱과 출력은 바벨이 담당하나, 변환은 플러그인이 담당하게 된다.

**플러그인은 바벨이 어떤 코드를 어떻게 변환할지에 대한 규칙을 나타낸다.** 플러그인은 직접 만들어도 되고 잘 만들어진 플러그인을 사용해도 된다.

npm 패키지로 제공하는 플러그인을 설치하여 es6의 const, let을 var로 바꾸고, 화살표 함수를 일반함수로 바꾸고 엄격모드를 적용하여 ES6+를 ES5로 변환하는 설정은 다음과 같다.

```jsx
module.exports = {
  plugins: [
    "@babel/plugin-transform-block-scoping",
    "@babel/plugin-transform-arrow-functions",
    "@babel/plugin-transform-strict-mode", 
  ]
}
```

 플러그인을 매번 설정하는 것은 번거롭다. 그래서 이렇게 **필요한 플러그인들을 목적에 따라 세트로 묶어 놓는 경우가 많다. 이러한 세트를 프리셋**이라고 한다.

대표적인 프리셋 ES6+를 변환하는 프리셋인 preset-env를 확인해보자.

```jsx
module.exports = {
  presets: [
    '@babel/preset-env'
  ]
}
```

다음으로, 변환과는 조금 다른 폴리필에 대해서 알아보면, **폴리필은 최신 ECMAscript환경을 만들기 위해 코드가 실행되는 환경에 존재하지 않는 빌트인, 메소드 등을 추가하는 역할**을 한다.

ES6에서 비동기 처리를 위해 등장한 Promise 객체는 env 프리셋을 가지고 변환을 하려고 해도 ie에서 인식하지 못한다. 바벨의 경우는 ES6+를 ES5로 변환할 수 있는 것들만 변환을 하는데, Promise와 같이 ES5에서 변환할 수 있는 대상이 없는 경우는 에러가 발생한다. 이러한 경우 우리는 폴리필을 통해서 이슈를 해결할 수 있다. Promise를 ES5로 변환할 수는 없지만 ES5 방식으로 구현하여 해결하는 것이다.

실무에서는 바벨을 직접 사용하는 경우보다는 웹팩으로 통합해서 사용하는 경우가 더 많다. 이 때 로더 형태로 제공되는 babel-loader를 사용한다.

```jsx
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader', // 바벨 로더를 추가한다 
      },
    ]
  },
}
```

## 참고

[https://devowen.com/293?category=778540](https://devowen.com/293?category=778540)							
