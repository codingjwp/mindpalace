# Referencing Values with Refs

> Ref로 값 참조

컴포넌트가 특정 정보를 기억하도록 하고 싶지만 해당 정보가 새로운 렌더링을 촉발하지 않도록 할려는 경우 `ref`를 사용.

## 배울 내용들

1. 컴포넌트에 ref를 추가하는 방법
2. ref 값을 업데이트하는 방법
3. state와 ref의 차이점
4. ref를 안전하게 사용하는 방법

## 컴포넌트에 ref 추가하기

React에서 `useRef` 훅을 가져와서 컴포넌트에 ref 추가

```typescript
import { useRef } from 'react'
```

컴포넌트 내부에서 `useRef` 훅을 호출 하고 참조할 초기값을 인자로 전달.

`useRef`는 다음과 같은 객체를 반환

```typescript
const ref = useRef(0);
// 반환 객체
{
	current: 0
}
```

`ref.current` 속성을 통해 해당 ref의 현재 값에 액세스할 수 있음. 이 값은 의도적으로 변이가 가능하므로 읽기, 쓰기가 모두 가능

React가 추적하지 않는 컴포넌트의 비밀 주머니와 같음 **(이것이 바로 React의 단방향 데이터 흐름에서 탈출구가 되는 이유)**

```typescript
let ref = useRef(0);

function handleClick() {
  ref.current = ref.current + 1;
  alert('You clicked ' + ref.current + ' times!');
}
```

여기서 ref는 숫자를 가리키고 있지만 `state`와 마찬가지로 문자열, 객체, 함수 등 무엇이든 가리킬 수 있으며 state와 달리 ref는 `current` 속성을 읽고 수정할 수 있는 일반 Javascript 객체임.

**컴포넌트는 ref가 증가할 때마다 리렌더링 되지 않는다**는 점을 유의. `state`와 마찬가지로 ref는 리렌더링 사이에 React에 의해 유지.

렌더링에 정보가 사용되는 경우 해당 정보를 `state`로 유지하고 이벤트 핸들러만 정보를 필요로 하고 변경해도 다시 렌더링할 필요가 없는 경우, `ref`를 사용하는 것이 더 효율적일 수 있음.

## ref와 state의 차이점

| refs | state |
| --- | --- |
| useRef(initialValue)는 { current: initialValue }을 반환 | useState(initialValue)는 state 변수의 현재값과 state 설정자함수([value, setValue])를 반환 |
| 변경 시 리렌더링을 촉발하지 않음 | 변경 시 리렌더링을 촉발함 |
| (Mutable) 렌더링 프로세스 외부에서 current 값을 수정하고 업데이트할 수 있음 | (Immutable) state setting 함수를 사용하여 state 변수를 수정해 리렌더링을 대기열에 추가해야함 |
| 렌더링 중에는 current 값을 읽거나 쓰지 않아야 함 | 언제든지 state를 읽을 수 있음. 각 렌더링에는 변경되지 않는 자체 state snapshot이 있음  |

ref로 구현하면, React는 컴포넌트를 다시 렌더링하지 않으므로 값이 변경되는 것을 볼수 없음.

## ref를 사용해야 하는 경우

일반적으로 ref는 컴포넌트가 React로 부터 외부API, 즉 컴포넌트의 형상에 영향을 주지 않는 브라우저 API 등과 통신해야 할 때 사용함.

1. timeout ID 저장
2. DOM element 저장 및 조작
3. TYPESCRIPT 계산하는 데 필요하지 않은 다른 객체 저장

컴포넌트에 일부 값을 저장해야 하지만 렌더링 로직에는 영향을 미치지 않는 경우 ref를 선택하세요.

## ref 모범 사례

다음 원칙을 따르면 컴포넌트의 예측 가능성을 높일 수 있음.

- **ref를 탈출구로 취급** : 외부 시스템이나 브라우저 API로 작업할때 유용. 애플리케이션 로직과 데이터 흐름의 대부분이 ref에 의존하는 경우 접근 방식을 재고해봐야함.
- **렌더링 중에는 `ref.current`를 읽거나 쓰지 말 것** : 렌더링 중에 일부 정보가 필요한 경우, 대신 `state`를 사용. React는 `ref.current`가 언제 변경되는지 알지 못하기 때문에 렌더링 중에 읽어도 컴포넌트의 동작을 예측하기 어려움.

React state의 제한은 ref에는 적용되지 않음. 예를 들어, state는 모든 렌더링에 대해 스냅샷처럼 작동하며 동기적으로 업데이트되지 않지만 ref의 현재 값을 변이하면 즉시 변경됨.

```typescript
ref.current = 5;
console.log(ref.current); // 5
```

**ref 자체가 일반 Javascript 객체이기 때문** 또한 ref로 작업할 떄 변이를 피하고자 조심할 필요 없음. 변이하는 객체가 렌더링에 사용되지 않는 한, Reacts는 ref나 그 콘텐츠로 무엇을 하든 상관안함.

## Ref와 DOM

ref는 모든 값을 가리킬 수 있으나 ref의 가장 일반적인 사용 사례는 DOM 요소에 엑세스하는 것.

예를 들어 프로그래밍 방식으로 input focus를 맞추고자 할때 유용