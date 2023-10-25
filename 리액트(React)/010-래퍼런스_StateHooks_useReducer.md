# State Hooks(useReducer)

> State 훅들(useReducer)

## `useReducer`

`useReducer` 는 컴포넌트에  `reducer`를 추가할 수 있는 React입니다.

```typescript
const [state, dispatch] = useReducer(reducer, initialArg, init?)
```

## 참조

컴포넌트의 최상위 레벨에서 `useReducer`를 호출하여 reducer를 통해 state를 관리하세요.

```typescript
import { useReducer } from 'react';

function reducer(state, action) {
}

function MyComponent() {
  const [state, dispatch] = useReducer(reducer, { age: 42 });
```

### 매개변수

- `reducer` : state가 업데이트되는 방식을 지정하는 `reducer` 함수입니다. 순수 함수여야 하고 `state`와 `action`을 인자로 받으며 state를 반환 해야합니다.
- `initialArg` : 초기 state가 계산되는 값입니다. 이 값에서 초기 state를 계산하는 방법은 다음 `init` 인자에 따라 달라집니다.
- `init` :  선택적으로 초기 state를 계산하는 방법을 지정하는 함수입니다. 이것을 지정하지 않으면 초기 state는 `initialArg` 로 설정됩니다. 그렇지 않으면 `init(initialArg)`를 호출한 결과로 설정됩니다.

### 반환값

`useReducer` 는 정확히 두 개의 값을 가진 배열을 반환합니다:

- `init(initialArg)`, `initialArg` 의 현재 state값
- state를 다른 값으로 업데이트하는 `dispatch` 함수

### 주의사항

**컴포넌트의 최상위 레벨** 또는 자체 훅에서만 호출할 수 있습니다. 반복문이나 조건문 내부에서는 호출할 수 없습니다.

### `dispatch` 함수

`useReducer` 가 반환하는 `dispatch` 함수를 사용하면 state를 다른 값으로 업데이트하고 다시 렌더링을 촉발할 수 있습니다. `dispatch` 함수에 유일한 인수로 액션을 전달해야 합니다

```typescript
const [state, dispatch] = useReducer(reducer, { age: 42 });

function handleClick() {
  dispatch({ type: 'incremented_age' });
```

### 매개변수

- `action` : 사용자가 수행한 작업입니다.

### 반환값

- 반환값이 없습니다.

### 주의사항

**다음 렌더링에 대한 state 변수만 업데이트합니다.** 만약 `dispatch`함수를 호출한 후 state 변수를 읽으면, 호출 전 화면에 있던 이전 값이 호출됩니다.

여러분이 제공한 새 값이 `[Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)`로 비교했을 때 현재 `state`와 동일하다면, React는 **컴포넌트와 그 자식들을 다시 렌더링하는 것을 건너뜁니다.** 이것은 최적화입니다. React는 결과를 무시하기 전에 여전히 컴포넌트를 호출하게 될 수도 있지만, 코드에 영향을 미치지는 않습니다

React는 state 업데이트를 일괄처리 합니다. **모든 이벤트 핸들러가 실행되고** `set` 함수를 호출한 후에 화면을 업데이트합니다.

## 사용법

```typescript
// 컴포넌트 외부
function reducer(state, action) {
  if (action.type === 'incremented_age') {
    return {
      age: state.age + 1
    };
  }
  throw Error('Unknown action.');
}
// 컴포넌트 내부
export default function Counter() {
	const [state, dispatch] = useReducer(reducer, { age: 42 });
	return <button onClick={() => { dispatch({ type: 'incremented_age' })}}>
}
```

`useReducer` 는 `useState`와 매우 유사하지만 이벤트 핸들러의 state 업데이트 로직을 컴포넌트 외부의 단일 함수로 옮길 수 있습니다.

### reducer 함수 작성

`action`은 어떤 형태든 가질 수 있습니다. 관례상 `action`을 식별하는 `type` 프로퍼티가 있는 객체를 전달하는 것이 일반적입니다. 여기에는 `reducer`가 다음 state를 계산하는 데 필요한 최소한의 필수 정보가 포함되어야 합니다.

```typescript
dispatch({
      type: 'changed_name',
      nextName: e.target.value
    });
```

액션 유형 이름은 컴포넌트에 로컬로 지정됩니다. **각 액션은 아무리 많은 데이터를 변경하게 되더라도 오직 하나의 상호작용만을 기술합니다.** state의 모양은 임의적이지만 일반적으로 객체나 배열이 될 것입니다.

### 주의 사항

state는 읽기 전용입니다. state의 객체나 배열을 수정하지 마세요.

```typescript
function reducer(state, action) {
  switch (action.type) {
    case 'incremented_age': {
			// 🚩 state안의 객체를 변이 하지 마세요.
      state.age = state.age + 1;
      return state;
    }
```

대신, reducer로부터 새로운 객체를 반환하세요:

```typescript
function reducer(state, action) {
  switch (action.type) {
    case 'incremented_age': {
      // ✅ 새로운 객체를 반환
      return {
        ...state,
        age: state.age + 1
      };
    }
```

### 초기 state 재생성 방지하기

React는 초기 state를 한 번 저장하고 다음 렌더링에서 이를 무시합니다.

```typescript
function createInitialState(username) {
}

function TodoList({ username }) {
  const [state, dispatch] = useReducer(reducer, createInitialState(username));
```

`createInitialState(username)`의 결과는 초기 렌더링에만 사용되지만, 이후의 모든 렌더링에서도 여전히 이 함수를 호출하게 됩니다. 이 문제를 해결하려면 `useReducer` 세번 째 인수에 **초기화 함수를 전달**할 수 있습니다.

```typescript
function createInitialState(username) {
}

function TodoList({ username }) {
  const [state, dispatch] = useReducer(reducer, username, createInitialState);
```

## 문제해결

### **dispatch하면 reducer state의 일부분이 undefined가 됩니다**

새 state를 반환할 때 모든 `case` 브랜치가 **기존 필드를 모두 복사하는지** 확인하세요.

```typescript
function reducer(state, action) {
  switch (action.type) {
    case 'incremented_age': {
      return {
        ...state, // 이것을 잊지말기.
        age: state.age + 1
      };
    }
```

위의`...state`가 없으면 반환된 다음 state에는 `age` 필드만 포함되고 다른 항목은 포함되지 않습니다

### dispatch 후 전체 reducer상태가 정의되지 않습니다.

state가 예기치 않게 `undefined` 가 된 경우, 케이스 중 하나에서 state를 반환하는 것을 잊었거나 액션 유형이 `case` 문 중 어느 것과도 일치하지 않을 수 있습니다. 이유를 찾으려면 `switch`외부에서 오류를 발생시키세요.

```typescript
function reducer(state, action) {
  switch (action.type) {
    case 'incremented_age': {
    }
    case 'edited_name': {
    }
  }
  throw Error('Unknown action: ' + action.type);
}
```