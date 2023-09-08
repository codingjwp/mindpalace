# Keeping components pure

> 컴포넌트 순수성 유지

순수 함수는 계산만 수행하고 그 이상은 수행하지 않음.

컴포넌트를 순수 함수로만 엄격하게 작서하면 코드베이스가 커짐에 따라 당황스러운 버그 및 예측할 수 없는 동작을 피해 갈수 있음.

이런한 이점을 얻기 위해 규칙을 준수해야 함.

## 배울 내용들

1. 순수성이란 무엇이며 버그를 방지하는 데 도움이 되는 방법
2. 렌더링 단계에서 변경 사항을 제외하여 컴포넌트를 순수하게 유지하는 방법
3. 엄격한 모드를 사용하여 컴포넌트에서 실수를 찾는 방법

## 공식으로서의 구성요소

[Pure function 순수 함수](https://en.wikipedia.org/wiki/Pure_function)

1. 자기 일에만 신경을 씁니다. 호출되기 전에 존재했던 객체나 변수를 변경하지 않습니다.
2. 동일한 입력, 동일한 출력. 동일한 입력이 주어지면 순수 함수는 항상 동일한 결과를 반환해야 합니다.

```javascript
// 순수 함수
function double(number) {
  return 2 * number;
}
```

React는 작성한 컴포넌가 순수 함수라고 가정합니다. **동일한 입력이 주어지면 항상 동일한 JSX를 반환해야 함을 의미.**

## 순수 함수가 아닌 컴포넌트

외부에 선언된 변수**(guest)**를 읽고 사용. **이 컴포넌트를 여러번 호출 하면 다른 JSX가 생성**

```javascript
let guest = 0;

function Cup() {
  // Bad: changing a preexisting variable!
  guest = guest + 1;
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup />
      <Cup />
      <Cup />
    </>
  );
```

![image](https://github.com/codingjwp/mindpalace/assets/113403155/014571f2-f4fc-402d-a866-ecc151a0c795)  


### props로 전달하여 순수 함수로 변경

```javascript
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup guest={1} />
      <Cup guest={2} />
      <Cup guest={3} />
    </>
  );
```

### StrictMode로 불순한 계산 감지

React에는 렌더링 하는 동안 읽을 수 있는 세가지 종류 입력`(props, state, context)` 가 존재.

이러한 입력은 항상 읽기 전용으로 처리

React는 개발 중에 각 구성 요소의 기능을 두번 호출 하는 **StrictMode**를 제공  

**StrictMode**는 컴포넌트 함수를 **두 번 호출**하여 규칙을 위반하는 컴퍼넌트를 찾는데 도움제공.  

**순수 함수는 계산만 하기 때문에 두 번 호출해도 아무것도 바뀌지 않습니다**.  

**StrictMode**는 프로덕션에 영향을 주지 않으므로 사용자의 앱 속도가 느려지지 않습니다. **StrictMode**를 선택하려면 루트 구성 요소를 `<React.StrictMode>` 작성. 일부 프레임워크에서는 기본적으로 이 작업을 수행함.

## 국소 돌연변이: 구성 요소의 작은 비밀

위에 순수하지 않는 컴포넌트 경우 **돌연변이 라고 부름.**

**하지만 렌더링 하는 동안 방금 생성한 변수와 객체를 변경하는 것은 완전히 괜찮음.**  

```javascript
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaGathering() {
  let cups = [];
  for (let i = 1; i <= 12; i++) {
    cups.push(<Cup key={i} guest={i} />);
  }
  return cups;
}
```

`cups = []` 배열이 `TeaGathering` 외부에 생성 되었다면 문제이나 동일한 렌더링 중 내부에서 생성했기 때문에 괜찮음.

`TeaGathering` 외부의 어떤 코드도 이런 일이 발생했다는 걸 알 수 없으므로 이를 **로컬 돌연번이** 라고 합니다.

## Side Effects를 일으킬 수 있는 경우

함수형 프로그래밍은 순수성에 크게 의존하지만, 언젠가는 어딘가에서 무언가를 바꿔야 함.

**이것이 바로 프로그래밍의 핵심!**.  

화면 업데이트, 애니메이션 시작, 데이터 변경 등 이러한 변경 사항을 **side effects**라고 함.  

렌더링하는 동안이 아니라 "부수적으로" 일어나는 일.

React에서 **side effects**는 보통 이벤트 핸들러 안에 속함.

event 핸들러는 사용자가 어떤 액션을 수행할 때(예를 들어 버튼을 클릭할 때) React가 실행하는 함수.

event 핸들러가 컴포넌트 내부에 정의되어 있더라도 렌더링 중에는 실행되지 않음!

따라서 event 핸들러는 순수할 필요가 없음.

다른 모든 옵션을 다 사용했는데도 **side effects**에 적합한 event 핸들러를 찾을 수 없는 경우에도 컴포넌트에서 `useEffect` 호출을 통해 반환된 JSX에 event 핸들러를 첨부할 수 있음.

이렇게 하면 나중에 렌더링 후 부작용이 허용될 때 React가 이를 실행하도록 지시.

**하지만 이 접근법은 최후의 수단(`useEffect`)**!

가능하면 렌더링만으로 로직을 표현하는 것이 좋음.

### React는 왜 순수성에 관심을 가지는 가

1. 컴포넌트는 서버와 같은 다른 환경에서 실행될 수 있음! 동일한 입력에 대해 동일한 결과를 반환하므로 하나의 컴포넌트가 많은 사용자 요청을 처리할 수 있음.
2. 입력이 변경되지 않은 컴포넌트의 렌더링을 건너뛰면 성능을 향상시킬 수 있음. 순수 함수는 항상 동일한 결과를 반환하므로 캐싱해도 안전.
3. **deep component tree**를 렌더링하는 도중에 일부 데이터가 변경되는 경우, React는 오래된 렌더링을 완료하기 위해 시간을 낭비하지 않고 렌더링을 다시 시작할 수 있음. 순수성를 사용하면 언제든지 계산을 중단해도 안전.

⏮️ Previous: [Rendering Lists](./011-%EB%A6%AC%EC%95%A1%ED%8A%B8%20Rendering%20lists.md)

⏭️ Next: Adding Interactivity
