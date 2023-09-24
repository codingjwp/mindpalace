# memo

`memo`를 사용하면 컴포넌트의 props가 변경되지 않은 경우 리렌더링을 건너뛸 수 있습니다.

```typescript
const MemoizedComponent = memo(SomeComponent, arePropsEqual?)
```

## 참조

### **`memo(Component, arePropsEqual?)`**

`memo`로 컴포넌트를 감싸 **memoized** 버전의 컴포넌트를 얻을 수 있습니다.

메모화된 버전의 컴포넌트는 일반적으로 props가 변경되지 않는 한 부모 컴포넌트가 리렌더링할 때 같이 리렌더링하지 않습니다. 그러나 메모화는 성능을 최적화하지만 보장되는 것은 아니어서, React는 여전히 리렌더링 할 수도 있습니다.

```typescript
import { memo } from 'react';

const SomeComponent = memo(function SomeComponent(props) {
  // ...
});
```

### 매개변수

- `Component` : memoize하려는 컴포넌트입니다. `memo`는 이 컴포넌트를 수정하지 않고 새롭게 메모화된 컴포넌트를 반환합니다. 함수와 `forwardRef` 컴포넌트를 포함한 모든 유효한 React 컴포넌트가 허용됩니다.
- `arePropsEqual?` : 컴포넌트의 이전 prop 및 새로운 prop의 두 인자를 받는 함수이며 선택적으로 사용할 수 있습니다. 이전 prop와 새로운 prop를 비교하여 같으면 `true` 아니면 `false`를 반환합니다. 일반적으로 이 함수를 지정하지 않습니다.

### 반환값

- `memo`는 새로운 React 컴포넌트를 반환합니다. `memo`에 제공한 원본 컴포넌트와 동일하게 동작하지만, 부모가 리렌더링 하더라도 prop이 변경되지 않는 한 React는 이를 리렌더링하지 않습니다.

## 사용법

### **prop이 변경되지 않았을 때 리렌더링 건너뛰기**

```typescript
const Greeting = memo(function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
});

export default Greeting;
```

props, state 및 context가 변경되지 않으면 항상 동일한 출력을 반환해야 함을 의미합니다.

**`memo`는 성능 최적화를 위해서만 사용해야 합니다.** `memo` 없이 코드가 작동하지 않으면 먼저 근본적인 문제를 찾아 해결하세요.

### **context를 사용하여 메모화된 컴포넌트 업데이트하기**

컴포넌트가 메모화 되었더라도 사용 중인 `context`가 변경될 때 이 컴포넌트는 리렌더링됩니다. 메모화는 부모로부터 전달되는 props에만 연관이 있습니다.

### **props 변경 최소화하기**

React는 `Object.is` 비교를 사용하여 컴포넌트의 모든 prop을 이전 값과 비교합니다. `Object.is (3, 3)`은 `true`이지만 `Object.is({}, {})`는 `false`입니다.

`useMemo` : `memo`를 최대한 활용하려면, props가 변경되는 시간을 최소화해야 합니다. 예를 들어, prop이 객체인 경우, `useMemo`를 사용하여 부모 컴포넌트가 해당 객체를 매번 다시 만드는 것을 방지하세요

메모화된 컴포넌트에 함수를 전달해야 하는 경우, 아예 변경되지 않도록 컴포넌트 외부에서 선언하거나, `useCallback`을 사용하여 정의를 캐시하십시오.

props 변경을 최소화하는 더 나은 방법은 props가 해당 요소에 필요한 최소한의 정보만을 받고 있는지 확인하는 것입니다. 예를 들어, 전체 객체 대신 개별 값을 사용할 수 있습니다.

```typescript
function Page() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);
  return <Profile name={name} age={age} />;
}

const Profile = memo(function Profile({ name, age }) {
  // ...
});
```

## 문제 해결

### **prop이 객체, 배열 또는 함수일 때 컴포넌트가 리렌더링됩니다.**

React는 이전 prop과 새 prop을 얕은 비교로 동등성을 파악합니다.

즉, 각각의 새 prop이 이전 prop과 참조가 동일한지 여부를 고려합니다. 부모가 다시 렌더링될 때마다 새 객체나 배열을 생성하면 설령 개별 요소들이 모두 동일하더라도 여전히 React는 변경된 것으로 간주합니다.

마찬가지로 부모 컴포넌트를 렌더링할 때 새 함수를 만들면 React는 함수의 정의가 동일하더라도 변경된 것으로 간주합니다.

⏮️ Previous : [lazy](./003-lazy.md)

⏭️ Next : [startTransition](./005-startTransition.md)