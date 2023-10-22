# Ref Hooks(useImperativeHandle)

> Ref 훅들(useImperativeHandle)

`useImperativeHandle`은 ref로 노출되는 핸들을 사용자가 직접 정의할 수 있게 해주는 React 훅입니다.

```typescript
useImperativeHandle(ref, createHandle, dependencies?)
```

## 참조

컴포넌트의 최상위 레벨에서 `useImperativeHandle`을 호출하여 노출할 ref 핸들을 사용자가 직접 정의할 수 있습니다

```typescript
import { forwardRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  useImperativeHandle(ref, () => {
    return {
      // ... your methods ...
      // ... 메서드는 여기에 작성합니다 ...
    };
  }, []);
```

## 사용법

### 매개변수

- `ref`: `forwardRef` 렌더 함수에서 두 번째 인자로 받은 `ref`입니다.
- `createHandle`: 인자가 없고 노출하려는 ref 핸들을 반환하는 함수입니다. 해당 ref 핸들은 어떠한 유형이든 될 수 있습니다. 일반적으로 노출하려는 메서드가 있는 객체를 반환합니다
- `dependencies`: `createHandle` 코드 내에서 참조하는 모든 반응형 값을 나열한 목록입니다.

### 반환값

`useImperativeHandle`은 `undefined`를 반환합니다.

기본적으로 컴포넌트는 자식 컴포넌트의 DOM 노드를 부모 컴포넌트에 노출하지 않습니다.

예를 들어, `MyInput`의 부모 컴포넌트가 `<input>` DOM 노드에 접근하려면 `forwardRef`를 사용하여 선택적으로 참조에 포함해야 합니다

```typescript
const MyInput = forwardRef(function MyInput(props, ref) {
  return <input {...props} ref={ref} />;
});
```

그러나 대신 사용자 지정 값을 노출할 수 있습니다. 노출된 핸들을 사용자 정의하려면 컴포넌트의 최상위 레벨에서 `useImperativeHandle`을 호출하세요.

아래의 코드에서 `<input>`에 대한 ref는 더 이상 전달되지 않습니다.

예를 들어, 전체 `<input>` DOM 노드를 노출하지 않고 `focus`와 `scrollIntoView`의 두 메서드만을 노출하고 싶다고 가정해 봅시다.

그러기 위해서는 실제 브라우저 DOM을 별도의 ref에 유지해야 합니다.

그리고 `useImperativeHandle`을 사용하여 부모 컴포넌트에서 호출할 메서드만 있는 핸들을 노출합니다.

```typescript
import { forwardRef, useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null);
  useImperativeHandle(ref, () => {
    return {
      focus() { inputRef.current.focus(); },
      scrollIntoView() { inputRef.current.scrollIntoView();},
    };
  }, []);
  return <input {...props} ref={inputRef} />;
});
```

### ****사용자 정의 명령 노출****

`imperative handle`을 통해 노출하는 메서드는 DOM 메서드와 정확하게 일치할 필요가 없습니다

```typescript
// 자식 컴포넌트 ref
useImperativeHandle(ref, () => {
    return {
      scrollAndFocusAddComment() {
        commentsRef.current.scrollToBottom();
        addCommentRef.current.focus();
      }
    };
  }, []);
// 부모 컴포넌트
function handleClick() {
    postRef.current.scrollAndFocusAddComment();
}
```

### 주의사항

**ref를 과도하게 사용하지 마세요.** ref는 props로 표현할 수 없는 필수적인 행동에만 사용해야 합니다.

예를 들어, 특정 노드로 스크롤하기, 노드에 초점 맞추기, 애니메이션 촉발하기, 텍스트 선택하기 등이 있습니다.

**prop으로 표현할 수 있는 것은 ref를 사용하지 마세요.**

예를 들어, `Modal` 컴포넌트에서 `{open, close}`와 같은 imperative handle을 노출하는 대신 `<Modal isOpen={isOpen} />`과 같은 `isOpen` prop을 사용하는 것이 더 좋습니다.

Effect를 사용하면 prop을 통해 명령형 동작**(imperative behavior)**을 노출할 수 있습니다.