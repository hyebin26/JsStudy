## Context API 
Context를 이용하면 단계마다 일일이 props를 넘겨주지 않고도 컴포넌트 트리 전체에 데이터를 제공할 수 있습니다.

앱 안의 여러 컴포넌트들에 전달해줘야 하는 props의 경우(예를 들면 선호 로케일, UI테마 등)이 과정이 번거로울 수 있습니다. context를 이용하면, 트리 단계마다 명시적으로 props를 넘겨주지 않아도 많은 컴포넌트가 이러한 값을 공유하도록 할 수 있습니다

### 언제 context를 써야 할까

context는 **React컴포넌트 트리 안에서 전역적이라고 볼 수 있는 데이터를 공유할 수 있도록 고안된 방법**입니다. 예를 들어

```jsx
class App extends React.Component {
  render() {
    return <Toolbar theme="dark" />;
  }
}

function Toolbar(props) {
  // Toolbar 컴포넌트는 불필요한 테마 prop를 받아서
  // ThemeButton에 전달해야 합니다.
  // 앱 안의 모든 버튼이 테마를 알아야 한다면
  // 이 정보를 일일이 넘기는 과정은 매우 곤혹스러울 수 있습니다.
  return (
    <div>
      <ThemedButton theme={props.theme} />
    </div>
  );
}

class ThemedButton extends React.Component {
  render() {
    return <Button theme={this.props.theme} />;
  }
}
```

context를 사용하면 중간에 있는 엘리먼트들에게 props를 넘겨주지 않아도 됩니다.

```jsx
// context를 사용하면 모든 컴포넌트를 일일이 통하지 않고도
// 원하는 값을 컴포넌트 트리 깊숙한 곳까지 보낼 수 있습니다.
// light를 기본값으로 하는 테마 context를 만들어 봅시다.
const ThemeContext = React.createContext('light');

class App extends React.Component {
  render() {
    // Provider를 이용해 하위 트리에 테마 값을 보내줍니다.
    // 아무리 깊숙히 있어도, 모든 컴포넌트가 이 값을 읽을 수 있습니다.
    // 아래 예시에서는 dark를 현재 선택된 테마 값으로 보내고 있습니다.
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 이젠 중간에 있는 컴포넌트가 일일이 테마를 넘겨줄 필요가 없습니다.
function Toolbar() {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 현재 선택된 테마 값을 읽기 위해 contextType을 지정합니다.
  // React는 가장 가까이 있는 테마 Provider를 찾아 그 값을 사용할 것입니다.
  // 이 예시에서 현재 선택된 테마는 dark입니다.
  static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;
  }
}
```

### 고려해야 할 점

context의 주된 용도는 다양한 레벨에 중첩된 많은 컴포넌트에게 데이터를 전달하는 것입니다. context를 사용하면 컴포넌트를 재사용하기가 어려워지므로 꼭 필요할 때만 사용해야 합니다.

여러 레벨에 걸쳐 props를 넘기는 걸 대체하는 데에 context보다 컴포넌트 합성이 더 간단한 해결책일 수도 있습니다. 예를 들어

```jsx
<Page user={user} avatarSize={avatarSize} />
// ... 그 아래에 ...
<PageLayout user={user} avatarSize={avatarSize} />
// ... 그 아래에 ...
<NavigationBar user={user} avatarSize={avatarSize} />
// ... 그 아래에 ...
<Link href={user.permalink}>
  <Avatar user={user} size={avatarSize} />
</Link>
```

실제로 사용되는 것은 `Avatar` 컴포넌트 뿐인데 `user`와 `avartarSize` props를 여러 단계에 걸쳐 보내줘야 한다는 게 번거로워 보일 수 있습니다. 게다가 위에서 `Avatar` 컴포넌트로 보내줘야 하는 props가 추가된다면 그 또한 중간 레벨에 모두 추가해줘야 합니다.

`Avatar` 컴포넌트 자체를 넘겨주면 context를 사용하지 않고 이를 해결할 수 있습니다. 그러면 중간에 있는 컴포넌트들이 `user`나 `avartarSize`에 대해 전혀 알 필요가 없습니다.

```jsx
function Page(props) {
  const user = props.user;
  const userLink = (
    <Link href={user.permalink}>
      <Avatar user={user} size={props.avatarSize} />
    </Link>
  );
  return <PageLayout userLink={userLink} />;
}

// 이제 이렇게 쓸 수 있습니다.
<Page user={user} avatarSize={avatarSize} />
// ... 그 아래에 ...
<PageLayout userLink={...} />
// ... 그 아래에 ...
<NavigationBar userLink={...} />
// ... 그 아래에 ...
{props.userLink}
```

이러한 제어의 역전을 이용하면 넘겨줘야 하는 props의 수는 줄고 최상위 컴포넌트의 제어력은 더 커지기 때문에 더 깔끔한 코드를 쓸 수 있는 경우가 많습니다. 하지만 이러한 역전이 항상 옳은 것은 아닙니다. **복잡한 로직을 상위로 옮기면 이 상위 컴포넌트들은 더 난해해지기 마련이고 하위 컴포넌트들은 필요 이상으로 유연해져야 합니다**.

```jsx
function Page(props) {
  const user = props.user;
  const content = <Feed user={user} />;
  const topBar = (
    <NavigationBar>
      <Link href={user.permalink}>
        <Avatar user={user} size={props.avatarSize} />
      </Link>
    </NavigationBar>
  );
  return (
    <PageLayout
      topBar={topBar}
      content={content}
    />
  );
}
```

이 패턴을 사용하면 자식 컴포넌트와 직속 부모를 분리(decouple)하는 문제는 대게 해결할 수 있습니다. 더 나아가 render props를 이용하면 렌더링되기 전부터 자식 컴포넌트가 부모 컴포넌트와 소통하게 할 수 있습니다. 

하지만 같은 **데이터를 트리 안 여러 레벨이 있는 많은 컴포넌트에 주어야 할 때**도 있습니다. 이런 **데이터 값이 변할 때마다 모든 하위 컴포넌트에게 널리 “방송”하는 것이 context**입니다. 흔히 예시로 드는 선호 로케일, 테마, 데이터 캐시 등을 관리하는 데 있어서는 일반적으로 context를 사용하는 것이 가장 편리합니다.

## API

### React.createContext

```jsx
const MyContext = React.createContext(defaultValue);
```

Context 객체를 만듭니다. Context 객체를 구독하고 있는 컴포넌트를 렌더링할 때 React는 트리 상위에서 가장 가까이 있는 짝이 맞는 `Provider` 로부터 현재값을 읽습니다.

`defaultValue` 는 **트리 안에서 적절한 Provider를 찾지 못할 때**만 쓰이는 값입니다.

### Context.Provider

```jsx
<MyContext.Provider value={/* value */}>
```

Context오브젝트에 포함된 React 컴포넌트인 Provider는 context를 구독하는 컴포넌트들에게 context의 변화를 알리는 역할을 합니다.

Provider 컴포넌트는 value prop을 받아서 이 값을 하위에 있는 컴포넌트들에게 전달합니다. 값을 전달받을 수 있는 컴포넌트의 수에 제한은 없습니다. Provider 하위에 또 다른 Provider를 배치하는 것도 가능하며, 이 경우 하위 Provider의 값이 우선시 됩니다.

Provider하위에서 context를 구독하는 모든 컴포넌트는 Provider의 value prop가 바뀔 때 마다 다시 렌더링됩니다.

### Context.Consumer

```jsx
<MyContext.Consumer>
	{value => /* context값을 이용한 렌더링 */}
</MyContext.Consumer>
```

context 변화를 구독하는 React컴포넌트입니다. 이 컴포넌트를 사용하면 함수 컴포넌트안에서 context를 구독할 수 있습니다.

Context.Consumer의 자식은 함수여야 합니다. 이 함수는 context의 현재값을 받고 React노드를 반환하빈다. 이 함수가 받는 `value` 매개변수 값은 해당 context의 Provider중 상위 트리에서 가장 가까운 Provider의 value props과 동일합니다. 상위에 Provider가 없다면 value 매개변수와 같은 `createContext()` 에 보냈던 `defaultValue` 와 동일할 것입니다.

### Context.displayName

Context 객체는 `displayName`문자열 속성을 설정할 수 있습니다. React 개발자 도구는 이 문자열을 사용해서 context를 어떻게 보여줄 지 결정합니다. 

예를 들어, 아래 컴포넌트는 개발자 도구에 MyDiplayName로 표시됩니다.

```jsx
const MyContext = React.createContext(/* some value */);
MyContext.displayName = 'MyDisplayName';

<MyContext.Provider> // "MyDisplayName.Provider" in DevTools
<MyContext.Consumer> // "MyDisplayName.Consumer" in DevTools
```
