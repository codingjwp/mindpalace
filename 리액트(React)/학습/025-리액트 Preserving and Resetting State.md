# Preserving and Resetting State

> state 보존 및 재설정

state는 컴포넌트 간에 격리. Reac는 UI 트리에서 어떤 컴포넌트가 어떤 state에 속하는지 추적하며 state를 언제 보전하고 언제 초기화할지 제어할 수 있음.

## 배울 내용들

1. React가 컴포넌트 구조를 보는 방법
2. React가 state를 유지하거나 재설정하도록 선택할 때
3. React가 컴포넌트의 state를 재설정하도록 강제하는 방법
4. key와 type이 state 보존 여부에  영향을 미치는 방법

## UI 트리

브라우저는 UI를 모델링하기 위해 많은 트리 구조를 사용 DOM은 HTML요소를, CSSOM은 CSS에 대해 동일한 역할을 함

React 또한 트리 구조를 사용하여 사용자가 만든 UI를 관리하고 모델링 함. React는 JSX로부터 UI 트리를 만들고 React Dom은 UI 트리와 일치하도록 브라우저 DOM 엘리먼트를 업데이트

<img width="877" alt="UI 트리" src="https://github.com/codingjwp/mindpalace/assets/113403155/1c07554a-7436-4ad1-adf8-b56216c47e9d">


## state는 트리의 한 위치에 묶임

컴포넌트에 state를 부여할 때, state가 컴포넌트 내부에 **존재**한다고 생각할 수  있지만 state는 실제로 React 내부에서 유지 됨.

React는 UI 트리에서 해당 컴포넌트가 어디에 위치하는지에 따라 보유하고 있는 각 state를 올바른 컴포넌트와 연결을 함.

```javascript
import { useState } from 'react';

export default function App() {
  const counter = <Counter />;
  return (
    <div>
      {counter}
      {counter}
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

위 코드를 트리로 표시되는 모습은 다음과 같음

<img width="454" alt="코드 트리" src="https://github.com/codingjwp/mindpalace/assets/113403155/ce387168-58bc-4c57-bdf1-fe823306bd75">


**카운터는 각 트리에서 고유한 위치에 렌더링되기 때문에 두 개의 개별 카운터 임.** 일반적으로 React를 사용하기 위해 이러한 위치에 대해 생각할 필요는 없지만, 작동 방식을 이해하는 것이 유용할 수 있음.

counter 하나가 업데이트되면 해당 컴포넌트에 대한 state만 업데이트 됨.

<img width="503" alt="고유한 위치 렌더링" src="https://github.com/codingjwp/mindpalace/assets/113403155/447d3f2d-d2c6-42fe-b684-93f61edf088e">


```javascript
import { useState } from 'react';

export default function App() {
  const [showB, setShowB] = useState(true);
  return (
    <div>
      <Counter />
      {showB && <Counter />} 
      <label>
        <input
          type="checkbox"
          checked={showB}
          onChange={e => {
            setShowB(e.target.checked)
          }}
        />
        Render the second counter
      </label>
    </div>
  );
}
```

React는 **같은 컴포넌트를 같은 위치에 렌더링하는 한** 그 state를 유지.

이를 확인하려면 두 카운터를 모두 증가시킨 다음 “두 번째 counter 렌더링” 체크박스를 선택 해제하여 두 번째 컴포넌트를 제거한 다음 다시 선택하여 다시 추가하면 state가 완전히 사라짐.

React가 컴포넌트를 제거하면 그 state가 사라지기 때문.

<img width="459" alt="컴포넌트 삭제" src="https://github.com/codingjwp/mindpalace/assets/113403155/145054a1-f39d-4e5f-8cfd-13a075b5d910">


다시 체크박스를 선택하면 `Counter`와 state가 처음부터 초기화되고 `score=0`이 DOM에 추가됨

<img width="498" alt="컴포넌트 추가" src="https://github.com/codingjwp/mindpalace/assets/113403155/311ae767-34b2-4cac-8acc-d85310ca0c5e">

React는 컴포넌트가 **UI 트리의 해당 위치에서 렌더링되는 동안** 컴포넌트의 state를 유지함.

컴포넌트가 **제거**되거나 같은 위치에 **다른 컴포넌트가 렌더링**되면 React는 해당 컴포넌트의 state를 **삭제**.

## 동일한 위치의 동일한 컴포넌트는 state를 유지

체크박스를 선택하거나 선택 취소해도 카운터 state는 재설정되지 않음.

`isFancy`가 `true`이든 `false`이든, 루트 `App` 컴포넌트에서 반환된 `div`의 첫 번째 자식에는 항상 `<Counter />`가 존재.

```javascript
{isFancy ? (
        <Counter isFancy={true} /> 
      ) : (
        <Counter isFancy={false} /> 
      )}
```

<img width="689" alt="state no reset 컴포넌트" src="https://github.com/codingjwp/mindpalace/assets/113403155/90733062-6c16-4771-a7a0-39b8e8b6e7e5">


같은 위치에 있는 같은 컴포넌트이므로 React의 관점에서 보면 같은 `count`

### 주의 사항

**React에서 중요한 것은 JSX 마크업이 아니라 UI 트리에서의 위치라는 것을 기억!!!**

```javascript
const [isFancy, setIsFancy] = useState(false);
if (isFancy) {
  return (<Counter isFancy={true} />)
}
else {
	return (<Counter isFancy={false} />)
}
		
```

state가 재설정될 것으로 예상할 수 있지만 그렇지 않음. 이 **두 `<Counter />` 태그가 모두 같은 위치에 렌더링되기 때문.**

이 두 카운터는 동일한 주소를 갖고 있기 때문에 로직을 어떻게 구성하든 상관없이 이전 렌더링과 다음 렌더링 사이에서 이방법으로 이들을 일치할 수 있음.

## 동일한 위치의 다른 컴포넌트는 state를 초기화

같은 위치에서 서로 다른 컴포넌트 유형 사이를 전환합니다. `Counter` 컴포넌트가 있지만 `p` 를 넣을때 UI트리에서 `Counter` 제거하고 state를 소멸 시킴

<img width="828" alt="delete added 컴포넌트" src="https://github.com/codingjwp/mindpalace/assets/113403155/293aa6f3-1485-4681-914a-ed7b97404964">


**같은 위치에 다른 컴포넌트를 렌더링하면 전체 하위 트리의 state가 재설정됩니다.**

**Add one**  버튼 클릭 후  체크박스 체크시

<img width="805" alt="switch 컴포넌트" src="https://github.com/codingjwp/mindpalace/assets/113403155/14e56b79-4947-4cc2-b1c6-6782d76cd1a0">


경험상 **리렌더링 사이에 state를 유지하려면 트리의 구조가 “일치”해야 함.** 구조가 다르면 React는 트리에서 컴포넌트를 제거할 때 state를 파괴하기 때문.

### 주의 사항

그렇기 때문에 컴포넌트 함수 정의를 중첩해서는 안 됨

```javascript
export default function MyComponent() {
  const [counter, setCounter] = useState(0);

  function MyTextField() {
    const [text, setText] = useState('');

    return (
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
    );
  }

  return (
    <>
      <MyTextField />
      <button onClick={() => {
        setCounter(counter + 1)
      }}>Clicked {counter} times</button>
    </>
  );
}
```

버튼을 클릭할 때마다 입력 state가 사라지는데 이는 `MyComponent`를 렌더링할 때마다 다른 `MyTextField` 함수가 생성되기 때문.

같은 위치에 다른 컴포넌트를 렌더링하기 때문에 React는 아래의 모든 state를 초기화 해버림.

이로 인해 버그와 성능 문제가 발생!! 이 **문제를 방지하려면 항상 컴포넌트 함수를 최상위 수준에서 선언하고 정의를 중첩하지 말 것.**

## 동일한 위치에서 state 재설정 하기

React는 컴포넌트가 같은 위치에 있는 동안 컴포넌트의 state를 보존함. 일반적으로 이것은 사용자가 원하는 것이므로 기본 동작으로 적합함. 

하지만 때로는 컴포넌트의 state를 리셋하고 싶을 때의 방법 두가지

1. 컴포넌트를 다른위치에 렌더링
2. 각 컴포넌트에  key로 명시적인 아이덴티티를 부여

### 옵션 1: 컴포넌트를 다른 위치에 렌더링

```javascript
<div>
      {isPlayerA &&
        <Counter person="Taylor" />
      }
      {!isPlayerA &&
        <Counter person="Sarah" />
      }
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
</div>
```

<img width="933" alt="다른 위치 렌더링" src="https://github.com/codingjwp/mindpalace/assets/113403155/40f90d13-191e-49fc-9b2b-8938c853e6f8">


각 state는 DOM에서 제거될 때 마다 소멸

이 솔루션은 같은 위치에 몇 개의 독립적인 컴포넌트만 렌더링할 때 편리하며 이 예시 경우에는 두 개만 있으므로 JSX에서 두 컴포넌트를 별도로 렌더링하는 것이 번거롭지 않음.

### 옵션 2: key로 state 재설정하기

key를 사용해 React가 모든 컴포넌트를 구분하도록 할 수 있습니다.

기본적으로 React는 부모 내의 순서를 사용해 컴포넌트를 구분하지만 key를 사용하면 이것이 특정 counter임을 React에 알릴 수 있음. 이렇게 할 경우 React는 counter가 트리에 어디에 나타나는 알수 있음.

```javascript
{isPlayerA ? (
	<Counter key="Taylor" person="Taylor" />
) : (
  <Counter key="Sarah" person="Sarah" />
)}
	<button onClick={() => {
	  setIsPlayerA(!isPlayerA);
  }}>
		Next player!
  </button>
```

Taylor와 Sarah 사이를 전환해도 state가 유지되지 않습니다. **서로 다른 `key`를 부여했기 때문**

`key`를 지정하면 React가 부모 내 순서가 아닌 `key` 자체를 위치의 일부로 사용하도록 지시함.

그렇기 때문에 JSX에서 같은 위치에 렌더링하더라도 React의 관점에서 보면 두 카운터는 서로 다른 카운터이며 결과적으로 state를 공유하지 않음.

카운터가 화면에 나타날 때마다 그 state가 생성하며 카운터가 제거될 때마다 그 state는 소멸되어 버림. 

두 카운터 사이를 토글하면 state가 계속 초기화.

key는 전역으로 고유하지 않음.  key는 부모 내에서의 위치만 지정

### 키로 form 재설정 하기

채팅 앱 같은 곳에서는 state를 초기화하는게 좋을 수 있음. 그럴 경우 key를 이용할 수 있음.

```javascript
<Chat key={to.id} contact={to} />
```

### 제거된 컴포넌트에 대한 state 보존

실제 채팅 앱에서는 사용자가 이전 수신자를 다시 선택할 때 입력 state를 복구하고 싶을 때 더 이상 표시되지 않는 컴포넌트의 state를 **살아있게** 유지하는 몇 가지 방법

- 현재 채팅만 렌더링하는 것이 아니라 모든 채팅을 렌더링하되 다른 모든 채팅은 CSS로 숨길 수 있음. 채팅은 트리에서 제거되지 않으므로 로컬 state가 유지되지만 이 솔루션은 간단한 UI에 적합. 하지만 숨겨진 트리가 크고 많은 DOM 노드를 포함하는 경우 속도가 매우 느려질 수 있음.
- 부모 컴포넌트에서 각 수신자에 대한 보류 중인 메시지를 state를 끌어올려서 보관할 수 있음. 이렇게 하면 자식 컴포넌트가 제거되더라도 중요한 정보를 보관하는 것은 부모 컴포넌트이므로 문제가 되지 않으며 이것이 가장 일반적인 해결책입니다.
- React state 외에 다른 소스를 사용할 수도 있음. 예를 들어, 사용자가 실수로 페이지를 닫아도 메시지 초안이 유지되기를 원할 수 있기에 이를 구현하기 위해 `Chat` 컴포넌트가 `[localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)`에서 읽어서 state를 초기화하고 초안도 저장하도록 할 수 있음.

⏮️ Previous : [Sharing State Between Components](./024-리액트%20Sharing%20State%20Between%20Components.md)

⏭️ Next : [Extracting State Logic into a Reducer](./026-리액트%20Extracting%20State%20Logic%20into%20a%20Reducer.md)
