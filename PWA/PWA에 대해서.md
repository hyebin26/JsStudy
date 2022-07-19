# PWA 사용해보기

npx create-react-app pwaa --template cra-template-pwa-typescript로 pwa와 typescript가 셋팅된 템플릿을 생성할 수 있다.

해당 템플릿을 통해서 Progressive Web App을 생성에 필요한 툴을 설치할 수 있으나 the offline/cache-first behavior은 opt-in이다.

템플릿을 시작하면 설치되는 `service-worker.js`파일로 서비스워커를 컴파일하고 precache를 URL에 주입하는 Workbox에 `InjectManifest` 플러그인을 빌트인으로 사용할 수 있다.

  

사용자가 offline-first behavior를 사용하기 위해서는 `index.js`를 수정해야 한다.

```jsx
// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://cra.link/PWA
serviceWorkerRegistration.unregister();
```

`unregister()`를 `register()`로 바꾸면 서비스워커에서 opt를 사용할 수 있다. 

### Opt-in이란?

Offline-first Progressive Web Apps는 전통적인 웹보다 안정적이고 빠르고 모바일 웹 경험을 제공한다.

- 모든 정적인 파일들이 캐시되기 때문에, 네트워크 연결에 상관없이 후속 방문에 페이지 로드 속도가 빠르다.
- 네트워크 속도가 오프라인일지라도, 네트워크 상태에도 불구하고 효과가 있을 것이다. 이것은 유저들이 지하철 등 인터넷이 잘 터지지 않는 곳에서도 앱을 사용할 수 있도록 한다.
- 모바일 디바이스에서, 너의 앱은 앱 아이콘 혹은 모든 것으로 홈 스크린에서 직접적으로 추가할 수 있다.

그러나 배포 디버깅을 더 어렵게 만들 수 있습니다.

 `workbox-webpack-plugin` 은 프로덕션 config에 통합되어 생성된 모든 assets을 자동으로 미리 캐시하고 업데이트를 배포할 때 최신 상태로 유지하는 서비스 워커파일을 컴파일합니다. 서비스 워커는 HTML에 대한 탐색 요청을 포함하여 webpack에 의해 생성된 assets에 대한 모든 요청을 처리하기 위해 cache-first 전략을 사용하여 느리거나 신뢰할 수 없는 네트워크에서도 웹 앱이 일괸되게 빠르도록 합니다. 

## Cache-first vs Network-first

이 두가지는 서비스워커에서 사용하는 캐싱 전략으로, `Workbox`와 함께 제공되는 전략들로, `Workbox`의 내부에서 어떤 일이 일어나는지 이해할 수 있다. 

### 1. Cache-first

[zc7Es0hAWpIHDFQOr9e2.avif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ee2a77e8-c2a9-4829-a264-1efc999ec334/zc7Es0hAWpIHDFQOr9e2.avif)

이 과정에서 들어오는 요청은 밑의 순서로 진행된다.

1. 모든 요청은 캐쉬된다. 만약에 asset가 캐시에 있다면 제공한다.
2. 만약에 요청이 캐시에 없다면 네트워크로 이동한다.
3. 네트워크에 요청이 끝난 후에 캐시에 추가하고 네트워크로부터 응답을 반환한다.

HTTP 캐시가 시작될 수 있는 서버에서 콘텐츠 최신성 검사를 건너뛰어 변경 불가능한 자산에 대한 속도 향상을 제공합니다**.** 중요한 점은, 모든 캐시된 assets들은 오프라인에서 이용할 수 있다.

이것은 캐시안에 있는 것들을 위해 “cache only” behavior을 제공하고 캐시되지 않은 모든 것들을 위해 “network only” behavior을 제공한다.

### 2. Network-first 전략

[b9msVkRsi6uLLu3bXT6t.avif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/72998914-b1c4-410a-8fef-1fabf6d9e8f3/b9msVkRsi6uLLu3bXT6t.avif)

우선, Network-first의 캐싱 전략의 순서부터 알아보면,

1. 요청을 위해 먼저 네트워크로 이동하고 응답을 캐시에 저장한다.
2. 만약에 너가 나중에 오프라인 상태가 된다면, 캐시안에서 가장 최신의 버전으로 대체한다.

즉, 네트워크가 응답되지 않는 상황일 경우 캐시되어 있는 응답을 사용하는 것이다. 이 전략은 온라인 상태에서 리소스의 최신 버전을 원하지만 사용 가능한 최신 버전에 대한 오프라인 액세스를 제공하려는 HTML 또는 API요청에 유용하다.

즉, 온라인 사용자에게는 최신 콘텐츠를 제공하지만 오프라인 사용자에게는 캐시된 이전의 버전을 제공한다. 하지만 이 방법에는 결함이 존재한다. 사용자의 인터넷 속도가 느린 경우 완벽하게 수용가능한 컨텐츠를 가져오는 동안 기다려야 한다. 즉, 네트워크가 실패할 때 까지 기다려야 한다. 사용자의 네트워크 속도가 느린 경우 매우 오랜시간이 걸릴 수 있으며 실망스러운 사용자 경험을 유발할 수 있다.

오프라인 기능이 중요하지만 해당 기능과 최신 버전의 마크업 또는 API데이터에 대한 액세스의 균형을 맞춰야 하는 상황에서 “Network-first, cache second”전략은 해당 목표를 확실하게 달성하는 전략이다.

### Offline-First 고려사항들
...작성중 
