# Performance Hooks(useMemo)

> 퍼포먼스 훅(useMemo)

`useMemo`는 리렌더링 사이의 계산 결과를 캐시할 수 있는 React 훅입니다.

```typescript
const cachedValue = useMemo(calculateValue, dependencies)
```

## 참조

### **`useMemo(calculateValue, dependencies)`**

컴포넌트 최상단에서 `useMemo`를 호출하여 리렌더링 사이의 계산 결과를 캐시합니다.

```typescript
import { useMemo } from 'react';

function TodoList({ todos, tab }) {
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab]
  );
}
```

### 매개변수

- `calculateValue` : 캐시하려는 값을 계산하는 함수입니다. 이 함수는 순수함수여야 하고 어떤 타입이든 반환을 해야합니다. 초기 렌더링시 함수를 호출하며 의존성 배열의 값이 변경되지 않으면 동일한 값을 반환합니다. 아니면 `calculateValue` 값을 반환하여 그 값을 저장합니다.
- `dependencies` : `calculateValue` 코드 내에서 참조되는 모든 반응형 값들의 목록입니다.

### 반환값

초기 렌더링에서 `useMemo`는 인자 없이 `calculateValue`를 호출한 결과를 반환합니다.

이후 렌더링에서는, 의존성이 변경되지 않은 경우에는 마지막 렌더링에서 저장된 값을 반환하고, 변경된 경우에는 `calculateValue`를 다시 호출하여 그 결과를 반환합니다.

### 주의사항

- React는 **특별한 이유가 있지 않는 한 캐시된 값을 유지하려고 합니다.** 개발 환경 및 상용 환경 모두에서, 초기 마운트 중에 컴포넌트가 일시 중단(suspend)되면 React는 캐시를 폐기합니다.
- `useMemo`는 성능 최적화를 위해서만 사용하면 좋습니다. 그렇지 않으면 `state`나 `ref`를 사용하는 것이 더 나을 수 있습니다.

## 사용법

### **비용이 많이 드는 재계산 생략하기**

기본적으로 React는 컴포넌트가 다시 렌더링될 때마다 컴포넌트 전체 본문을 다시 실행합니다.

```typescript
// 기본적으로 컴포넌트 사용하는 코드
function TodoList({ todos, tab, theme }) {
  const visibleTodos = filterTodos(todos, tab);
}
```

위 코드 같은 경우 데이터가 크지 않으면 대부분의 계산은 빠르기 때문에 문제가 되지 않으나 큰 배열을 필터링하거나, 변환하거나, 고비용의 계산을 수행할 때, 데이터가 변경되지 않았다면 다시 계산하는 것을 건너뛰고 싶을 수 있습니다.

그런 경우 `useMemo`를 사용하여 캐싱 즉 `memoization` 을 합니다.

### **모든 곳에 `useMemo`를 추가해야 하나요?**

대부분의 인터랙션이 투박한 앱의 경우(페이지 또는 전체 섹션 교체 등) 일반적으로 메모화가 필요하지 않습니다. 반면 앱이 그림 편집기처럼 대부분의 인터랙션이 도형 이동 처럼 세분화되어 있다면 메모화가 매우 유용할 수 있습니다

`useMemo`를 통한 최적화 할 때 유용한 몇 가지

- `useMemo`에 넣는 계산이 눈에 띄게 느리고 의존성이 거의 변하지 않는 경우
- `[memo](https://react-ko.dev/reference/react/memo)`로 감싼 컴포넌트에 prop으로 전달하는 경우. 값이 변경되지 않은 경우 리렌더링을 건너뛰고 싶을 수 있습니다.
- 전달한 값은 나중에 어떤 훅의 의존성으로 사용될 것입니다. 예를 들어, 또다른 `useMemo` 또는 `useEffect`에서 이 값에 의존하고 있을 수 있습니다.

일부 개별 사례에 대해 생각하지 않고 전부 `memoization` 을 하는 방식을 택하기도 하지만 가독성과 효과적인 것은 아닙니다.

**실제로 몇 가지 원칙을 따르면 많은 메모화가 불필요해 질 수 있습니다.**

- 컴포넌트가 다른 컴포넌트를 시각적으로 감쌀 때 **JSX**를 자식으로 받아들이도록 하세요. 
이렇게 하면 `wrapper` 컴포넌트가 자체 `state`를 업데이트할 때 React는 그 자식 컴포넌트가 다시 렌더링할 필요가 없다는 것을 알 수 있습니다.
- 로컬 state를 선호하고 필요 이상으로 state를 끌어올리지 마세요.
- 컴포넌트를 다시 렌더링했을 때 문제가 발생하거나 눈에 띄는 시각적 아티팩트가 생성된다면 컴포넌트에 버그가 있는 것입니다. 렌더링 로직을 순수하게 유지하세요.
- state를 업데이트하는 불필요한 Effect를 피하세요.
- Effect에서 불필요한 의존성을 제거하세요.

특정 인터렉션이 여전히 느리게 느껴진다면 [React 개발자 도구 profiler](https://legacy.reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html)를 사용해 어떤 컴포넌트가 메모화를 통해 가장 큰 이점을 얻을 수 있는지 확인하고 필요한 경우 메모화 하세요.

### **컴포넌트의 리렌더링 건너뛰기**

어떤 경우에는 `useMemo`를 사용하여 자식 컴포넌트의 리렌더링 성능을 최적화할 수도 있습니다. 이를 설명하기 위해, `TodoList` 컴포넌트가 `visibleTodos`를 자식 `List` 컴포넌트에 prop으로 전달한다고 가정해 보겠습니다.

```typescript
export default function TodoList({ todos, tab, theme }) {
  // ...
  return (
    <div className={theme}>
      <List items={visibleTodos} />
    </div>
  );
}
```

**기본적으로 컴포넌트가 리렌더링되면 React는 모든 자식 컴포넌트를 재귀적으로 리렌더링합니다.**

이 때문에 다른 `theme`로 `TodoList`가 리렌더링되면 `List` 컴포넌트도 리렌더링됩니다. 이는 리렌더링에 많은 계산이 필요하지 않은 컴포넌트의 경우에는 괜찮습니다. 그러나 리렌더링이 느리다는 것을 확인했다면, 이전 렌더링과 동일한 prop이 있는 경우 `List`가 리렌더링을 건너뛰도록 `memo`로 감싸줄 수 있습니다.

```typescript
import { memo } from 'react';

const List = memo(function List({ items }) {
  // ...
});
```

이 변경으로 인해 **`List`는 모든 prop이 이전 렌더링과 같은 경우에는 리렌더링을 건너뛸 것입니다.**

```typescript
export default function TodoList({ todos, tab, theme }) {
  // theme가 변경될 때마다 매번 다른 배열이 됩니다...
  const visibleTodos = filterTodos(todos, tab);
  return (
    <div className={theme}>
      {/* ... List의 prop은 절대로 같을 수 없으므로, 매번 리렌더링할 것입니다 */}
      <List items={visibleTodos} />
    </div>
  );
}
```

**위 예제에서는 `filterTodos` 함수가 항상 다른 배열을 생성합니다.** 따라서 `memo` 최적화도 작동하지 않음을 의미합니다. 바로 이럴 경우 `useMemo` 가 유용합니다.

```typescript
export default function TodoList({ todos, tab, theme }) {
  // 리렌더링 사이에 계산 결과를 캐싱하도록 합니다...
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab] // ...따라서 여기의 의존성이 변경되지 않는다면 ...
  );
  return (
    <div className={theme}>
      {/* ...List는 같은 props를 전달받게 되어 리렌더링을 건너뛸 수 있게 됩니다 */}
      <List items={visibleTodos} />
    </div>
  );
}
```

**`visibleTodos` 계산을 `useMemo`로 감싸면, 리렌더링 사이에 동일한 값이 보장됩니다.**

### **개별 JSX 노드 메모화**

`<List>`를 `memo`로 감싸는 대신 `useMemo`로 감쌀 수 있습니다. 동일한 작동을 하지만 노드를 단순히 코드에서 동일하게 보이는 것이 아니라 **실제로 동일한 객체**여야 합니다. 이 예시에서 `useMemo`는 바로 이런 역할을 합니다.

```typescript
const children = useMemo(() => <List items={visibleTodos} />, [visibleTodos]);
```

하지만 JSX 노드를 수동으로 `useMemo`로 감싸는 것은 편리하지 않습니다. 예를 들어, 이 작업을 조건부로 수행할 수 없습니다. 일반적으로 JSX 노드를 `useMemo`로 감싸는 대신 `memo`로 컴포넌트를 감싸는 이유입니다.

### **다른 훅의 의존성 메모화**

```typescript
function Dropdown({ allItems, text }) {
  const searchOptions = useMemo(() => {
    return { matchMode: 'whole-word', text };
  }, [text]); // ✅ text 변경시에만 변경됨

  const visibleItems = useMemo(() => {
    return searchItems(allItems, searchOptions);
  }, [allItems, searchOptions]); // ✅ allItems 또는 searchOptions 변경시에만 변경됨
```

위의 예에서 `text` 가 변경되지 않았다면 `searchOptions` 객체도 변경되지 않습니다. 이보다 더 나은 수정 방법은 `searchOptions` 객체 선언을 `useMemo` 계산 함수 내부로 이동하는 것입니다

```typescript
function Dropdown({ allItems, text }) {
  const visibleItems = useMemo(() => {
    const searchOptions = { matchMode: 'whole-word', text };
    return searchItems(allItems, searchOptions);
  }, [allItems, text]); // ✅ allItems 또는 text 변경시에만 변경됨
```

### **함수 메모화**

**함수를 메모화하는 것은 충분히 흔한 일이므로 React는 이를 위한 특별한 훅을 제공합니다.** 중첩 함수를 추가로 작성할 필요가 없도록 함수를 `useMemo` 대신 `useCallback`으로 감싸세요

## 문제해결

### `useMemo` 호출이 객체를 반환하길 기대했으나 실제로는 undefined 객체를 반환합니다**

아래 코드는 작동하지 않습니다.

```typescript
 // 🔴 화살표 함수에서 () => { 만으로는 객체를 리턴할 수 없습니다.
  const searchOptions = useMemo(() => {
    matchMode: 'whole-word',
    text: text
  }, [text]);
```

명시적으로 `return`문을 작성하세요.

```typescript
// ✅ 이 코드는 작동하며 명시적입니다
  const searchOptions = useMemo(() => {
    return {
      matchMode: 'whole-word',
      text: text
    };
  }, [text]);
```