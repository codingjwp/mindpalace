# Performance Hooks(useTransition)

> 퍼포먼스 훅(useTransition)

`useTransition`은 UI를 차단하지 않고 state를 업데이트할 수 있는 React 훅입니다.

```typescript
const [isPending, startTransition] = useTransition()
```

## 참조

### **`useTransition()`**

컴포넌트의 최상위 레벨에서 `useTransition`을 호출하여 일부 state 업데이트를 트랜지션으로 표시합니다.

```typescript
import { useTransition } from 'react';

function TabContainer() {
  const [isPending, startTransition] = useTransition();
  // ...
}
```

### 매개변수

- `useTransition`은 매개변수를 받지 않습니다.

### 반환값

- 보류 중인 트랜지션이 있는지 여부를 알려주는 `isPending` 플래그
- state 업데이트를 트랜지션으로 표시할 수 있는 `startTransition` 함수

### **`startTransition` function**

`useTransition`이 반환하는 `startTransition` 함수를 사용하면 state 업데이트를 트랜지션으로 표시할 수 있습니다

```typescript
function TabContainer() {
  const [isPending, startTransition] = useTransition();
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
}
```

### 매개변수

- `scope` : 하나 이상의 `set` 함수를 호출하여 일부 state를 업데이트하는 함수입니다. React는 매개변수 없이 `scope`를 즉시 호출하고 `scope` 함수 호출 중에 동기적으로 예약된 모든 state 업데이트를 트랜지션으로 표시합니다. 이는 논블로킹이고, 원치 않는 로딩을 표시하지 않을 것입니다.

### 반환값

- `startTransition`은 아무것도 반환하지 않습니다.

### 주의사항

- 해당 state의 `set` 함수에 접근할 수 있는 경우에만 업데이트를 트랜지션으로 감쌀 수 있습니다.
- `startTransition`에 전달하는 함수는 동기식이어야 합니다.
React는 이 함수를 즉시 실행하여, 실행하는 동안 발생하는 모든 state 업데이트를 트랜지션으로 표시합니다.
나중에 더 많은 state 업데이트를 수행하려고 하면(예: 타임아웃), 트랜지션으로 표시되지 않습니다.
- 트랜지션으로 표시된 state 업데이트는 다른 state 업데이트에 의해 중단됩니다.
예를 들어, 트랜지션 내에서 차트 컴포넌트를 업데이트한 다음, 차트가 다시 렌더링되는 도중에 입력을 시작하면 React는 입력 업데이트를 처리한 후 차트 컴포넌트에서 렌더링 작업을 다시 시작합니다.
- 트랜지션 업데이트는 텍스트 입력을 제어하는 데 사용할 수 없습니다.
- 진행 중인 트랜지션이 여러 개 있는 경우, React는 현재 트랜지션을 함께 일괄 처리합니다. 이는 향후 릴리스에서 제거될 가능성이 높은 제한 사항입니다

## 사용법

### **트랜지션에서 상위 컴포넌트 업데이트하기**

상위에서 받아오는 함수 경우에 자식 컴포넌트에서 `startTransition` 사용하여 트랜지션을 할 수 있습니다.

```typescript
export default function TabButton({ children, isActive, onClick }) {
  const [isPending, startTransition] = useTransition();
  if (isActive) {
    return <b>{children}</b>
  }
  return (
    <button onClick={() => {
      startTransition(() => {
        onClick();
      });
    }}>
      {children}
    </button>
  );
}
```

### **트랜지션 중에 ‘보류중’ state 표시하기**

```typescript
function TabButton({ children, isActive, onClick }) {
  const [isPending, startTransition] = useTransition();
  if (isPending) {
    return <b className="pending">{children}</b>;
  }
```

### **원치 않는 로딩 표시 방지하기**

`<Suspense>` 적용한 컴포넌트

```typescript
import { Suspense, useState } from 'react';
import TabButton from './TabButton.js';

export default function TabContainer() {
  const [tab, setTab] = useState('about');
  return (
    <Suspense fallback={<h1>🌀 Loading...</h1>}>
			{/*... 생략*/}
      <TabButton isActive={tab === 'posts'} onClick={() => setTab('posts')}>
        Posts
      </TabButton>
			{/*... 생략*/}
    </Suspense>
  );
}
```

`startTransition`를 사용한 컴포넌트가 존재할 경우 `onClick` 이 작동하였을 때 Suspense가 작동을 안하고 `startTransition` 가 작동하여  `return <b className="pending">{children}</b>`가 작동합니다.

```typescript
import { useTransition } from 'react';

export default function TabButton({ children, isActive, onClick }) {
  const [isPending, startTransition] = useTransition();
  if (isActive) {
    return <b>{children}</b>
  }
  if (isPending) {
    return <b className="pending">{children}</b>;
  }
  return (
    <button onClick={() => {
      startTransition(() => { onClick(); }); }}>
      {children}
    </button>
  );
}
```

트랜지션은 탭 컨테이너와 같이 이미 노출된 콘텐츠를 숨기지 않을 수 있을 만큼만 **대기**합니다. Posts 탭에 중첩된 `<Suspense>` 바운더리가 있는 경우 트랜지션은 이를 **대기**하지 않습니다.

### **Suspense가 도입된 라우터 구축하기**

React 프레임워크나 라우터를 구축하는 경우 페이지 네비게이션을 트랜지션으로 표시하는 것이 좋습니다.

```typescript
function Router() {
  const [page, setPage] = useState('/');
  const [isPending, startTransition] = useTransition();

  function navigate(url) {
    startTransition(() => {
      setPage(url);
    });
  }
```

두가지 방법으로 이 방법을 권장합니다.

- 트랜지션은 중단 가능하므로, 사용자는 다시 렌더링이 완료될 때까지 기다리지 않고 바로 클릭할 수 있습니다.
- 트랜지션은 원치 않는 로딩 표시를 방지하여, 사용자가 네비게이션 시 갑작스럽게 이동 하는 것을 방지할 수 있습니다.

## 문제 해결

### **트랜지션에서 input 업데이트가 작동하지 않습니다**

이는 트랜지션은 논블로킹이지만 변경 이벤트에 대한 응답으로 input을 업데이트하는 것은 동기적으로 이루어져야 하기 때문입니다.

input에 대한 응답으로 트랜지션을 실행하려면 두 가지 옵션이 있습니다.

- input의 (항상 동기적으로 업데이트되는) state와 트랜지션 실행시 업데이트할 state 변수를 각각 선언할 수 있습니다. 이를 통해 동기 state를 사용하여 input을 제어하고, (input보다 “지연”되는) 트랜지션 state 변수를 나머지 렌더링 로직에 전달할 수 있습니다.
- 또는 하나의 state 변수를 가지고, 실제 값보다 지연되는 `useDeferredValue`를 추가할 수 있습니다. 그러면 새로운 값을 자동으로 따라잡기 위해 논블로킹 리렌더를 촉발합니다.

### **React가 state 업데이트를 트랜지션으로 처리하지 않습니다**

```typescript
startTransition(() => {
  // ❌ Setting state *after* startTransition call
  setTimeout(() => {
    setPage('/about');
  }, 1000);
});

startTransition(async () => {
  await someAsyncFunction();
  // ❌ Setting state *after* startTransition call
  setPage('/about');
});

setTimeout(() => {
  startTransition(() => {
    // ✅ Setting state *during* startTransition call
    setPage('/about');
  });
}, 1000);

await someAsyncFunction();
startTransition(() => {
  // ✅ Setting state *during* startTransition call
  setPage('/about');
});
```

### **컴포넌트 외부에서 `useTransition`을 호출하고 싶습니다**

훅이기 때문에 컴포넌트 외부에서 `useTransition`을 호출할 수 없습니다. 이 경우 대신 독립형 `startTransition` 메서드를 사용하세요. 동일한 방식으로 작동하지만 `isPending` 표시기를 제공하지 않습니다.