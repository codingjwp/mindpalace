# State: A Component’s Memory

> State: 컴포넌트의 메모리

컴포넌트는 현재 입력값, 현재 이미지, 장바구니와 같은 것을 **기억** 해야하는데 React에서는 이러한 종류를 컴포넌트별 메모리를 `**state**`라고 부릅니다.

## 배울 내용

1. useState 훅으로 state 변수를 추가 하는 방법
2. useState 훅이 반환하는 값의 쌍
3. state 변수를 두 개 이상 추가하는 방법
4. state를 지역적이라고 하는 이유

## 일반 변수로 충분하지 않는 경우

`handleClick` 이벤트 핸들러가 지역 변수 `index`를 업데이트 하고 있지만 두 가지 이유로 변경 사항이 표시 되지 않음.

1. 지역 변수는 렌더링간 유지 되지 않음. : React는 컴포넌트를 두 번쨰로 렌더링할 때 지역 변수에 대한 변경 사항을 고려하지 않고 처음부터 렌더링 함.
2. 지역 변수를 변경해도 렌더링을 발동시키지 않음 : React는 새로운 데이터로 컴포넌트를 다시 렌더링 해야한다는 것을 인식 못함.

```javascript
export default function Gallery() {
  let index = 0;
  function handleClick() {
    index = index + 1;
  }
  return (
    <>
      <button onClick={handleClick}>
        Next
      <p>  
        ({index + 1} of test)
      </p>
  );
}
```

컴포넌트를 새 데이터로 업데이트하려면 두 가지 작업이 필요.

1. 렌더링 사이에 데이터를 유지.
2. 새로운 데이터로 컴포넌트를 **렌더링(리렌더링)**하도록 React를 **촉발** 함

`useState` 훅은 이 두 가지를 제공

1. 렌더링 사이를 유지하기 위한 **state 변수**
2. 변수를 업데이트하고 React가 컴포넌트를 다시 렌더링 하도록 촉발하는 **state 설정자 함수**

## state 변수 추가

```javascript
import { useState } from 'react';

const [index, setIndex] = useState(0);
```

`index`는 `state` 변수, `setIndex`는 설정자 함수

여기서 `[`와`]` 구문을 [배열 구조 분해](https://ko.javascript.info/destructuring-assignment)라고 하여 배열에서 값을 읽을 수 있음.

`useState`가 반환하는 배열에는 항상 정확히 두 개의 항목이 존재.

```javascript
// setIndex 사용 방법중 하나
function handleClick() {
 setIndex(index + 1);
}
```

### 첫 번째 훅 만나기

React에서 `useState` 를 비롯해 `use` 로 시작하는 다른 함수를 훅(hook)이라 부름

훅은 React가 **렌더링** 중일 때만 사용할 수 있는 특별한 함수.

### 주의할 점

훅은 컴포넌트 **최상위 레벨(최상위 컴포넌트가 아님)**또는 **커스텀 훅**에서만 호출 할수 있음.

조건문, 반복문 또는 기타 중첩된 함수 내부에서는 훅을 호출할 수 없음.

훅은 함수이지만 컴포넌트의 필요에 대한 무조건적인 선언이라고 생각할 수있음.

### useState 해부하기

`useState` 를 호출하는 것은 React에게 이 컴포넌트가 무언가를 기억하기를 원한다고 말하는 것.

useState는 `[something, setSomething]` 와 같이 지정하는 것이 일반적. 원하는대로 이름을 지을 수 있지만 규칙을 정하면 프로젝트 전반에서 이해가 쉬어짐.

useState의 유일한 인수는 state 변수의 **초기값.** 

`useState(0)` 이 경우 초기값이 `0` 으로 설정.

컴포넌트가 렌더링될 때마다 useState는 두 개의 값을 포함하는 배열을 제공.

1. 저장한 값을 가진 **state 변수**(`index`).
2. state 변수를 업데이트하고 React가 컴포넌트를 다시 렌더링하도록 촉발할 수 있는 **state 설정자 함수** (`setIndex`).

```javascript
const [index, setIndex] = useState(0);
```

**컴포넌트가 처음 렌더링**되면 `index` 초기값으로 `0`가 `useState`에 전달했으므로 `[0, setIndex]`를 반환

`setIndex(index + 1)`로 **state를 업데이트**.

**컴포넌가 두 번째로 렌더링**되지만 React는 여전히 `useState(0)`을 보지만 `index`를 1로 설정한 것을 기억하고 `[1, setIndex]`를 반환.

## 컴포넌트에 여러 state 변수 지정

여러개의 **state**변수를 가질 수 있으면 서로 연관이 없는 경우 여러 개의 **state** 변수를 갖는 것이 좋으나 두개의 **state**변수를 자주 함께 변경하는 경우 두 변수를 함치는게 더 좋을 수 있음.

## State는 격리되고 프라이빗

**state**는 화면 컴포넌트 인스턴스에 지역적.

**동일한 컴포넌트를 두 군데에서 렌더링하면 각 사본은 완전히 격리된 state를 갖게 되며** 이 중 하나를 변경하여도 다른 컴포넌트에는 영향을 미치지 못함

이것이 모듈 상단에 선언하는 일반 변수와 **state**의 차이점

**state**는 특정 함수 호출에 묶이지 않고 코드의 특정 위치에 묶이지도 않지만 화면상의 특정 위치에 지역점

**props와 달리** **state는 이를 선언하는 컴포넌트 외에는 완전히 비공개이며, 부모 컴포넌트는 이를 변경할 수 없음**

두개의 컴포넌트의 state를 동기화 할려면 가장 가까운 공유 부모 컴포넌트에서 state를 추가하고 props를 자식 컴포넌트로 전달 하면 됨.
