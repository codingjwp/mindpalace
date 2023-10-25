# lazy

`lazy` 를 사용하면 처음 렌더링될 때까지 컴포넌트 코드의 로딩을 지연시킬 수 있습니다.

```typescript
const SomeComponent = lazy(load)
```

## 참조

### `lazy(load)`

컴포넌트 외부에서 `lazy`를 호출하여 지연 로드된 React 컴포넌트를 선언합니다.

```typescript
import { lazy } from 'react';

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

### 매개변수

- `load` :  **Promise**또는 다른 thenable*(*`then` 메서드를 가진 **Promise**와 유사한 객체)을 반환하는 함수입니다. React는 반환된 컴포넌트를 처음 렌더링하려고 시도할 때까지 `load`를 호출하지 않습니다

React가 `load`를 처음 호출한 후에는 **resolve**될 때까지 기다린 다음 **resolve**된 값을 React 컴포넌트로 렌더링합니다.

반환된 **Promise** 및 **Promise**의 **resolve**된 값은 모두 캐시되므로, React는 `load`를 두 번 이상 호출하지 않습니다.

**Promise**가 **reject**되면 React는 가장 가까운 **Error Boundary**에 **reject**된 이유를 `throw` 합니다.

### 반환값

- `lazy` 는 트리에 렌더링할 수 있는 React 컴포넌트를 반환합니다.
- 지연 컴포넌트의 코드가 로딩되는 동안 렌더링을 시도하면 **일시 중단**됩니다. 로딩하는 동안 로딩 표시기를 보여주려면 `<Suspense>`를 사용하세요.

## 사용법

### **Suspense가 있는 지연 로딩 컴포넌트**

일반적으로는 정적 `import` 선언을 사용하여 컴포넌트를 import 합니다. 하지만 처음 렌더링될 때까지 로딩을 지연시키려면 import를 다음과 같이 대체하세요

```typescript
import { lazy } from 'react';

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

이 코드는 번들러나 프레임워크의 지원이 필요할 수 있는 [dynamic `import()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) 에 의존합니다.

이제 컴포넌트의 코드가 필요에 의해서만 로드되므로, 로드되는 동안 표시할 내용도 지정해야 합니다. 지연 컴포넌트나 그 부모 컴포넌트를 `<Suspense>`로 감싸면 됩니다.

```typescript
<Suspense fallback={<Loading />}>
	{/*... 생략*/}
</Suspense>
```

## 문제 해결

### `lazy` 컴포넌트의 상태가 예기치 않게 초기화 됩니다**

아래 코드는 실행이 되긴하지만 `lazy`를 컴포넌트 안에 선언하지마세요.

```typescript
import { lazy } from 'react';

function Editor() {
  // 🔴 나쁨: 이렇게 하면 리렌더링시마다 모든 state가 초기화됩니다
  const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
  // ...
}
```

항상 묘듈의 최상위 레벨에서 선언하세요.

```typescript
import { lazy } from 'react';
// ✅ 좋음: lazy 컴포넌트를 컴포넌트들의 밖에서 선언하세요
const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));

function Editor() {
  // ...
}
```