# Other Hooks(useSyncExternalStore)

> 다른 훅들(useSyncExternalStore)

`useSyncExternalStore`는 외부 스토어를 구독할 수 있는 React 훅입니다.

```typescript
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)
```

## 참조

### **`useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)`**

컴포넌트의 최상위 레벨에서 `useSyncExternalStore`를 호출하여 외부 데이터 저장소에서 값을 읽습니다.

```typescript
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

function TodosApp() {
  const todos = useSyncExternalStore(todosStore.subscribe, todosStore.getSnapshot);
}
```

### 매개변수

- `subscribe` : **하나의 `callback` 인수를 받아 스토어를 구독하는 함수입니다.** 스토어가 변경되면 제공된 `callback`을 호출해야 합니다. `subscribe` 함수는 구독을 해제하는 함수를 반환해야 합니다.
- `getSnapshot` : **컴포넌트에 필요한 스토어 데이터의 스냅샷을 반환하는 함수입니다.** 스토어가 변경되지 않은 상태에서 `getSnapshot`을 반복적으로 호출하면 동일한 값을 반환해야 되야 하며 저장소가 변경되어 반환된 값이 다른 경우 컴포넌트를 리렌더링 합니다.
- `getServerSnapshot` : 선택적으로 사용가능하고 스토어에 있는 데이터의 초기 스냅샷을 반환하는 함수입니다. 오직 **서버에서 렌더링**할 때와 이를 클라이언트에서 **hydrate**하는 동안에만 사용됩니다. 이 함수가 제공되지 않으면 서버에서 컴포넌트를 렌더링할 때 오류가 발생합니다.

### 반환값

- 렌더링 로직에 사용할 수 있는 스토어의 현재 스냅샷입니다.

### 주의사항

- `getSnapshot`이 반환하는 스토어 스냅샷은 불변이어야 합니다.
- 리렌더링시에 다른 `subscribe` 함수가 전달되면 React는 새로 전달된 `subscribe` 함수를 사용하여 저장소를 다시 구독합니다. 컴포넌트 외부에서 `subscribe`를 선언하면 이를 방지할 수 있습니다.

## 사용법

### **외부 스토어 구독하기**

대부분의 React 컴포넌트는 `props`, `state`, `context`에서만 데이터를 읽습니다. 하지만 때론 시간이 지남에 따라 변경되는 React 외부의 저장소에서 데이터를 읽어야 하는 경우도 있습니다.

여기에 몇 가지가 그에 해당합니다.

- React 외부에 state를 보관하는 서드파티 상태 관리 라이브러리
- 변이 가능한 값을 노출하는 브라우저 API와 그 변이 사항을 구독하는 이벤트.

```typescript
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

function TodosApp() {
  const todos = useSyncExternalStore(todosStore.subscribe, todosStore.getSnapshot);
  // ...
}
```

스토어에 있는 데이터의 snapshot을 반환합니다. 두 개의 함수를 인수로 전달해야 합니다.

- `subscribe` 함수는 스토어를 구독해야 하고, 구독 취소 함수를 반환해야 합니다.
- `getSnapshot` 함수는 스토어에서 데이터의 스냅샷을 읽어야 합니다.

React는 이 함수를 사용해 컴포넌트가 스토어를 구독한 상태로 유지하고 변경 사항이 있을 때 다시 렌더링합니다.

아래 코드에서 `todosStore`는 React 외부에 데이터를 저장하는 외부 store로 구현되어 있습니다.

```typescript
// 이것은 React와 통합해야 하는 타사 상점의 예시입니다
// 앱이 React로 완전히 빌드된 경우, 대신 React state를 사용하는 것이 좋습니다.

let nextId = 0;
let todos = [{ id: nextId++, text: 'Todo #1' }];
let listeners = [];

export const todosStore = {
  addTodo() {
    todos = [...todos, { id: nextId++, text: 'Todo #' + nextId }]
    emitChange();
  },
  subscribe(listener) {
    listeners = [...listeners, listener];
    return () => {
      listeners = listeners.filter(l => l !== listener);
    };
  },
  getSnapshot() {
    return todos;
  }
};

function emitChange() {
  for (let listener of listeners) {
    listener();
  }
}
```

### 참고 사항

가능하면  `Built in React state`를 `useState` 및 `useReducer`와 함께 사용하는 것이 좋습니다. `useSyncExternalStore` API는 주로 기존의 비 React 코드와 통합해야 할 때 유용합니다.

### **서버 렌더링의 지원 추가하기**

React 앱이 서버 렌더링을 사용하는 경우, React 컴포넌트는 브라우저 환경 외부에서도 실행되어 초기 HTML을 생성합니다. 이로 인해 외부 스토어에 연결할 때 몇 가지 문제가 발생합니다.

- 브라우저 전용 API에 연결하는 경우 서버에 해당 API가 존재하지 않으므로 작동하지 않습니다.
- 타사 데이터 저장소에 연결하는 경우 서버와 클라이언트 간에 일치하는 데이터가 필요합니다.

이러한 문제를 해결하려면 `getServerSnapshot` 함수를 `useSyncExternalStore`의 세 번째 인수로 전달하세요.

```typescript
export function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot);
  return isOnline;
}

function getSnapshot() {
  return navigator.onLine;
}

function getServerSnapshot() {
  return true; // Always show "Online" for server-generated HTML
}

function subscribe(callback) {
  // ...
}
```

`getServerSnapshot` 함수는 두가지 상황에서만 실행됩니다.

- HTML을 생성할 때 서버에서 실행됩니다.
- hydration중, 즉,React가 서버 HTML을 가져와서 인터랙티브하게 만들 때 클라이언트에서 실행됩니다

## 문제 해결

### **오류가 발생했습니다: `getSnapshot`의 결과를 캐시해야 합니다.**

`etSnapshot`은 실제로 변경된 사항이 있는 경우에만 다른 객체를 반환해야 합니다.

```typescript
function getSnapshot() {
  // 🔴 getSnapshot에서 항상 다른 객체를 반환하지 마세요
  return {
    todos: myStore.todos
  };
}
// 수정 후 
function getSnapshot() {
  // ✅ 불변데이터는 반환할 수 있습니다
  return myStore.todos;
}
```