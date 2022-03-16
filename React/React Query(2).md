# 3가지 코어 컨셉

## 요약

1. Queries: 유니크 키로 연결되어 있고 서버에서 데이터를 가져오기 위해 모든 Promise기반 메서드와 함께 사용할 수 있다.
2. Mutation: 서버의 데이터를 생성/업데이트/삭제하는 side-effect를 사용할 때 사용된다.
3. Query Invalidation: 쿼리를 다시 가져오기 전에 쿼리가 out of data가 될 때 `invalidateQueries` 메소드를 이용해 오래된 것을 지능적으로 표시하고 잠재적으로 다시 가져올 수 있다. 

## Queries

쿼리는 **unique key로 연결된** 비동기 소스에 대한 선언적인 dependency이다. 쿼리는 서버에서 데이터를 가져오기 위해 모든 Promise기반 메서드(GET 및 POST 메서드 포함)와 함께 사용할 수 있다. 만약에 method가 서버의 데이터를 수정하는 경우 대신 **[Mutations](https://react-query.tanstack.com/docs/guides/mutations)**를 사용하는 것이 좋다. 

컴포넌트 혹은 커스텀 훅스에서 쿼리를 구독하기 위해, useQuery를 호출하기 위해선 최소한으로 밑의 항목들이 필요하다.

- **쿼리를 위한 unique key**
- Promise를 리턴하는 함수 ⇒ Resolve data or throw an error

```jsx
import { useQuery } from 'react-query'
 
 function App() {
   const info = useQuery('todos', fetchTodoList)
 }
```

**Unique key**는 내부적으로 refetching, caching 그리고 앱을 통해 쿼리를 공유하는 것에 사용된다.

쿼리의 결과들은 `useQuery`로 리턴되며 템플릿 및 여러 데이터의 용도에 필요한 쿼리에 대해 모든 정보들이 포함되있다.

```jsx
const result = useQuery('todos', fetchTodoList)
```

`result` 객체들은 너가 알고 싶은 매우 중요한 상태들을 포함한다. query는 오직 어떤 주어진 순간에 존재하는 상태중에 하나가 될 수 있다.

- `isLoading` or `status === “loading”` - 쿼리는 데이터가 없고 현재 fetching중이다.
- `isError` or `status === “error”` - 쿼리는 에러와 마주쳤다.
- `isSuccess` or `status === “success”` - 쿼리는 성공했고 데이터를 이용가능하다.
- `isIdle` or `status === “idle”` - 쿼리는 현재 이용불가능하다.

대부분의 쿼리들에서, `isLoading` 상태를 체크하기 위해 충반하고, 그리고 나서 `isError` 상태, 마지막으로 데이터가 이용가능하고 성공적인 상태가 된다면 렌더하는 것을 가정할 수 있다.

```jsx
function Todos() {
   const { isLoading, isError, data, error } = useQuery('todos', fetchTodoList)
 
   if (isLoading) {
     return <span>Loading...</span>
   }
 
   if (isError) {
     return <span>Error: {error.message}</span>
   }
 
   // We can assume by this point that `isSuccess === true`
   return (
     <ul>
       {data.map(todo => (
         <li key={todo.id}>{todo.title}</li>
       ))}
     </ul>
   )
 }
```

## Mutations

쿼리들과 달리, Mutations는 일반적으로 데이터를 생성/업데이트/삭제하거나 server side-effect에 사용된다. 이러한 목적을 위해, React Query는 useMutation 훅을 export한다.

서버에 새로운 todo를 추가하는 mutation 예제를 확인해보자.

```jsx
function App(){
	const mutation = useMutation(newTodo =>{
    return axios.post("/todos",newTodo);
	})
return (
     <div>
       {mutation.isLoading ? (
         'Adding todo...'
       ) : (
         <>
           {mutation.isError ? (
             <div>An error occurred: {mutation.error.message}</div>
           ) : null}
 
           {mutation.isSuccess ? <div>Todo added!</div> : null}
 
           <button
             onClick={() => {
               mutation.mutate({ id: new Date(), title: 'Do Laundry' })
             }}
           >
             Create Todo
           </button>
         </>
       )}
     </div>
}
```

Mutation은 주어진 순간에 다음 상태 중 하나에만 있을 수 있다.

- `isIdle` or `status === “idle”` - Mutation은 현재 idle 또는 fresh/reset상태이다.
- `isLoading` or `status === “loading”` - Mutation은 현재 동작하고 있는 중이다.
- `isError` or `status === “error”` - Mutation은 에러와 맞닥뜨렸다.
- `isSuccess` or `status === “success”` - Mutation은 성공적이고 mutation은 데이터는 이용가능하다.

위의 예제에서 **단일 변수 또는 객체** mutate를 사용하여 함수를 호출하여 mutation function에 변수를 전달할 수 있음을 확인했다.

변수만 있어도 mutation이 특별한 것은 아니지만 `onSuccess` 옵션, **[쿼리 클라이언트의](https://react-query.tanstack.com/reference/QueryClient#queryclientinvalidatequeries)** `invalidQueries`방법 및 쿼리 클라이언트의 `setQueryData`방법과 함께 사용하면 mutation이 강력한 도구가 될 수 있다.

```jsx
// 리액트 16버전보다 더 빠른 버전은 동작하지 않는다.
 const CreateTodo = () => {
   const mutation = useMutation(event => {
     event.preventDefault()
     return fetch('/api', new FormData(event.target))
   })

   return <form onSubmit={mutation.mutate}>...</form>
 }
```

### Resetting Mutation State

때때로 `error` 혹은 `data`를 정리하는 것을 필요한 경우가 있다. 이것을 하기 위해, 이것을 다루기 위해 `reset` 을 사용할 수 있다.

```jsx
const CreateTodo = () => {
   const [title, setTitle] = useState('')
   const mutation = useMutation(createTodo)
 
   const onCreateTodo = e => {
     e.preventDefault()
     mutation.mutate({ title })
   }
 
   return (
     <form onSubmit={onCreateTodo}>
       {mutation.error && (
         <h5 onClick={() => mutation.reset()}>{mutation.error}</h5>
       )}
       <input
         type="text"
         value={title}
         onChange={e => setTitle(e.target.value)}
       />
       <br />
       <button type="submit">Create Todo</button>
     </form>
   )
 }
```

### Mutation Side Effects

`useMutation` 은 mutation 생명주기 동안 스테이지에서 빠르고 쉽게 side-effect를 허락하는 약간의 helper options이 딸려 있다. 이는 mutation과 **[optimistic updates](https://react-query.tanstack.com/guides/optimistic-updates)** 후에 쿼리들을 refetching과 무효화하는 것에 (**[invalidating and refetching queries after mutations](https://react-query.tanstack.com/guides/invalidations-from-mutations))** 모두 유용하다.

```jsx
useMutation(addTodo, {
   onMutate: variables => {
    // Mutation이 발생한다.
 
     // 예를 들어 롤백할 때 사용할 데이터가 포함된 컨텍스트를 
     // 선택적으로 반환한다.
     return { id: 1 }
   },
   onError: (error, variables, context) => {
     // 에러가 발생한다.
     console.log(`rolling back optimistic update with id ${context.id}`)
   },
   onSuccess: (data, variables, context) => {
     // Boom baby!
   },
   onSettled: (data, error, variables, context) => {
     // Error or success... doesn't matter!
   },
 })
```

콜백 함수에서 Promise를 리턴할 때, 다음 콜백이 호출되기 전에 먼저 기다립니다.

```jsx
 useMutation(addTodo, {
   onSuccess: async () => {
     console.log("I'm first!")
   },
   onSettled: async () => {
     console.log("I'm second!")
   },
 })
```

너는 `mutate`를 호출 할 때 `useMutation` 에 정의된 것보다 너가 **원하는 추가의 콜백들을 트리거**하고 싶을 수도 있다. 이것은 구성 요소별 side-effect를 유발하기 위해 사용할 수 있다. 그것을 위해, mutation 변수 뒤에 `mutate` 함수에 같은 콜백 옵션을 제공할 수 있다. 지원되는 overrides에는 `onSuccess`, `onError`, `onSettled`가 포함된다. 변형이 완료되기 전에 구 성요소가 마운트 해제되면 이러한 추가 콜백이 실행되지 않는다.

```jsx
useMutation(addTodo, {
   onSuccess: (data, variables, context) => {
     // I will fire first
   },
   onError: (error, variables, context) => {
     // I will fire first
   },
   onSettled: (data, error, variables, context) => {
     // I will fire first
   },
 })
 
 mutate(todo, {
   onSuccess: (data, variables, context) => {
     // I will fire second!
   },
   onError: (error, variables, context) => {
     // I will fire second!
   },
   onSettled: (data, error, variables, context) => {
     // I will fire second!
   },
 })
```

## Query Invalidation(쿼리 무효화)

쿼리를 다시 가져오기 전에 쿼리가 오래될(out of date) 때까지 기다리는 것이 항상 작동하는 것은 아니다. 특히 사용자가 수행한 작업으로 인해 쿼리 데이터가 오래되었다는 사실을 알고 있는 경우에는 더욱 그렇다. 이를 위해 QueryClient에는 쿼리를 오래된 것으로 지능적으로 표시하고 잠재적으로 다시 가져올 수 있는 `invalidateQueries` 메서드가 있다.

```jsx
// 캐시의 모든 모든 뮤효화 
 queryClient.invalidateQueries()
 // `todos`로 시작하는 키로 모든 쿼리를 무효화
 queryClient.invalidateQueries('todos')
```

쿼리가 `invalidateQueries` 와 함께 무효화 되었을 때, 두 가지가 발생한다.

- 오래된(stale)로 표시된다. 이 오래된 상태는 useQuery 또는 관련 훅에서 사용 중인 모든 `staleTime`구성을 재정의 한다.
- 쿼리가 현재 useQuery 또는 관련 훅을 통해 렌더링되는 경우 백그라운드에서도 refetch된다.

### Query Matching with `invalidateQueries`

`invalidateQueries` and `removeQueries` 같은 API를 사용할 때, 다수의 쿼리들을 매치할 수 있다. 사용할 수 잇는 필터 유형에 대한 정보는 쿼리 필터를 참조.

밑의 예제의 경우, 우리는 쿼리키에 `todos` 로 시작하는 무효화 쿼리들에 prefix(앞에 붙이기) `todos` 를 사용할 수 있다.

```jsx
import { useQuery, useQueryClient } from 'react-query'
 
 // 컨텍스트에서 QueryClient가져오기
 const queryClient = useQueryClient()
 
 queryClient.invalidateQueries('todos')
 
 // 아래의 두 쿼리 모두 무효화된다.
 const todoListQuery = useQuery('todos', fetchTodoList)
 const todoListQuery = useQuery(['todos', { page: 1 }], fetchTodoList)
```

더 구체적인 쿼리 키를 `invalidateQueries`메소드에 전달하여 특정 변수가 있는 쿼리를 무효화할 수도 있다.

```jsx
queryClient.invalidateQueries(['todos', { type: 'done' }])
 
 // 밑의 쿼리는 무효화된다.
 const todoListQuery = useQuery(['todos', { type: 'done' }], fetchTodoList)
 
 // 그러나, 아래의 쿼리는 무효화되지 않을 것이다.
 const todoListQuery = useQuery('todos', fetchTodoList)
```

### 참고
https://react-query.tanstack.com/overview
