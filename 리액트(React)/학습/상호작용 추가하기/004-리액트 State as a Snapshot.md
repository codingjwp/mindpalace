# State as a Snapshot

> 스탭샷으로서의 state

**state** 변수는 읽고 쓸 수 있는 일반 Javascript 변수 처럼 보일 수 있지만 **state**는 **스냅샷**처럼 동작하며 **state** 변수를 설정해도 이미 가지고 있는 **state** 변수는 변경되지 않고 대신 **리렌더링**이 실행됨.

## 배울 내용들

1. state 설정으로 리렌더링이 촉발되는 방식
2. state 업데이트 시기 및 방법
3. state를 설정한 직후에 state가 업데이트되지 않는 이유
4. 이벤트 핸들러가 state의 스냅샷에 엑세스 하는 방법

## **state를 설정하면 렌더링이 촉발**

클릭과 같은 사용자 이벤트에 반응하여 사용자 인터페이스가 직접 변경된다고 생각 할 수 있지만 React에서는 이 멘탈 모델과는 다르게 작동.

이전 페이지에서 state를 설정하면 React에 리렌더링을 요청하는 것을 확인 했으며 인터페이스가 이벤트에 반응할려면 state를 업데이트 해야함.

## 렌더링은 그 시점의 스냅샷을 찍음

렌더링은 React가 컴포넌트, 즉 함수를 호출한다는 뜻.

JSX는 시간상 UI의 스냅샷과 같음. prop, 이벤트 핸들러, 로컬 변수는 모두 **렌더링 당시의 state를 사용해** 계산됨.

사진과 동영상 프레임과 달리 반환하는 UI 스냅샷은 **대화형**

input에 대한 응답으로 어떤 일이 일어날지 지정하는 이벤트 핸들러와 같은 로직이 포함

React에서는 이 스탭샷과 일치하도록 화면을 업데이트하고 이벤트 핸들러를 연결

### React가 컴포넌트를 다시 렌더링

1. React가 함수를 다시 호출
2. 함수가 새로운 JSX 스냅샷을 반환
3. 그러면 React가 반환한 스냅샷과 일치하도록 화면을 업데이트

![컴포넌트 렌더링](https://github.com/codingjwp/mindpalace/assets/113403155/42cb4b72-1063-4da3-bfea-697e6e642f56)

출처 [react 공식문서](https://react.dev/learn/state-as-a-snapshot)

컴포넌트 메모리로서 state는 함수가 반환된 후 사라지는 일반 변수와는 다름.

state는 실제로 함수 외부에 마치 선반에 있는 것 처럼 React 자체에 **존재**

React가 컴포넌트를 호출하면 특정 렌더링에 대한 state의 스탭샷을 제공. 컴포넌트는 **해당 렌더링의 state 값을 사용해** 계산된 새로운 props 세트와 이벤트 핸들러가 포함된 UI의 스냅샷을 JSX에 반환

![특정 렌더링 스냅샷](https://github.com/codingjwp/mindpalace/assets/113403155/fd34dcb4-ffe9-4b69-b45f-2454f189c99e)

출처 [react 공식문서](https://react.dev/learn/state-as-a-snapshot)

아래 코드는 **+3 버튼** 클릭시 어떻게 작동할까?

```javascript
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  )
}
```

결론: 1만 증가 한다.

**state를 설정하면 다음 렌더링에 대해서만 변경됨.** 첫 번쨰 렌더링에서 number는 `0` 따라서 `onClick` 핸들러에서 `setNumber(number + 1)`가 호출된 후에도 number의 값은 여전히 `0`

1. `setNumber(number + 1)`: `number`는 `0`이므로 `setNumber(0+1)` 
    - React는 다음 렌더링에서 number을 1로 변경할 준비.

이 내용을 `setNumber` 3개 만큼 반복. 즉 `0`인 state를 `1`로 3번 설정

## 시간 경과에 따른 state

```javascript
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        alert(number);
      }}>+5</button>
    </>
  )
}
```

**state 변수의 값**은 이벤트 핸들러의 코드가 비동기적이라도 **렌더링 내에서 절대 변경되지 않습니다.**

**React는 하나의 렌더링 이베트 핸들러 내에서 state 값을 고정으로 유지합니다.** 코드가 실행되는 동안 state가 변경되었는지 걱정할 필요가 없음.

⏮️ Previous: [Render and Commit](./003-리액트%20Render%20and%20Commit.md)

⏭️ Next: [Queueing a Series of State Updates](./005-리액트%20Queueing%20a%20Series%20of%20State%20Updates.md)
