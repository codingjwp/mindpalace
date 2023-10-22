# State Hooks(useState)

> State 훅들(useState)

## `useState`

컴포넌트의 최상위 레벨에서 `useState`를 호출하여 state변수를 선언하세요.

## 매개변수

`initialState` : 초기에 state를 설정할 값입니다. 값은 모든 데이터 타입이 허용되지만, 함수에 대해서는 특별한 동작이 있습니다. 이 인자는 초기 렌더링 이후에는 무시됩니다.

함수를 `initialState`로 전달하면 이를 초기화 함수로 취급합니다.

이 함수는 순수해야 하고 인자를 받지 않아야 하며 반드시 어떤 값을 반환해야 합니다.

React는 컴포넌트를 초기화할 때 초기화 함수를 호출하고, 그 반환값을 초기 state로 저장합니다.

### 반환값

`useState`는 두개의 값을 가진 배열을 반환합니다.

1. 현재 state 값
2. `set` 함수

### 주의사항

useState는 컴포넌트 최상위 레벨이나 직접 만든 훅에서만 호출할 수 있습니다.

반복문이나 조건문에서는 호출할 수 없습니다.

## `setSomting(nextState)`과 같은 `set` 함수

`set` 함수를 사용하면 state를 다른 값으로 업데이트하며 리렌더링을 촉발할 수 있습니다.

다음 state를 전달하거나 이전 state값으로 부터 계산하여 다음 state 도출할 수 있습니다.

```typescript
// 전달
setName('tomy');
// 이전 값으로 부터 계싼
setCount(p > p + 1)
```

### 매개변수

`nextState` : state로 될 값으로 모든 데이터 타입이 허용되지만 함수에 대해서는 특별한 동작이 존재합니다.

함수로 `nextState` 를 전달하면 업데이터 함수로 취급되며 이 함수는 순수해야 하며 대기중인 state를 유일한 인자로 사용해야합니다.

### 리턴값

`set` 함수는 반환 값이 없습니다.

### 주의사항

- set 함수는 다음 렌더링에 대한 state 변수만 업데이트 합니다.
- set 함수를  호출하고 후에도 **여전히 호출 전에 있던 값이 담겨져 있습니다.**

```typescript
const [number, setNumber] = useState(1);
setNumber(2);
// number 값은 1
console.log(number)
```

- 사용자의 제공한 `nextState`가 `[Object.is](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/is)에 의해` 현재 state와 **동일한 경우 React 컴포넌트와 그 자식들을 리렌더링하지 않습니다.**
- **React는 state 업데이트를 일괄 처리 합니다.** 모든 이벤트 핸들러를 실행되고 set 함수를 호출한 다음 화면을 업데이트 합니다.
- 렌더링 도중 set 함수를 호출하는 것은 현재 렌더링 중인 컴포넌트 내에서만 가능합니다. React는 해당 출력을 버리고 새로운 state로 다시 리렌더링을 시도합니다. (**이전 정보를 저장하는데 사용 가능합니다**)

## 사용법

### 초기 state 다시 생성하지 않기

React는 초기 state를 한 번 저장하고 다음 렌더링부터는 이를 무시합니다.

```typescript
const [todos, setTodos] = useState(createInitialTodos());
```

`createInitialTodos()` 의 결과는 초기 렌더링에만 사용하지만 여전히 모든 렌더링에 이 함수를 호출하게 되기에 낭비가 될 수 있습니다.

함수 자체 인 `createInitialTodos` 를 전달하면 초기화 중에만 함수를 호출합니다.

```typescript
const [todos, setTodos] = useState(createInitialTodos);
```

### 두 개의 차이점

아래 코드와 같이 초기값을 `createInitialTodos()` 로 넣을 경우 `console.log(’called’)` 가 버튼 클릭 이벤트가 발생할 때 마다 `called`가 콘솔 창에 출력 됩니다. 하지만 `createInitialTodos`로 넣을 경우 버튼 클릭 이벤트가 발생해도 콘솔 창에 `called`가 출력 되지 않습니다.

```typescript
function createInitialTodos() {
  const initialTodos = [];
	console.log('called');
  for (let i = 0; i < 50; i++) {
    initialTodos.push({
      id: i,
      text: 'Item ' + (i + 1)
    });
  }
  return initialTodos;
}

export default function TodoList() {
  const [todos, setTodos] = useState(createInitialTodos());
	return <button onClick={() => {
    setTodos([{
     id: todos.length,
     text: text
    }, ...todos]);
	 }}>Add</button>
}
```

### key로 state 재설정하기

**컴포넌트에 다른 `key`를 전달하여 컴포넌트의 state를 재설정**할 수 있습니다

`key`가 변경되면 React는 `Form` 컴포넌트(및 그 모든 자식)를 처음부터 다시 생성하므로 state가 초기화됩니다.

```typescript
const [version, setVersion] = useState(0);
function handleReset() {
 setVersion(version + 1);
}

return (
	<>
		<button onClick={handleReset}>Reset</button>
		<Form key={version} />
	</>
);
```

### 이전 렌더링에서 얻은 정보 저장하기

보통 이벤트 헨들러에서 state값를 업데이트합니다. 하지만 드물게 렌더링에 대한 응답으로 state를 조정해야하는 경우가 있습니다.

예를 들어 props가 변경될 때 state 값도 변경하고 싶을 수 있습니다. 하지만 대부분의 경우 이 기능이 필요 하지 않습니다.

- **필요한 값을 현재 props나 다른 state에서 모두 계산할 수 있는 경우, 중복되는 state를 모두 제거하세요**. 너무 자주 재 계산하는 것이 걱정된다면, `useMemo` 훅을 사용하면 도움이 될 수 있습니다.
- 전체 컴포넌트 트리의 state를 재설정하려면 컴포넌트에 다른 key를 전달하세요.
- 가능하면 이벤트 헨들러의 모든 관련 state를 업데이트하세요.
- 이 중 어느 것에도 해당하지 않는 희귀한 경우에는, 컴포넌트가 렌더링되는 동안 `set` 함수를 호출하여 지금까지 렌더링된 값을 기반으로 state를 업데이트하는 데 사용할 수 있는 패턴이 있습니다.

### 코드 예시

카운터가 마지막 변경 이후 증가 또는 감소를 표시하고 싶다고 가정해 보겠습니다. 

`count` prop은 이를 알려주지 않으므로 이전 값을 추적해야 합니다. 이를 추적하기 위해 `prevCount` state 변수를 추가합니다. `trend`라는 또 다른 state 변수를 추가하여 count의 증가 또는 감소 여부를 추적합시다. `prevCount`와 `count`를 비교해서, 같지 않은 경우 `prevCount`와 `trend`를 모두 업데이트합니다.

이제 현재 count props와 마지막 렌더링 이후 count가 어떻게변경되었는지 모두 표시할 수 있습니다.

```typescript
// CountLabel Components
import { useState } from 'react';

export default function CountLabel({ count }) {
  const [prevCount, setPrevCount] = useState(count);
  const [trend, setTrend] = useState(null);
  console.log(prevCount, count);
  if (prevCount !== count) {
    setPrevCount(count);
    setTrend(count > prevCount ? 'increasing' : 'decreasing');
  }
  return (
    <>
      <h1>{count}</h1>
      {trend && <p>The count is {trend}</p>}
    </>
  );
}

// App Components
import { useState } from 'react';
import CountLabel from './CountLabel.js';

export default function App() {
  const [count, setCount] = useState(0);
  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
      <button onClick={() => setCount(count - 1)}>
        Decrement
      </button>
      <CountLabel count={count} />
    </>
  );
}
```

렌더링하는 동안 `set` 함수를 호출하는 경우, 그 `set` 함수는 `prevCount !== count`와 같은 조건 안에 있어야 하며, 조건 내부에 `setPrevCount(count)`와 같은 호출이 있어야 한다는 점에 유의하세요. 

그렇지 않으면 리렌더링을 반복하다가 결국 깨질 것입니다. 또한 이 방식은 오직 현재 렌더링 중인 컴포넌트의 state만을 업데이트할 수 있습니다.

렌더링 중에 다른 컴포넌트의 `set` 함수를 호출하는 것은 에러입니다. 마지막으로, 이 경우에도 `set` 함수 호출은 여전히 변이가 아닌 state 업데이트여야만 합니다. 순수 함수의 다른 규칙을 어겨도 된다는 의미가 아닙니다.

이 패턴은 이해하기 어려울 수 있으며 일반적으로 피하는 것이 가장 좋습니다. 하지만 Effect에서 state를 업데이트하는 것보다는 낫습니다.

렌더링 도중 `set` 함수를 호출하면 React는 컴포넌트가 `return`문으로 종료된 직후, 자식을 렌더링하기 전에, 해당 컴포넌트를 리렌더링 합니다.

이렇게 하면 자식 컴포넌트를 두 번 렌더링할 필요가 없습니다. 나머지 컴포넌트 함수는 계속 실행되고 결과는 버려집니다.

조건이 모든 훅 호출보다 아래에 있으면 이른 `return;`을 통해 렌더링을 더 일찍 다시 시작할 수 있습니다.

## 문제 해결

### ****state를 업데이트해도 화면이 바뀌지 않습니다****

React는 [Object.is](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/is)비교 결과 **다음 state가 이전 state와 같으면 업데이트를 무시**합니다. 이는 보통 객체나 배열의 state를 직접 변경(변이)할 때 발생합니다.

```typescript
obj.x = 10;  // 🚩 잘못된 방법: 기존 객체를 변이
setObj(obj);
// ✅ 올바른 방법: 새로운 객체 생성
setObj({ ...obj, x: 10 });
```

### ****에러가 발생했습니다: “리렌더링 횟수가 너무 많습니다”****

전형적으로 이는 렌더링 중에 state를 무조건적으로 설정하고 있음을 의미 하기 때문에, 컴포넌트가 렌더링, state 설정(렌더링 유발), 렌더링, state 설정(렌더링 유발) 등의 루프에 들어가는 것입니다.

이 문제는 이벤트 핸들러를 지정하는 과정에서 실수로 발생하는 경우가 많습니다

```typescript
// 🚩 잘못된 방법: 렌더링 동안 핸들러 요청
return <button onClick={handleClick()}>Click me</button>

// ✅ 올바른 방법: 이벤트 핸들러로 전달
return <button onClick={handleClick}>Click me</button>
return <button onClick={(e) => handleClick(e)}>Click me</button>
```