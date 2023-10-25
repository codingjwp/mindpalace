# Context Hooks(useContext)

> 컨텍스트 훅들(useContext)

## `useContext`

`useContext`는 컴포넌트에서 **context**를 읽고 구독할 수 있게 해주는 React Hook입니다.

```typescript
const value = useContext(SomeContext)
```

## 참조

컴포넌트의 최상위 레벨에서 `useContext`를 호출하여 context를 읽고 구독합니다.

```typescript
import { useContext } from 'react';

function MyComponent() {
  const theme = useContext(ThemeContext);
```

### 매개변수

- `SomeContext` : 이전에 `createContext`로 생성한 `context` 입니다. `context` 자체의 정보를 보유하지 않으며 컴포넌트에서 제공하거나 읽을 수 있는 정보의 종류를 나타낼 뿐입니다.

### 반환값

- `context` : `useContext`는 호출하는 컴포넌트에서 트리 상 위에 있는 가장 가까운 `<SomeContext.Provider>`에 전달된 `value`를 반환합니다. 이러한 `provider`이 없는 경우 반환되는 값은 해당 `context`에 대해 `createContext`에 전달한 `defaultValue`가 됩니다. 반환된 값은 항상 최신 값입니다. React는 `context`가 변경되면 `context`를 읽는 컴포넌트를 자동으로 리렌더링합니다.

### 주의사항

- `<Context.Provider>`는 반드시 `useContext()` 호출을 수행하는 **컴포넌트의 위에 있어야 합니다.**
- React는 변경된 `value`를 받는 `provider`부터 시작해서 해당 `context`를 사용하는 자식들에 대해서 까지 전부 **자동으로 리렌더링**합니다. `memo`로 리렌더링을 건너뛰어도 새로운 `context`값을 수신하는 자식들을 막지 못합니다.
- 웹팩과 같은 자바스크립트 번들러를 사용할 때 모듈을 번들링할 때 식별자로 파일 경로를 사용하기 때문에, 같은 모듈이 다른 경로로 참조될 경우 중복으로 포함될 수 있습니다 이런 경우 context에 손상이 갈 수 있습니다.

## 사용법

### 트리 깊숙이 데이터 전달하기

```typescript
const ThemeContext = createContext(null);

function MyPage() {
  return (
    <ThemeContext.Provider value="dark">
      <Form />
    </ThemeContext.Provider>
  );
}

function Form() {
  return (
    <Panel title="Welcome">
      <Button>Sign up</Button>
    </Panel>
  );
}

function Panel({ title, children }) {
  const theme = useContext(ThemeContext);
  const className = 'panel-' + theme;
  return (
    <section className={className}>
		// ... 생략
		</section>
  )
}
function Button({ children }) {
  const theme = useContext(ThemeContext);
  const className = 'button-' + theme;
  return (
    <button className={className}>
		// ... 생략
		</button>
  );
}
```

### fallback 기본값 지정하기

React가 부모 트리에서 특정 `context`의 `provider`들을 찾을 수 없는 경우, `useContext()`가 반환하는 `context` 값은 해당 `context`를 생성할 때 지정한 `default value`과 동일합니다.

```typescript
const ThemeContext = createContext(null);
```

`default value`은 **절대 변경되지 않습니다.** `null` 대신 `default value`으로 사용할 수 있는 더 의미 있는 값이 있는 경우가 많습니다.

```typescript
const ThemeContext = createContext('light');
```

### **트리 일부에 대한 context 재정의하기**

필요한 만큼 provider들을 중첩하고 재정의할 수 있습니다.

```typescript
<ThemeContext.Provider value="dark">
  // ...
  <ThemeContext.Provider value="light">
    <Footer />
  </ThemeContext.Provider>
	//  ...
</ThemeContext.Provider>
```

### **객체 및 함수 전달 시 리렌더링 최적화**

context 값이 JavaScript 객체, 함수로 프로퍼티를 가지면 리렌더링할 때마다 이것은 다른함수, 다른 객체가 될 것이므로 React는 `useContext(AuthContext)`를 호출하는 트리 깊숙한 곳의 모든 컴포넌트도  리렌더링해야 합니다.

소규모 앱에서는 문제가 되지 않습니다. 그러나 `currentUser`와 같은 기초 데이터가 변경되지 않았다면 리렌더링할 필요가 없습니다.

React가 이 사실을 활용할 수 있도록 함수를 `useCallback`으로 감싸고 객체 생성은 `useMemo`로 감싸면 됩니다. 해당 방법은 성능 최적화를 위한 것입니다.

```typescript
const [currentUser, setCurrentUser] = useState(null);

const login = useCallback((response) => {
  storeCredentials(response.credentials);
  setCurrentUser(response.user);
}, []);

const contextValue = useMemo(() => ({
	currentUser,
  login
}), [currentUser, login]);

return (
  <AuthContext.Provider value={contextValue}>
    <Page />
  </AuthContext.Provider>
);
```

## 문제 해결

### **컴포넌트가 provider의 값을 인식하지 못합니다**

1. `<SomeContext.Provider>` 가 `useContext()`를 호출하는 컴포넌트 보다 위에 있어야합니다.
2. `<SomeContext.Provider>` 를 감싸는 것을 잊었는지 확인하세요. React Devtool를 사용하면 편합니다.