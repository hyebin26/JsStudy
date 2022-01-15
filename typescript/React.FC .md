참고:[https://blog.variant.no/a-better-way-to-type-react-components-9a6460a1d4b7](https://blog.variant.no/a-better-way-to-type-react-components-9a6460a1d4b7)의 번역글입니다. 

### All about that Expression

만약에 너가 React.FC 타입을 사용하기를 원한다면, 매개변수나 리턴되는 값이 아니라 함수 타입 자체에 주석을 달아야만 한다. 이것은 너가 표현식으로서 함수를 가지는 것을 필요로한다는 것을 의미한다. 나는 함수 표현식을 좋아하지만 일반적으로 최상위 함수에 대한 함수 선언을 선호한다. 이유는 여러가지가 있다.

호이스팅 및 direct export를 통해 콘텐츠의 우선 순위(Prioritizing content)를 지정하기 위해.

### Prioritizing content

JavaScript/Typescript 모듈을 만들 때, 나는 더 중요한 컨텐츠를 강조하는 것을 시도한다. 가급적이면, 컨텍스트 및 모듈의 주요 목표를 이해하기 위한 관련 콘텐츠가 파일의 "스크롤 없이 볼 수 있는 부분"에 표시되도록 최적화를 시도한다. 즉, Editor나 code explorer에서 열면 가장 중요한 내용을 볼 수 있다. 이는 helper componenets 및 utill들이 있는 export components가 더 아래로 이동했음을 의미한다.

```jsx
// 1. Setup 변수. 예상대로 작동가능
const BoundFooItem = partial(FooItem, { title: "Foo" });

// 2. Main export
export default function Foo() {}

// 3. Additional exports. 추가의 exports. 주로 main export로 의미적으로 그룹화할 수 있는 자식 유형
export function FooItem() {}

// 4. Helper components
function MyInternalComponent() {}

// 5. Utils
function add() {}
function partial() {}
function identity() {}
```

위의 `FooItem`과 `MyInternalComponent`가 정상적으로 작동하려면, 호이스팅이 필요하다. 함수 표현식을 사용하는 것은 올바른 상황에서 이러한 유형의 호이스팅이 가능하지만 TDZ(Temporal Dead Zone)을 유발할 수 있다. **함수 선언이 더 안전한 선택이다.**

```jsx
const BoundFooItem = partial(FooItem, { title: "Foo" });
// 에러: ReferenceError: Cannot access 'FooItem' before initialisation
export default function Foo() {}
export const FooItem = () => {};
```

### Exporting directly

React.FC를 사용하여 코드베이스로 작업한 적이 있다면 다음 패턴을 인식할 것이다.

```jsx
const MyComponent: React.FC<{}> = () => {
	...
};
export default MyComponent;
```

이는 TypeScript와 모듈 문법의 조합이 default export로 형식화된 함수 표현식을 지원하지 않기 때문이다. 이것은 더 주관적이지만 모듈의 공개 API가 무엇인지 쉽게 추적할 수 있도록 export 및 생성을 그룹화하는 것을 좋아한다. 선언과 함께 default export를 사용하는 것은 React.FC는 지원하지 않는다.

```jsx
// MyComponent가 기본 API라는 것이 더 명확하다.
export default function MyComponent() {}
```

### **No Generics**

TDZ없는 호이스팅이 없고 components를 직접 내보낼 수 없다는 것은 우리가 해결할 수 있는 한계다. 그러나 React로 TypeScript를 배울 때 어려움을 겪었던 한 가지 기술적 한계가 있다. 따라서 React.FC를 React Components: Generics의 범용 타이핑 스타일로 사용하는 것을 권장하기 어렵다.

드롭다운이나 목록과 같은 재사용 가능한 구성 요소를 만들 때 전달된 항목의 유형을 정확히 모르는 경우가 종종 있다. 이것은 제네릭의 경우이다. React와 TypeScript는 제네릭을 지원하지만 React.FC를 사용할 때 제네릭을 수행하는 것을 허용하지 않는다.

```tsx
type MyDropDownProps<T> = {
 items: T[];
 itemToString(item: T): string;
 onSelected(item: T): void;
};

// 아래의 예제 중에 유효한 것은 없다.
const MyDropDown: React.FC<MyDropDownProps> = (props) => {};
const MyDropDown: React.FC<MyDropDownProps<T>> = <T>(props) => {};
const MyDropDown: React.FC<MyDropDownProps<T>> = (props) => {};
const MyDropDown<T>: React.FC<MyDropDownProps<T>> = (props) => {};
// React.FC를 사용할 때 generic을 사용하는 직접적인 방법이 없다.
```

MyDropDown과 함께 제네릭을 올바르게 사용하려면 타입을 타입 매개변수로 직접 사용해야 한다.

```tsx
type MyDropDownProps<T> = {
  items: T[];
  itemToString(item: T): string;
  onSelected(item: T): void;
};
// 유효한 코드
function MyDropDown<T>(props: MyDropDownProps<T>) {}
```

매개변수를 직접 입력함으로써 다른 함수와 같은 방법으로 TypeScript를 사용한다. React components을 위한 특별한 경우가 아니다. 이것은 TypeScript와 함께 다른 코드베이스의 기존 지식을 사용할 수 있음을 의미한다. 여기서 우리가 부족한 한 가지는 component에서 return value를 입력하는 것이다. TypeScript는 자체적으로 반환 유형을 유추할 수 있다. 너가 명시적으로 반환 유형을 추가할 수 있다.

```tsx
function MyDropDown<T>(props: MyDropDownProps<T>): JSX.Element {}
```

### **We gon’ take Children**

JSX.Elements에 대해 이야기를 하려고 한다. 위에서 제안한 해결책을 사용하면 children을 어떻게 처리할까? 이것은 작동하지 않는다.

```tsx
type MyComponentProps = { className: string };
// 작동하지 않는다.
function MyComponent({ className, children }: MyComponentProps) {
  // chidren은 타입이 아니고 TypeScript가 에러를 발생시킨다.
  return <div className={className}>{children}</div>;
}
```

그러나 React.FC를 사용하면 children은 작동된다.

```tsx
// 작동가능
const MyComponent: React.FC<MyComponentProps> = function ({
  className,
  children,
}) {
  return <div className={className}>{children}</div>;
};
```

`React.FC`가 props 유형에 children 속성을 추가하기 때문입니다. 이것은 편리한 것 같지만 사실은 나쁘다. `React.FC`를 사용하면 항상 자식을 속성으로 사용하는 것처럼 보인다.

매개변수에서 직접 타입을 지정하면 더 많은 유연성과 정확성을 제공한다. 그럼 children은 어떻게 입력합니까? 수동으로 또는 React의 도우미 유형을 사용하는 두 가지 방법을 사용할 수 있다.

```tsx
type MyComponentProps = {
  title: string;
  // ReactNode는 array, scalar value등 모든 children 타입을 허락하는 타입이다.
  children: React.ReactNode;
};
function MyComponentCorrect({ title, children }: MyComponentProps) {}
```

children에게 어떤 유형을 사용할지 기억하기 어렵다면 React 유형에서 편리한 도우미를 사용할 수 있다.

```tsx
type MyComponentProps = React.PropsWithChildren<{
  title: string;
}>;
function MyComponentCorrect({ title, children }: MyComponentProps) {}
```

동일한 작업을 수행하고 `PropsWithChildren`은 `React.FC`와 마찬가지로 교차 유형으로 정의된다.

매개변수에 직접 props를 사용하면 component를 보다 정확하게 입력하고 유연성을 높일 수 있다.
