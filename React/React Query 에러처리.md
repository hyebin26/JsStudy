useQuery를 커스텀 훅을 사용해서 서버 데이터를 읽을 때, 에러처리를 하는 방법을 정리한 글입니다
작성중

### 1. UseEffect

```jsx
import { useEffect } from "react";
import { useQuery } from "react-query";
import { useNavigate } from "react-router-dom";

const fetchCarClasses = () => {
  return fetch("...");
};

const useTestQuery = () => {
  const navi = useNavigate();
  const query = useQuery("getCars", fetchCarClasses, {
    retry: false,
  });
  useEffect(() => {
    if (query.isError) {
      navi("/errorPage");
    }
  }, [query.isError]);

  return query;
};

export default useTestQuery;
```

우선 시작은 에러처리를 할 때, 해당 데이터를 보여주는 UI 컴포넌트에서 하는 것은 복잡함을 유발(비즈니스 로직 분리)할 수 있기 때문에 error처리를 데이터를 받는 커스텀 훅에서 처리하고 싶다라는 생각에서 시작한다. 

에러가 발생하면  `errorPage` 로 이동시키고 싶은데 처음에 선택한 방법은 `useEffect` 이다. 에러가 발생하면 `useEffect`를 이용해서 `isError` 의 상태가 변경되면 호출되게 하고 `isError` 가 true이면 페이지를 이동시킨다.

주의할점

- `retry:false` 를 한 이유: retry는 에러가 발생했을 때 해당 서버에 요청을 다시 보내는 것이다. `default:3` 으로 설정되어 있기 때문에 3번의 요청 후에 `isError:true` 로 변하기 때문에 재요청을 보내지 않는 것으로 설정해주었다.
- `isError:()⇒ navi(”/errorPage”)` 를 사용하지 않은 이유: useEffect를 이용해서 페이지를 이동시키는 것이 더 안전해서.이유는?
