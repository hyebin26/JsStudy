## Typescript 설치 및 webpack, babel 셋팅

### Typescript 설치

```tsx
npm install --save-dev typescript ts-loader
```

우선 typescript와 ts-loader를 다운 받습니다. 

ts-loader를 다운 받는 이유는 타입스크립트는 TSC를 통해서 Javascript로 트랜스파일이 되는데, 웹팩에서는 TSC를 인식하지 못하므로, TSC와 대화를 할 수 있는 ts-loader가 필요합니다. 웹팩은 ts-loader패키지로 tsc에게 컴파일을 요청해서 Typescript 파일들을 컴파일합니다.

Source Map은 개발하는 코드와 번들링된 코드 사이의 관계를 표현하는 데이터입니다. Source Map을 사용하지 않으면 브라우저에서 디버깅할 때 번들링된 코드 즉 bundle.js파일을 디버깅해야 됩니다. 번들링된 코드를 디버깅하는 것은 비효율적이기 때문에 Source Map이 등장하였습니다. 

### 웹팩과 바벨이 왜 필요할까?

우선 웹팩은 자바스크립트 어플리케이션을 위한 정적 모듈 번들러입니다. 자바스크립트 앱을 처리할 때 웹팩은 내부에서 하나 혹은 여러 개의 entry point로 부터 dependency graph를 빌드하고 프로젝트가 필요로하는 모든 모듈을 결합합니다. 

Webpack은 모든 파일을 재귀적으로 탐색하고 종속성을 식별합니다. 그 후 축소된 출력 파일을 생성하고 불필요한 자산을 제거합니다. 전체 프로세스는 Vanilla JS 프로젝트를 유지 관리하기 쉽고 효율적으로 만듭니다.

- Dependency graph는 어플리케이션에서 필요로 하는 모든 모듈을 포함한다.

바벨은 구문 변화기이자 트랜스파일러입니다. 코드가 이전 브라우저를 포함하여 모든 브라우저 및 환경과 호환되기를 원하는 경우에 사용됩니다. Babel은 최신 JavaScript 코드(ES6+)를 이전 JavaScript 코드(ES5)로 변환합니다. 이렇게 하면 최신 JavaScript 버전을 사용하여 코딩하는 경우에도 모든 브라우저와 코드가 호환됩니다.

바벨은 자바스크립트 컨파일러로, 오래된 브라우저나 환경에 현재의 자바스크립트 버전을 호환가능하게 ES5로 변환하기 위해 주로 사용됩니다. 바벨이 주로 하는 일은 

1. 문법을 변환한다.
2. Source code 변형
3. Polyfill 특징 ⇒ 브라우저에서 지원하지 않는 코드를 사용가능한 코드조각이나 플러그인을 의미한다. 바벨은 ES6의 Map, Promise, Set등은 존재하지 않으므로, Polyfill의 필요합니다.

### Babel

![0005](https://user-images.githubusercontent.com/67263146/177194208-9fcbac6f-5a95-4694-9035-7a13ef59603c.png)

바벨은 컴파일러로, 컴파일되는 과정은 크게 3가지로 나눠집니다.

1. 파싱(Parsing)

바벨은 소스 코드를 가지고 추상적인 형태의 코드로 변환하는 과정을 수행하는데, 이를 바벨에선 파싱이라고 표현하며, 이 추상적인 형태의 코드는 추상구문트로(Abstract Syntax Tree)라고 부릅니다. 추상구문트리는 추상화된 코드의 표현체로, 소스코드의 좀 더 원활한 변환을 위해 작성되는 표현체입니다. 

바벨의 플러그인 중 babel-parser라고 불리는 플러그인이 있는데, 또 다른 이름으로는 bablyon입니다. 추상 구문트리는 바빌론에 의해 작성됩니다. 

1. 변환(Transformation)

바벨은 1단계에서 파싱된 추상구문트리를 받아와 각 브라우저의 환경에 맞는 결과로 반환합니다. 바벨의 플러그인 중 preset/plugin에 의해 처리되는 곳으로, preset은 plugin들을 모아놓은 배열입니다.

이 플러그인들은 babel-traverse를 이용해 원본 추상구문트리를 가로질러가는 과정속에서 각 부분들이 어떻게 정의되어야 하는지를 기록합니다. 추상구문트리는 2단계에 접어들면서 babel-traverse에 의해 재작성 됩니다. 이 과정에서 브라우저 호환 환경에 맞게 적절한 형태로 변환될 수 있도록 안내 가이드같은 것들이 적히는 변형 과정을 거치게되고, 이렇게 일련의 변형을 마치면 새로운 추상구문트리로 바뀌게 됩니다. 

1. 코드 생성Generation)

3단계에서는 2단계에서 생성된 새로운 추상구문트리를 바탕으로 실제 브라우저 환경에 맞는 소스코드로 변환하는 과정이 이뤄집니다. 2단계에서 만들어진 추상구문트리는 각 브라우저에서 코드 구문이 어떻게 바뀌어야 하는지에 대한 내용을 기술하지만, 이렇게 기술만 하는 단계가 2단계라면, 실제로 이 내용을 바탕으로 코드를 생성하는 곳이 3단계입니다. 이 과정은 babel-generator에 의해 이뤄집니다.

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "edge": "17",
          "firefox": "60",
          "chrome": "67",
          "safari": "11.1"
        }
      }
    ],
    "@babel/preset-typescript"
  ],
  "plugins": [
    [
      "@babel/plugin-transform-runtime",
      {
        "corejs": 3,
        "proposals": true
      }
    ]
  ]
}
```

### Webpack

```jsx
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  mode: "development",
  resolve: {
    extensions: [".ts", ".js"],
  },
  module: {
    rules: [
      {
        test: /\.ts$/,
        exclude: /node_modules/,
        use: ["babel-loader", "ts-loader"],
      },
      {
        test: /\.(jpg|png|gif|svg)$/,
        use: ["file-loader"],
      },
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "public"),
  },
  plugins: [new HtmlWebpackPlugin({ template: "./public/index.html" })],
};
```
