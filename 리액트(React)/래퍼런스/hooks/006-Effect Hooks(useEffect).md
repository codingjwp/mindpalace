# Effect Hooks(useEffect)

> Effect 훅들(useEffect)

`useEffect`는 컴포넌트를 외부 시스템과 동기화할 수 있는 React 훅입니다.

```typescript
useEffect(setup, dependencies?)
```

## 참조

### ****`useEffect(setup, dependencies?)`****

컴포넌트의 최상위 레벨에서 `useEffect`를 호출하여 Effect를 선언합니다.

```typescript
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [serverUrl, roomId]);
}
```

### 매개변수

- `setup`: Effect의 로직이 포함된 함수입니다. 셋업 함수는 선택적으로 클린업 함수를 반환할 수 있으며 의존성이 변경되어 리렌더링 될 때 이전 값으로 클린업 함수를 실행한 후 새로운 값으로 셋업 함수를 실행합니다.
- `dependencies` : setup 코드 내에서 참조된 모든 반응형 값의 목록입니다. 반응형 값은 `props,` `state`, 컴포넌트 본문 내부에 선언한 모든 변수, 함수를 포함합니다.

### 반환값

`useEffect`는 `undefined`를 반환합니다.

### 주의사항

- **외부 시스템과 동기화하려는 목적이 아니라면 Effect가 필요하지 않을지도 모릅니다.**
- 의존성 중 일부가 컴포넌트 내부에 정의된 객체 또는 함수인 경우 **Effect가 필요 이상으로 자주 다시 실행될 위험이 있습니다**
- Effect가 상호작용으로 인한 것이 아니라면, **React는 브라우저가 Effect를 실행하기 전에 업데이트된 화면을 먼저 그리도록 합니다.** Effect가 시각적인 작업을 하고 있고, 지연이 눈에 띄는 경우 `useEffect`를 `useLayoutEffect`로 대체해야 합니다.
- 상호작용으로 인해 Effect가 발생한 경우에도, **브라우저는 Effect 내부의 state 업데이트를 처리하기 전에 화면을 다시 그릴 수 있습니다.** 보통 이게 기대하는 동작일 것입니다. 만약 브라우저가 화면을 다시 칠하지 못하도록 차단해야 하는 경우라면 `useEffect`를 `useLayoutEffect`로 바꿔야 합니다.
- 서버 렌더링 중에는 실행되지 않으며 Effects는 클라이언트에서만 실행됩니다.

## 사용법

### ****커스텀 훅으로 Effect 감싸기****

Effect를 수동으로 작성해야 하는 경우가 자주 발생한다면 이는 컴포넌트가 의존하는 일반적인 동작에 대한 커스텀 훅을 추출해야 한다는 신호일 수 있습니다.

이 `useChatRoom` 커스텀 훅은 Effect의 로직을 보다 선언적인 API 뒤에 **숨깁니다.**

```typescript
function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = { serverUrl: serverUrl, roomId: roomId };
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

### **Effect에서 데이터 페칭하는 것을 대체할 좋은 대안은 무엇인가요?**

Effect 내부에 `fetch`를 작성하는것은 데이터를 `fetch`하는 인기있는 방법이며, 완전한 `client-side` 앱에서 특히 그렇습니다. 하지만 이는 매우 수동적인 접근 방식이며 상당한 단점이 존재합니다.

- **Effect는 서버에서 실행되지 않습니다.** 클라이언트 컴퓨터는 모든 JavaScript를 다운로드하고 앱을 렌더링해야만 이제 데이터를 로드해야 한다는 것을 알 수 있습니다. 이는 매우 효율적이지 않습니다.
- **Effect에서 직접 페칭하면 `네트워크 워터폴`을 만들기 쉽습니다.** 부모 컴포넌트를 렌더링하면 일부 데이터를 페치하고, 자식 컴포넌트를 렌더링하면 자식 컴포넌트가 데이터를 페칭하기 시작합니다. 네트워크가 매우 빠르지 않는 한 모든 데이터를 병렬로 페칭하는 것보다 훨씬 느립니다.
- **Effects에서 직접 페칭한다는 것은 일반적으로 데이터를 데이터를 미리 로드하거나 캐시하지 않는다는 의미입니다.** 예를 들어, 컴포넌트가 마운트를 해제했다가 다시 마운트하면 데이터를 다시 가져와야 합니다.
- **인체 공학적으로 좋지 않습니다.** 조건 경합과 같은 버그가 발생하지 않는 방식으로 `fetch` 호출을 작성하려면 상용구 코드가 상당히 많이 필요합니다.

### ****반응형 의존성 지정****

**Effect의 의존성을 “선택”할 수 없다는 점에 유의하세요.** Effect의 코드에서 사용되는 모든 반응형 값은 의존성으로 선언해야 합니다.

**Effect의 코드가 반응형 값을 사용하지 않는다면 의존성 목록은 비어 있어야 합니다(`[]`)**

### ****Effect의 이전 state를 기반으로 state 업데이트하기****

아래의 코드와 같이 Effect의 이전 state를 기반으로 state를 업데이트하려는 경우 문제가 발생할 수 있습니다.

```typescript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const intervalId = setInterval(() => {
      setCount(count + 1); // 매초마다 카운터를 증가시키려면
    }, 1000)
    return () => clearInterval(intervalId);
  }, [count]); // 🚩하지만 종속성으로 'count'를 지정하면 항상 간격이 재설정됩니다.
}
```

count가 반응형 값이므로 의존성 목록에 지정해야합니다. 하지만 이로 인해 count가 변경될 때 마다 clean up 함수가 발생합니다. 위 코드를 해결하기 위해서는 의존성 목록을 없애고 `setCount(c => c + 1)` 로 수정해야합니다.

## 문제 해결

### ****내 Effect가 무한히 재실행됩니다****

- Effect가 일부 state를 업데이트하고 있습니다.
- 이 state는 리렌더링을 일으키며, 이로부터 Effect의 의존성이 변경됩니다.

### ****컴포넌트가 마운트 해제되지 않았는데도 클린업 로직이 실행됩니다.****

클린업 기능은 마운트 해제시 뿐만 아니라 변경된 의존성과 함께 다시 렌더링하기 전에 매번 실행됩니다.

클린업 코드는 있는데 그에 대응하는 셋업 코드는 없다면, 일반적으로 문제가 있는 코드입니다.

```typescript
useEffect(() => {
  return () => {
    doSomething();
  };
}, []);
```

클린업 로직은 셋업 로직과 **대칭**이어야 하며, 셋업이 수행한 모든 작업을 중지하거나 취소해야 합니다.

```typescript
useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [serverUrl, roomId]);
```

⏮️ Previous : [Ref Hooks(useImperativeHandle)](./005-Ref%20Hooks(useImperativeHandle).md)

⏭️ Next: [Effect Hooks(useLayoutEffect)](./007-Effect%20Hooks(useLayoutEffect).md)