# Ref Hooks(useRef)

> Ref 훅(useRef)

`useRef`는 렌더링에 필요하지 않은 값을 참조할 수 있는 React 훅입니다.

```typescript
const ref = useRef(initialValue)
```

## `useRef(initialValue)`

컴포넌트의 최상위 레벨에서 `useRef`를 호출하여 ref를 선언합니다.

```typescript
import { useRef } from 'react';

function MyComponent() {
  const intervalRef = useRef(0);
  const inputRef = useRef(null);
```

### 매개변수

- `initialValue` : ref 객체의 `current` 프로퍼티 초기 설정 값입니다. 이 인자는 초기 렌더링 이후 무시됩니다.

### 반환값

- `current` : 처음에는 전달한 `initialValue` 로 설정됩니다. 나중에 다른 값으로 변경 가능하며 ref 객체를 **JSX**  노드 ref 속성으로 React로 전달하면 React는 `current` 프로퍼티로 설정합니다,
- 다음 렌더링에서 `useRef`는 동일한 객체를 반환합니다.

### 주의사항

- `ref.current`는 state랑 다르게 변이가 가능하나 렌더링에 사용되는 객체 state를 포함하는 경우 해당 객체는 변이하면 안됩니다.
- `ref.current` 프로퍼티를 변경해도 React는 컴포넌트를 다시 렌더링하지 않습니다.
- 초기화를 제외하고는 렌더링 중에 `ref.current`를 쓰거나 읽지 마세요. 이렇게 하면 컴포넌트의 동작을 예측할 수 없게 됩니다.

## 사용법

`useRef`는 처음에 제공한 초기값으로 설정된 단일 `current` 프로퍼티가 있는 ref 객체를 반환하고 다음 렌더링에서 `useRef`는 동일한 객체를 반환합니다.

**ref를 변경해도 리렌더링을 촉발하지 않습니다.**

ref를 사용하면 다음을 보장합니다

- 렌더링할 때마다 재설정되는 일반 변수와 달리 리렌더링 사이에 **정보를 저장**할 수 있습니다.
- 리렌더링을 촉발하는 state 변수와 달리 변경해도 **리렌더링을 촉발하지 않습니다.**
- 정보가 공유되는 외부 변수와 달리 각각의 컴포넌트에 **로컬로 저장됩니다**

화면에 표시되는 정보를 저장하는 데는 ref가 적합하지 않습니다

### 주의사항

사용하지 말아야할 방법

```typescript
function MyComponent() {
  // 🚩 렌더링 중에 ref를 작성하지 마세요.
  myRef.current = 123;
  // 🚩 렌더링 중에 ref를 읽지 마세요.
  return <h1>{myOtherRef.current}</h1>;
}
```

대신 하는 방법 useEffect에서 하거나 이벤트 핸들러에서 사용하거나 해야합니다.

```typescript
function MyComponent() {
  useEffect(() => {
    // ✅ Effect에서 ref를 읽거나 쓸 수 있습니다.
    myRef.current = 123;
  });
  function handleClick() {
    // ✅ 이벤트 핸들러에서 ref를 읽거나 쓸 수 있습니다.
    doSomething(myOtherRef.current);
  }
}
```

### ref 콘텐츠 재생성 피하기

```typescript
const playerRef = useRef(new VideoPlayer());
```

`new VideoPlayer()`의 결과는 초기 렌더링에만 사용되지만, 호출 자체는 이후의 모든 렌더링에서도 여전히 계속 이뤄집니다. 이는 값비싼 객체를 생성하는 경우 낭비일 수 있습니다.

이 문제를 해결하기 위해 다음과 같이 ref를 초기화 할 수 있습니다.

```typescript
const playerRef = useRef(null);
  if (playerRef.current === null) {
    playerRef.current = new VideoPlayer();
  }
```

반적으로 렌더링 중에 `ref.current`를 쓰거나 읽는 것은 허용되지 않습니다. 하지만 이 경우에는 결과가 항상 동일하고 초기화 중에만 조건이 실행되므로 충분히 예측할 수 있으므로 괜찮습니다

## 해결 방법

### ****커스텀 컴포넌트에 대한 ref를 얻을 수 없습니다****

```typescript
const inputRef = useRef(null);

return <MyInput ref={inputRef} />;
```

`ref`를 가져올려는 컴포넌트를  `forwardRef`로 감싸세요.

```typescript
import { forwardRef } from 'react';

const MyInput = forwardRef(({ value, onChange }, ref) => {
  return (
    <input value={value} onChange={onChange} ref={ref} />
  );
});
```