# Choosing the State Structure

> state 구조 선택 

state를 잘 구조화하면 수정과 디버깅이 편한 컴포넌트와 버그가 끊임 없이 바생하는 컴포넌트의 차이를 만들수 있음.

## 배울 내용들

1. 단일 state 변수를 사용해야하는 경우 vs 다중 state 변수를 사용해야 하는 경우
2. state 정리시 피해야할 사항
3. state 구조와 관련된 일반적인 문제를 해결하는 방법

## state 구조화 원칙

state를 보유하는 컴포넌트를 작성할 때엔 얼마나 많은 state변수를 사용할지 데이터의 모양은 어떻게 할지에 대해 선택해야 함. 최적화되지 않은 state 구조를 사용하더라도 올바른 프로그램을 작성할 수 있지만. 더 나은 선택을 할 수 있도록 몇 가지 원칙이 존재.

1. **관련 state를 그룹화** :  항상 두개 이상의 state변수를 동시에 업데이트 하는 경우 단일 state 변수로 병합하는 것이 좋음
2. **state의 모순을 피하기** : 여러 state조각이 서로 모순되거나 불일치 할 수 있는 방식으로 state 구성하면 실수가 발생할 여지가 생김
3. **불필요한 state를 피하기** : 렌더링 중 컴포넌가 props나 기존 state 변수에서 일부 정보를 계산할 수 있다면 해당 컴포넌트의 state에 넣지 않아야 함
4. **state 중복을 피하기** : 동일한 데이터가 여러 state 변수 간에 또는 중첩된 객체 내에 중복되면 동기화 state를 유지하기 어려움.
5. **깊게 중첩된 state를 피하기** : 깊게 계층화된 state는 업데이트 하기 쉽지 않기 때문에 평평한 방식으로 구성하는것이 좋음

state에서 불필요하거나 중복된 데이터를 제거하면 모든 데이터가 동기화 상태를 유지하는 데 도움됨.

> **state를 최대한 단순하게 만들되, 그보다 더 단순해서는 안 됩니다.**
> 

## 관련 state 그룹화

**두 개의 state 변수가 항상 함께 변경되는 경우에는 하나의 state 변수로 통합하는 것이 좋음.**

데이터를 객체나 배열로 그룹화하는 또 다른 경우는 필요한 state의 조각 수를 모를 때입니다.

```javascript
const [position, setPosition] = ({x: 0, y: 0});
```

### 주의 사항

state 변수가 객체인 경우 다른 필드를 명시적으로 복사하지 않고는 그 안의 한 필드만 업데이트할 수는 없다. 예를 들어, 위 예제에서는 `y` 속성이 전혀 없기 때문에 `setPosition({ x: 100 })`을 수행할 수 없지만 대신 `x`만 설정하려면 `setPosition({ ...position, x: 100 })`을 수행하거나 두 개의 state 변수로 분할하여 `setX(100)`을 수행해야함.

## state의 모순 피하기

`isSending` 및 `isSent` state 변수가 있는 호텔 피드백 양식

```javascript
const [text, setText] = useState('');
const [isSending, setIsSending] = useState(false);
const [isSent, setIsSent] = useState(false);
```

해당 코드는 **불가능한** state의 설정을 허용. 

`setIsSent`와 `setIsSending` 중 하나만 호출 하면 `isSending`과 `isSent`가 동시에 `true` 가 되는 상황

**`isSending`과 `isSent`는 동시에 `true`가 되어서는 안되므로, 세 가지 유효한 state 중 하나를 취할 수 있는 `status` 라는 state변수 하나로 대체해야 함.** `status`는 `typing`(초기값), `sending`, `sent`

```javascript
const [text, setText] = useState('');
const [status, setStatus] = useState('typing');

async function handleSubmit(e) {
    e.preventDefault();
    setStatus('sending');
    await sendMessage(text);
    setStatus('sent');
  }

const isSending = status === 'sending';
const isSent = status === 'sent';
```

## 불필요한 state를 피하기

렌더링 중에 컴포넌트의 props나 기존 state 변수에서 **일부 정보를 계산**할 수 있는 경우 해당 정보를 컴포넌트의 state에 **넣지 않아야함.**

### props를 state에 그대로 미러링 하지 말것

중복 state의 일반적 예시

```javascript
function Message({ messageColor }) {
  const [color, setColor] = useState(messageColor);
	// ... 생략
}
```

여기서 `color` state 변수는 `messageColor` props로 초기화 됨.

문제는 **부모 컴포넌트가 나중에 다른 `messageColor` 값(예: `blue` 대신 `red`)을 전달하면 `color` state 변수가 업데이트되지 않는다.** state는 첫 번째 렌더링 중에만 초기화 됨.

그렇기 때문에 state 변수에 일부 prop을 미러링하면 혼란을 초래. 대신 코드에 `messageColor` prop을 직접 사용

```javascript
const color = messageColor;
```

props를 state로 ‘미러링’하는 것은 특정 prop에 대한 모든 업데이트를 무시하려는 경우에만 의미가 있음.

관례에 따라 prop 이름을 `initial` 또는 `default`로 시작하여 새 값이 무시됨을 명확히 할 필요가 있음.

```javascript
function Message({ initialColor }) {
  // `color` 상태 변수는 `initialColor`의 *첫 번째* 값을 보유합니다.
  // `초기색깔` 프로퍼티에 대한 추가 변경은 무시됩니다.
  const [color, setColor] = useState(initialColor);
```

## state 중복 피하기

```javascript
const initialItems = [
  { title: 'pretzels', id: 0 },
  { title: 'crispy seaweed', id: 1 },
  { title: 'granola bar', id: 2 },
];

export default function Menu() {
  const [items, setItems] = useState(initialItems);
  const [selectedItem, setSelectedItem] = useState(
    items[0]
  );
	// ... 생략
} 
```

`**selectedItem`의 내용이 `items` 목록 내 항목 중 하나와 동일한 객체,** 즉 항목 자체에 대한 정보가 두 곳에서 중복

변경 내용 

```javascript
const [items, setItems] = useState(initialItems);
const [selectedId, setSelectedId] = useState(0);

const selectedItem = items.find(item =>
    item.id === selectedId
);
```

`setItems`가 리렌더링을 촉발하고 `items.find(...)`가 업데이트된 제목의 항목을 찾기 때문. 선택한 ID만 필수적이므로 선택한 *항목*을 state로 유지할 필요가 없으며 나머지는 렌더링 중에 계산할 수 있습니다.

## 깊게 중첩된 state는 피하기

**state가 너무 깊게 중첩되어 업데이트하기 어려운 경우 “flat”하게 만드는 것을 고려해보기.** 

다음은 이 데이터를 재구성할 수 있는 한 가지 방법.

- 각 `place`가 하위 place의 배열을 갖는 트리 구조 대신, 각 place가 자식 `place ID`의 배열을 보유하도록 할 수 있음
- 다음 각 `place ID`에서 해당 place로의 매핑을 저장.

이 데이터 재구성은 데이터베이스 테이블을 떠올리게 할 수 있음.

```javascript
export const initialTravelPlan = {
  0: {
    id: 0,
    title: '(Root)',
    childIds: [1, 43, 47],
  },
  1: {
    id: 1,
    title: 'Earth',
    childIds: [2, 10, 19, 27, 35]
  },
  2: {
    id: 2,
    title: 'Africa',
    childIds: [3, 4, 5, 6 , 7, 8, 9]
  }, 
  3: {
    id: 3,
    title: 'Botswana',
    childIds: []
  },
 // ... 생략
}
```

**state가 “flat”(“정규화”라고도 함)해졌으므로 중첩된 항목을 업데이트하는 것이 더 쉬워짐.**

두 단계의 state만 업데이트.

- 부모 장소의 업데이트 된 버전은 제거된 ID를 childIds 배열에서 제외
- 루트 테이블 객체의 업데이트된 버전에는 상위 위치 업데이된 버전이 포함되어야 함

```javascript
import { useState } from 'react';
import { initialTravelPlan } from './places.js';

export default function TravelPlan() {
  const [plan, setPlan] = useState(initialTravelPlan);

  function handleComplete(parentId, childId) {
    const parent = plan[parentId];
    const nextParent = {
      ...parent,
      childIds: parent.childIds
        .filter(id => id !== childId)
    };
    setPlan({
      ...plan,
      [parentId]: nextParent
    });
  }

  const root = plan[0];
  const planetIds = root.childIds;
  return (
    <>
      <h2>Places to visit</h2>
      <ol>
        {planetIds.map(id => (
          <PlaceTree
            key={id}
            id={id}
            parentId={0}
            placesById={plan}
            onComplete={handleComplete}
          />
        ))}
      </ol>
    </>
  );
}

function PlaceTree({ id, parentId, placesById, onComplete }) {
  const place = placesById[id];
  const childIds = place.childIds;
  return (
    <li>
      {place.title}
      <button onClick={() => {
        onComplete(parentId, id);
      }}>
        Complete
      </button>
      {childIds.length > 0 &&
        <ol>
          {childIds.map(childId => (
            <PlaceTree
              key={childId}
              id={childId}
              parentId={id}
              placesById={placesById}
              onComplete={onComplete}
            />
          ))}
        </ol>
      }
    </li>
  );
}
```

### 메모리 사용량 개선하기

이상적으로 테이블 객체에서 삭제된 항목도 제거하여 메모리 사용량을 개선하는 것이 좋음.

⏮️ Previous : [Reacting to Input with State](./001-리액트%20Reacting%20to%20Input%20with%20State.md)

⏭️ Next : [Sharing State Between Components](./003-리액트%20Sharing%20State%20Between%20Components.md)
