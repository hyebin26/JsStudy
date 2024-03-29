## Sementic tag
시멘틱 태그는 HTML5에 도입되었는데, 개발자와 브라우저에게 의미있는 태그를 제공하는 것을 의미한다. 예를 들어 div태그는 non-semantic태그이고, article, header는 semantic태그에 속한다. 시멘틱 태그는 태그만 보고 대략적으로 들어갈 내용을 유추할 수 있다는 장점이 있다. 헤더와 푸터를 설정할 때도 과거에는 div id="header"와 같이 했던 것을 header하나로 깔끔하게 정리할 수 있다.

## HTML tag
모든 HTML 문서는 <!DOCTYPE>선언으로 시작한다. HTML5의 경우 !DOCTYPE html 이런 식으로 말이다. 이 선언은 태그는 아니지만 내가 작성하려는 문서가 html의 형식을 따른다는 것을 선언하는 것으로, 브라우저가 문서를 렌더링 할 때 쿼크모드로 바뀌지 않도록 하는 것이 유일한 목적이다. 쿼크모드는 같은 코드라도 웹 브라우저마다 서로 다르게 해석하므로 전혀 다른 결과물을 보여주게 된다.

여러가지 태그가 있는데 필수 태그를 살펴보면, html, head, body등이 있다. html태그는 HTML문서의 가장 최상단에 위치한 태그이며, head태그에는 style,script,link,title 등이 들어간다. body태그에는 HTML문서의 내용이 들어간다.

meta태그를 살펴보면, <meta>태그의 charset속성은 해당 HTML문서의 문자 인코딩 방식을 명시한다. meta charset="문자셋" UTF-8은 유니코드를 위한 문자셋으로 가장 많이 사용된다.

## 쿠키 vs 세션스토리지 vs 로컬스토리지
기본적으로 쿠키와 로컬 스토리지, 세션 스토리지는 모두 브라우저에서 데이터 저장소의 역할을 하는 것들이다. 웹에서 로그인을 하기 위해서는 토큰을 발급받아 API를 호출해야 한다. 하지만 반복되는 작업을 계속 하게 되는 것이 비효율적이고, 이를 보완하기 위해 쿠키를 서버와 클라이언트에 생성해서 토큰 발급 없이 쿠키만 가지고 서버에 요청을 할 수 있게 된다. 쿠키는 저장 공간이 4KB로 작은 편인데 이러한 단점을 보완하여 만든 것이 웹 스토리지이다.

웹 스토리지는 서버에 클라이언트 데이터를 저장하지 않는다. 웹 스토리지에는 로컬 스토리지와 세션 스토리지가 있는데 로컬 스토리지는 브라우저에 정보가 계속해서 남아있는 반면, 세션 스토리지는 해당 세션이 끝나면, 즉 브라우저가 닫히면 데이터가 사라진다. 웹 스토리지는 데스크탑 기준 5~10MB의 저장 공간을 가지고 있어서 쿠키에 비해 훨씬 저장공간이 크다는 장점이 있다. 웹 스토리지는 반면 HTML5부터 지원하기 때문에 이전 브라우저에서는 지원이 되지 않는다는 단점이 있다.

## 다국어 페이지 제공 
이용자가 우리에게 제공하는 최소한의 정보로 그들의 국가를 파악하고, 그 국가에 맞는 언어로 페이지를 보여줘야 한다. 그러기 위해서는 첫 번째로 이용자의 국가를 판별하는 것, 두 번째는 다국어로 된 컨텐츠를 체계적으로 제공하는 방법이 있을 것이다.

1. 이용자의 국가를 판별하는 방법
가장 일반적인 방법은 HTTP요처어 헤더의 Accept-Language정보 또는 IP로 지역을 판단하는 것으로, IP로 지역을 판단하는 경우 신뢰도가 낮고 구현하기 어렵다.

2. 다국어 컨텐트를 체계적으로 제공하는 방법
- 웹접근성을 위해 HTML의 속성 중 lang에는 해당하는 언어에 맞는 값을 집어넣어야 한다. 예를 들면 한국어는 ko, 영어는 en을 넣어야한 한다. 이러한 설정은 시각장애인들에게 웹화면을 읽어주는 웹 리더가 올바르게 동작할 수 있도록 돕기도 한다.

3. i18n
다국어 컨텐츠를 언어별로 제공할 때는 i18n으로 제공하는 것이 일반적이라고 생각한다. 각 언어별 json 파일(ko.json, en.json, jp.json과 같은 파일을 구성하고 i18n 라이브러리를 이용하여 언어별 페이지를 제공한다.

## 참고
https://devowen.com/271?category=778540
