# startTransition

`startTransition`은 UI를 차단하지 않고 state를 업데이트할 수 있습니다.

```typescript
startTransition(scope)
```

## 참조

### `startTransition(scope)`

`startTransition` 함수를 사용하면 state 업데이트를 트랜지션으로 표시할 수 있습니다.

```typescript
import { startTransition } from 'react';

function TabContainer() {
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
}
```

### 매개변수

- `scope` :

### 반환값

- `startTransition`은 아무것도 반환하지 않습니다.

### 주의사항

- `startTransition`은 트랜지션이 ‘pending’인지 추적하는 방법을 제공하지 않습니다. 트랜지션이 진행 중일 때 ‘pending’ 표시기를 보여주고 싶다면, 대신 `useTransition`이 필요합니다.
- 해당 state의 `set` 함수에 접근할 수 있는 경우에만 업데이트를 트랜지션으로 감쌀 수 있습니다. 일부 prop이나 커스텀 훅 값에 대한 응답으로 트랜지션을 시작하려면, 대신 `useDeferredValue`를 사용해보세요.
- `startTransition`에 전달하는 함수는 동기식이어야 합니다. React는 이 함수를 즉시 실행하여, 실행하는 동안 발생하는 모든 state 업데이트를 트랜지션으로 표시합니다.
- 트랜지션으로 표시된 state 업데이트는 다른 state 업데이트에 의해 중단됩니다.
- 트랜지션 업데이트는 텍스트 입력을 제어하는 데 사용할 수 없습니다.
- 진행 중인 트랜지션이 여러 개 있는 경우, React는 현재 트랜지션을 함께 일괄 처리합니다. 이는 향후 릴리스에서 제거될 가능성이 높은 제한 사항입니다.

## 사용법

### **state 업데이트를 논블로킹 트랜지션으로 표시하기**

```typescript
import { startTransition } from 'react';

function TabContainer() {
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
}
```

트랜지션을 사용하면 느린 기기에서도 사용자 인터페이스 업데이트를 반응성 있게 유지할 수 있습니다.

트랜지션을 사용하면 재렌더링 도중에도 UI가 반응성을 유지합니다.

예를 들어, 사용자가 탭을 클릭했다가 마음이 바뀌어 다른 탭을 클릭하면 첫 번째 리렌더링이 완료될 때까지 기다릴 필요 없이 다른 탭을 클릭할 수 있습니다.