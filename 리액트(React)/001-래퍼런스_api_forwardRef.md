# forwardRef

`forwardRef`를 사용하면 컴포넌트가 ref를 사용하여 부모 컴포넌트에 DOM 노드를 노출할 수 있습니다.

```typescript
const SomeComponent = forwardRef(render)
```

## 참조

### `forwardRef(render)`

컴포넌트가 `ref`를 받아 자식 컴포넌트로 전달하도록 하려면 `forwardRef()`를 호출하세요.

```typescript
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  // ...
});
```

### 매개변수

- `render` : 컴포넌트의 렌더링 함수입니다. React는 컴포넌트가 부모로부터 받은 props와 `ref`를 가지고 이 함수를 호출합니다. 반환하는 JSX는 컴포넌트의 결과물이 됩니다

### 반환값

- `forwardRef` : JSX에서 렌더링할 수 있는 React 컴포넌트를 반환합니다. 일반 함수로 정의된 React 컴포넌트와 달리, `forwardRef`가 반환하는 컴포넌트는 `ref` prop을 받을 수도 있습니다.

## `render` 함수**

`forwardRef`는 렌더링 함수를 인자로 받습니다. React는 `props` 및 `ref`와 함께 이 함수를 호출합니다

```typescript
const MyInput = forwardRef(function MyInput(props, ref) {
  return (
    <label>
      {props.label}
      <input ref={ref} />
    </label>
  );
});
```

### 매개변수

- `props` : 부모 컴포넌트가 전달한 props입니다.
- `ref` : 부모 컴포넌트가 전달한 `ref` 속성입니다. `ref`는 객체나 함수일 수 있습니다. ref를 전달하지 않은 경우 `null`이 됩니다. 받은 `ref`를 다른 컴포넌트에 전달하거나 `useImperativeHandle`에 전달해야 합니다.

### 반환값

- `forwardRef` : JSX에서 렌더링할 수 있는 React 컴포넌트를 반환합니다.

## 사용법

### **부모 컴포넌트에 DOM 노드 노출하기**

아래의 코드와 같이 적용하면 부모 컴포넌트가 `MyInput`에 의해 노출된 `<input>` DOM 노드에 접근할 수 있습니다

```typescript
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} ref={ref} />
    </label>
  );
});
```

컴포넌트 내부의 DOM 노드에 대한 ref를 노출하면 나중에 컴포넌트의 내부를 변경하기가 더 어려워진다는 점에 유의하세요

### **DOM 노드 대신 명령형 핸들 노출하기**

일부 컴포넌트가 `MyInput`에 대한 ref를 받으면 DOM 노드 대신 `{ focus, scrollIntoView }` 객체만 받습니다. 이를 통해 DOM 노드에 대해 노출하는 정보를 최소한으로 제한할 수 있습니다.

```typescript
import { forwardRef, useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input {...props} ref={inputRef} />;
});
```

### 주의사항

**ref를 과도하게 사용하지 마세요**. 노드로 스크롤하기, 노드에 초점 맞추기, 애니메이션 트리거하기, 텍스트 선택하기 등 prop으로 표현할 수 없는 필수적인 동작에만 ref를 사용해야 합니다.

**prop으로 무언가를 표현할 수 있다면 ref를 사용해서는 안 됩니다.** 예를 들어, `Modal` 컴포넌트에서 `{ open, close }`와 같은 명령형 핸들을 노출하는 대신 `<Modal isOpen={isOpen} />`과 같이 prop `isOpen`을 사용하는 것이 더 좋습니다.

## 문제 해결

### **컴포넌트가 `forwardRef`로 감싸져 있지만, 컴포넌트의 `ref`는 항상 `null`입니다.**

일부 로직이 조건부인 경우 `MyInput`에 대한 `ref`가 `null`일 수도 있습니다

```typescript
const MyInput = forwardRef(function MyInput({ label, showInput }, ref) {
  return (
    <label>
      {label}
      {showInput && <input ref={ref} />}
    </label>
  );
});
```