### CRA를 사용하는 이유

페이스북에서 개발안 Create-React-App 보일러 플레이트를 사용하면 간편하게 리액트 프로젝트를 시작할 수 있습니다. CRA는 리액트 프로젝트를 처음 실행할 때 필요한 여러가지 라이브러리나 웹팩의 설정없이 간편하게 프로젝트를 시작할 수 있습니다. CRA가 가지는 장점은 여러가지가 있습니다.

- 단 하나의 one build dependency를 가지게 되므로 React 프로젝트를 구성할 때 필요한 Webpack, Babel, ESLint등 간의 연결에 대해서 신경쓰지 않아도 됩니다
- 위에서 나열한 Webpack, Babel, ESLint는 처음 프로젝트를 구성할 때 반드시 설정이 필요한 패키지들입니다. 이를 처음 설정할 때 상당한 시간이 소요될 수 있는데, CRA는 프로젝트에 필요한 필수적인 설정을 대신해줍니다.
- CRA는 Autoprefixer를 지원해줍니다. 즉, 일반적인 CSS코드 생성을 하게되면 자동으로 -webkit-, -ms-등을 자동으로 적용해줍니다.

### Eject에 대해서

Eject는 one build dependency를 가진 프로젝트를 custom하기 위한 방법입니다. 

```jsx
yarn eject
```

해당 명령어를 입력하면, 숨겨져 있던 모든 설정들(All configurations, webpack, babel 등)과 패키지들이 가지는 의존성을 볼 수 있게 됩니다.

### Eject를 쓰면 안되는 이유

eject는 우리의 프로젝트를 자유롭게 customizing한다는 점에서 매력적으로 보입니다. 하지만 몇 가지 CRA를 쓰면서 갖게 되는 이점을 포기하게 됩니다..

- CRA는 모든 configuration을 직접 유지보수해야 됩니다. Webpack, Babel등등 이외에도 프로젝트를 진행하면서 컴포넌트, 테스트 등 많은 작업을 해야됩니다. 게다가 협업을 하는 상황이라면 같이 일하는 개발자들도 같이 Webpack, Babel에 상당히 익숙해져야 합니다.
- One Build Dependency의 장점을 잃게 됩니다. 작업 도중 하나의 패키지가 필요해서 설치한다거나, 삭제할 떄, 항상 다른 패키지들과 의존성을 신경써야 합니다.
- 다시 예전상태로 돌아갈 수 없습니다. 또한 프로젝트를 많은 파일로 더럽힐 수 있고 CRA에서 제공하는 추상화를 방해합니다.

### React-app-rewired

`react-app-rewired`를 사용하면 `eject`를 사용하지 않고 CRA프로젝트에 customizing을 할 수 있습니다. `config-overrides.js` 파일을 통해 ESLint, Jest, Typescript같은 패키지들의 configuration을 수정할 수 있습니다.

```jsx
// config-overrides.js
module.exports = {
  webpack: function (config, env) {
    return config;
  },
  jest: function (config) {
    return config;
  },
  devServer: function (configFunction) {
    return function (proxy, allowedHost) {
      return config;
    };
  },
  paths: function (paths, env) {
    return paths;
  },
}
```

> Overriding: 부모 클래스로부터 상속받은 메소드의 내용을 변경하는 것을 말합니다.
>
