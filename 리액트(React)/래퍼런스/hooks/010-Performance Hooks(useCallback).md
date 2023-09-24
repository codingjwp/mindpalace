# Performance Hooks(useCallback)

> 퍼포먼스 훅(useCallback)

`useCallback`은 리렌더링 사이에 함수 정의를 캐시할 수 있게 해주는 React 훅입니다.

```typescript
const cachedFn = useCallback(fn, dependencies)
```

## 참조

### **`useCallback(fn, dependencies)`**

최상위 컴포넌트에서 `useCallback`을 호출하여 리렌더링 사이에 함수 정의를 캐시합니다.

```typescript
const handleSubmit = useCallback((orderDetails) => {
	post('/product/' + productId + '/buy', {
		referrer,
		orderDetails,
	});
}, [productId, referrer]);
```

### 매개변수

- `fn` : 캐시하려는 함수 값입니다
- `dependencies` : `fn` 코드 내에서 참조된 모든 반응형 값의 배열입니다

### 반환값

- 초기 렌더링에서 `useCallback`은 전달한 `fn` 함수를 반환합니다.
- 렌더링 중에는 마지막 렌더링에서 이미 저장된 `fn` 함수를 반환하거나(의존성이 변경되지 않은 경우), 렌더링 중에 전달했던 `fn` 함수를 반환합니다.

## 사용법

### **컴포넌트 리렌더링 건너뛰기**

렌더링 성능을 최적화할 때 자식 컴포넌트에 전달하는 함수를 캐시해야 할 때가 있습니다.

아래 코드와 같이 자식 컴포넌트에 `handleSubmit` 함수를 전달한다고 가정해 보겠습니다.

```typescript
return (
    <div className={theme}>
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
```

먼저 props가 지난 렌더링과 동일한 경우 `memo`로 감싸 `ShippingForm`에게 리렌더링을 건너뛰도록 지시할 수 있습니다.

```typescript
const ShippingForm = memo(function ShippingForm({ onSubmit }) {
  // ...
});
```

**`handleSubmit`을 `useCallback`으로 감싸면, 리렌더링 사이에 동일한 함수가 되도록 할 수 있습니다.**

```typescript
// 리렌더링 사이에 함수를 캐싱하도록 지시합니다...
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]); // ...따라서 이 의존성이 변경되지 않는 한...
```

### ****useCallback과 useMemo는 무슨 관련이 있을까요?****

차이점은 캐시할 수 있는 항목에 있습니다

- **`useMemo`는 호출한 함수의 결과를 캐시합니다.**
- **`useCallback`은 함수 자체를 캐시합니다**

### ****useCallback을 모든 곳에 추가해야 하나요?****

`useCallback`으로 함수를 캐싱하는 것은 몇 가지 경우에만 유용합니다.

- `memo`로 감싼 컴포넌트에 prop으로 전달합니다
- 전달한 함수는 나중에 일부 훅의 의존성으로 사용됩니다

### **메모된 콜백에서 state 업데이트하기**

일반적으로 메모화된 함수는 가능한 적은 의존성을 갖기를 원할 것입니다. 다음 state를 계산하기 위해 일부 state만 읽어야 하는 경우, 대신 업데이터 함수를 전달하여 해당 의존성을 제거할 수 있습니다.

```typescript
function TodoList() {
  const [todos, setTodos] = useState([]);

  const handleAddTodo = useCallback((text) => {
    const newTodo = { id: nextId++, text };
    setTodos(todos => [...todos, newTodo]);
  }, []); // ✅ todos에 대한 의존성이 필요하지 않음
```

### **Effect가 너무 자주 발동되지 않도록 하기**

Effect 내부에 함수를 호출하고 싶은 경우가 있습니다. 하지만 이로 인해 문제가 발생합니다.

```typescript
useEffect(() => {
    const options = createOptions();
    const connection = createConnection();
    connection.connect();
    return () => connection.disconnect();
  }, [createOptions]); // 🔴 문제: 이 의존성은 렌더링시마다 변경됨
```

이 문제를 해결하려면 Effect에서 호출해야 하는 함수를 `useCallback`으로 감싸면 됩니다

```typescript
const createOptions = useCallback(() => {
    return {
      serverUrl: 'https://localhost:1234',
      roomId: roomId
    };
  }, [roomId]); // ✅ roomId 변경시에만 변경됨

  useEffect(() => {
    const options = createOptions();
    const connection = createConnection();
    connection.connect();
    return () => connection.disconnect();
  }, [createOptions]); // ✅ createOptions 변경시에만 변경됨
```

**하지만 함수 의존성을 없애는 편이 더 좋습니다.** 함수를 Effect 내부로 이동하세요

```typescript
useEffect(() => {
    function createOptions() { // ✅ useCallback이나 함수에 대한 의존성이 필요하지 않음!
      return {
        serverUrl: 'https://localhost:1234',
        roomId: roomId
      };
    }
    const options = createOptions();
    const connection = createConnection();
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ roomId 변경시에만 변경됨
```

### **커스텀 훅 최적화하기**

커스텀 훅을 작성하는 경우 반환하는 모든 함수를 `useCallback`으로 감싸는 것이 좋습니다

```typescript
function useRouter() {
  const { dispatch } = useContext(RouterStateContext);

  const navigate = useCallback((url) => {
    dispatch({ type: 'navigate', url });
  }, [dispatch]);

  const goBack = useCallback(() => {
    dispatch({ type: 'back' });
  }, [dispatch]);

  return { navigate, goBack, };
}
```

⏮️ Previous : [Performance Hooks(useMemo)](./009-Performance%20Hooks(useMemo).md)

⏭️ Next : [Performance Hooks(useTransition)](./011-Performance%20Hooks(useTransition).md)