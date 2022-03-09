## Suspense

Suspense를 사용하면 컴포넌트가 렌더링되기 전까지 기다릴 수 있습니다.

Suspense는 데이터 불러오기 라이브러리가 아닙니다. **Suspense는 컴포넌트가 읽어들이고 있는 데이터가 아직 준비되지 않았다고 React에 알려줄 수 있는**, 데이터 불러오기 라이브러리에서 사용할 수 있는 메커니즘입니다.

장기적인 관점으로는 Suspense가 데이터 출처와 상관없이 컴포넌트로부터 비동기 데이터를 읽는 데에 주된 방식으로 거듭나길 바라고 있습니다.

### Suspense를 사용하는 이유

- **데이터 불러오기 라이브러리들이 React와 깊게 결합할 수 있도록 해줍니다.** 데이터 불러오기 라이브러리가 Suspense지원을 구현한다면 React컴포넌트에서 이를 사용하는 것이 아주 자연스럽게 느껴질 것입니다.
- **의도적으로 설계된 로딩 상태를 조정할 수 있도록 해줍니다.** Suspense는 데이터가 *어떻게* 불러져야 하는지를 정하지 않고, 앱의 시각적인 로딩 단계를 밀접하게 통제할 수 있도록 해줍니다.
- **경쟁 상태(Race Condition)를 피할 수 있도록 돕습니다.** `await`를 사용하더라도 비동기 코드는 종종 오류가 발생하기 쉽습니다. Suspense를 사용하면 데이터를 *동기적으로* 읽어오는 것처럼 느껴지게 해줍니다. 마치 이미 불러오기가 완료된 것처럼 말입니다.

## 기존의 접근 방식 vs Suspense

### 접근방식1: 렌더링 직후 불러오기(Suspense 미사용)

리액트에서 데이터를 불러오는 가장 흔한 방식은 Effect를 사용하는 것이다. 아래의 컴포넌트로 문제점을 살펴보겠습니다.

```tsx
function ProfilePage() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchUser().then(u => setUser(u)); // 데이터 불러오기
  }, []);

  if (user === null) {
    return <p>Loading profile...</p>;
  }
  return (
    <>
      <h1>{user.name}</h1>
      <ProfileTimeline />
    </>
  );
}

function ProfileTimeline() {
  const [posts, setPosts] = useState(null);

  useEffect(() => {
    fetchPosts().then(p => setPosts(p)); // 데이터불러오기
  }, []);

  if (posts === null) {
    return <h2>Loading posts...</h2>;
  }
  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.text}</li>
      ))}
    </ul>
  );
}
```

위의 코드를 실행하고 콘솔로그를 살펴보면 아래의 같은 순서를 확인할 수 있습니다.

1. 사용자 정보불러오기 시작
2. ...기다리기
3. 사용자 정보 불러오기 완료
4. 게시글 불러오기
5. ...기다리기
6. 게시글 불러오기 완료

사용자 정보 불러오기가 3초가 소요된다면 3초 후에 게시글 불러오기를 시작할 수 있다는 것입니다. 이것이 바로 **워터폴**로, 순차적으로 실행되는 현상입니다.

이를 고치는 것은 가능하지만, 앱이 거대해짐에 따라 많은 사람들은 이 문제를 방지할 수 있는 해결책을 원할 것입니다.

### 접근방식2: 불러오기 이후 렌더링(Suspense 미사용)

라이브러리는 데이터를 불러오는 데에 있어 보다 중앙화된 방식을 제공하는 것으로 워터폴을 방지할 수 있습니다.

이 페이지는 데이터를 불러오는 라이브러리를 사용하지 않으므로, 데이터 불러오기 메서드를 하나로 합쳐서 비슷한 앞서 설명한 것과 유사한 코드를 직접 작성해보겠습니다.

```tsx
function fetchProfileData() {
  return Promise.all([
    fetchUser(),
    fetchPosts()
  ]).then(([user, posts]) => {
    return {user, posts};
  })
}
```

아래의 예시에서는 `<ProfilePage>` 가 두 요청을 기다리는데, 두 요청은 동시에 시작됩니다.

```tsx
// 최대한 일찍 불러오기를 발동시킵니다
const promise = fetchProfileData();

function ProfilePage() {
  const [user, setUser] = useState(null);
  const [posts, setPosts] = useState(null);

  useEffect(() => {
    promise.then(data => {
      setUser(data.user);
      setPosts(data.posts);
    });
  }, []);

  if (user === null) {
    return <p>Loading profile...</p>;
  }
  return (
    <>
      <h1>{user.name}</h1>
      <ProfileTimeline posts={posts} />
    </>
  );
}

// 자식 컴포넌트들은 더 이상 불러오기를 발동시키지 않습니다
function ProfileTimeline({ posts }) {
  if (posts === null) {
    return <h2>Loading posts...</h2>;
  }
  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.text}</li>
      ))}
    </ul>
  );
}
```

이제 이벤트가 발생하는 순서는 다음처럼 변경됩니다.

1. 사용자 정보를 불러오기 시작
2. 게시글 불러오기 시작
3. 기다리기...
4. 사용자 정보 불러오기 완료
5. 게시글 불러오기 완료

기존에 존재하는 “워터폴”현상은 고쳤지만 의도하지 않은 문제를 만들었습니다. `fetchProfileData` 내에서 `Promise.all()` 을 사용하는 과정에서 모든 데이터가 반환되기를 기다려야 합니다. 따라서 게시글들을 모두 불러오기 전까지 프로필 정보를 렌더링할 수 없습니다. 즉, 둘 다 기다려야 합니다.

물론 `Promise.all` 을 사용하지 않고 두 프라미스를 기다라면 이러한 문제를 해결할 수 있습니다. 하지만 이러한 접근 방식은 데이터와 컴포넌트 트리의 복잡도가 커짐에 따라 점점 어려워집니다.

### 접근방식3: 불러올 때 렌더링(Suspense 사용)

직전의 접근 방식에서는 아래와 같이, `setState`를 호출하기 전에 데이터를 불러왔습니다.

1. 데이터 불러오기 시작
2. 데이터 불러오기 완료
3. 렌더링 시작(setState 호출)

Suspense를 사용하면 불러오기를 먼저 시작하면서도 아래와 같이 마지막 두 단계의 순서를 바꿔줄 수 있습니다.

1. 데이터 불러오기 시작
2. 렌더링 시작 
3. 데이터 불러오기 완료

**Suspense를 사용하면, 렌더링을 시작하기 전에 응답이 오기를 기다리지 않아도 됩니다**. 사실 네트워크 요청을 발동시키고서 아래와 같이 바로 렌더링을 발동시킵니다.

```tsx
// 이것은 프라미스가 아닙니다. Suspense 통합에서 만들어낸 특별한 객체입니다.
const resource = fetchProfileData();

function ProfilePage() {
  return (
    <Suspense fallback={<h1>Loading profile...</h1>}>
      <ProfileDetails />
      <Suspense fallback={<h1>Loading posts...</h1>}>
        <ProfileTimeline />
      </Suspense>
    </Suspense>
  );
}

function ProfileDetails() {
  // 아직 로딩이 완료되지 않았더라도, 사용자 정보 읽기를 시도합니다
  const user = resource.user.read();
  return <h1>{user.name}</h1>;
}

function ProfileTimeline() {
  // 아직 로딩이 완료되지 않았더라도, 게시글 읽기를 시도합니다
  const posts = resource.posts.read();
  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.text}</li>
      ))}
    </ul>
  );
}
```

화면 상에 `<ProfilePage>`를 렌더링할 때에 아래와 같은 일들이 벌어집니다.

1. 이미 `fetchProfileData()` 내에서 서버에 요청했습니다. 이 함수는 프라미스가 아니라 특별한 “자원”을 돌려줍니다. 보다 현실적인 예시에서는, Relay와 같은 데이터 라이브러리에서 제공하는 Suspense 통합을 제공할 것입니다.
2. React는 `<ProfilePage>` 의 렌더링을 시도합니다. 자식 컴포넌트로 `<ProfileDetails>`와 `<ProfileTimeline>`을 반환합니다.
3. React는 `<ProfileDetails>`의 렌더링을 시도합니다. `resource.user.read()`를 호출합니다. 아직 불러온 데이터가 아무 것도 없으므로, 이 컴포넌트는 “정지합니다”. React는 이 컴포넌트를 넘기고, 트리 상의 다른 컴포넌트의 렌더링을 시도합니다.
4. React는 `<ProfileTimeline>`의 렌더링을 시도합니다. `resource.posts.read()`를 호출합니다. 또 한번, 아직 데이터가 없으므로, 이 컴포넌트 또한 “정지합니다”. React는 이 컴포넌트도 넘기고, 트리 상의 다른 컴포넌트의 렌더링을 시도합니다.
5. 렌더링을 시도할 컴포넌트가 남아있지 않습니다. `<ProfileDetails>`이 정지된 상태이므로, React는 트리 상에서 `<ProfielDetails>` 위에 존재하는 것 중 가장 가까운 `<Suspense>` Fallback을 찾습니다. 그것은 `<h1>Loading profile...</h1>`입니다. 일단, 지금으로서는 할 일이 다 끝났습니다

여기에서 `resource` 객체는 아직은 존재하지 않지만, 로딩이 이루어질 데이터를 나타냅니다. `read()` 를 호출할 경우, 데이터를 얻거나 또는 컴포넌트가 **정지합니다.** 

데이터를 받으면, React는 정지한 컴포넌트를 렌더링을 다시 시도하며, 그 때 마다 React가 “더 깊은곳까지” 처리할 수 있게 될 것입니다. Suspense는 로딩 상태의 기본 단위를 변경할 수 있고, 코드를 크게 변경하지 않고도 로딩 상태의 배치를 조정할 수 있도록 해줍니다.

`Suspense` 를 사용하면 컴포넌트에서 if/else로 받아오는 것이 아니라 코드가 더 깔끔해지고 데이터플로우도 이해하기 쉬워졌다. 리액트 18을 사용할 때 Fetching라이브러리는 리액트와 커뮤니케이션하여 컴포넌트안에 데이터를 가져오는지 여부를 알릴 수 있습니다.

### 오류처리하기

프라미스를 사용하여 코드를 작성할 때 오류를 처리하기 위하여 `catch`를 사용했을 것입니다. Suspense를 사용할 때는 프라미스가 렌더링을 시작하길 기다리지 않는데, 이러한 오류 처리가 어떻게 이루어질까요?

 

Suspense를 사용하면, 불러오기에서 발생한 오류를 처리하는 것이 렌더링 오류를 처리하는 것과 동일한 방식으로 이루어집니다. 어디에서든 [오류 경계](https://ko.reactjs.org/docs/error-boundaries.html)를 렌더링하여 그 아래에 존재하는 컴포넌트의 오류를 “잡아낼” 수 있습니다.

우선, 프로젝트 전체에 걸쳐 사용할 오류 경계 컴포넌트를 아래와 같이 정의합니다.

```tsx
function ProfilePage() {
  return (
    <Suspense fallback={<h1>Loading profile...</h1>}>
      <ProfileDetails />
      <ErrorBoundary fallback={<h2>Could not fetch posts.</h2>}>
        <Suspense fallback={<h1>Loading posts...</h1>}>
          <ProfileTimeline />
        </Suspense>
      </ErrorBoundary>
    </Suspense>
  );
}
```

이 오류 경계는 렌더링 오류, 그리고 데이터 불러오기를 위한 Suspense에서 발생한 오류를 둘 다 잡아낼 것입니다. 오류 경계는 쓰고 싶은 만큼 사용할 수 있지만, 오류 경계의 배치는 계획적으로 이루어지는 것이 좋습니다.
