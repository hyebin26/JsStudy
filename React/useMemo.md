```jsx
const handleTest = (isOk) => {
  return isOk ? "red" : "purple";
};

function Test({ isOk, children }) {
  const good = useMemo(() => {
    return handleTest(isOk);
  }, [isOk]);
  return (
    <>
      <h2>{good}</h2>
      {children}
    </>
  );
}

export default Test;
```

고비용의 계산을 통해 변수의 값이 변경될 때 useMemo를 사용하면 두 번째 인자로 받는 디펜던시의 값이 변경되어야만 렌더링이 된다.
