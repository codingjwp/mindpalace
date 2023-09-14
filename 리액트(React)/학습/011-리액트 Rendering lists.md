# Rendering lists

> 렌더링 목록

데이터 컬렉션에서 여러 유사한 구성요소를 표시 할 려는 경우 Javascript 배열 메소드를 사용하여 데이터 배열을 조작할 수 있음.

## 배울 내용

- JavaScript를 사용하여 배열에서 구성 요소를 렌더링하는 방법`map()`
- JavaScript를 사용하여 특정 구성요소만 렌더링하는 방법`filter()`
- React 키를 사용하는 시기와 이유

## 배열에서 데이터 렌더링

다양한 데이터를 사용하여 동일한 컴포넌트에 여러 인스턴스를 표시해야 할 경우

1. 데이터를 배열로 이동.
2. 새로운 JSX 노드 배열로 매핑
3. 래핑된 컴포넌트 적용

```javascript
const people = [
  'Creola Katherine Johnson: mathematician',
  'Mario José Molina-Pasquel Henríquez: chemist',
  'Mohammad Abdus Salam: physicist',
  'Percy Lavon Julian: chemist',
  'Subrahmanyan Chandrasekhar: astrophysicist'
];

export default function List() {
  const listItems = people.map(person =>
    <li>{person}</li>
  );
  return <ul>{listItems}</ul>;
}
```

## 항목 배열 필터링

필요한 항목이 포함된 것만 반환 받아 적용.

1. 필터링 하여 새로운 배열 만들기 `filter()`
2. 매핑 하기.
3. 컴포넌트를 반환

```javascript
import { people } from './data.js';
import { getImageUrl } from './utils.js';

export default function List() {
  const chemists = people.filter(person =>
    person.profession === 'chemist'
  );
  const listItems = chemists.map(person =>
    <li>
      <img
        src={getImageUrl(person)}
        alt={person.name}
      />
      <p>
        <b>{person.name}:</b>
        {' ' + person.profession + ' '}
        known for {person.accomplishment}
      </p>
    </li>
  );
  return <ul>{listItems}</ul>;
}
```

### 화살표 함수 사용 시 배열 메소드

화살표 함수는 암시적으로 바로 뒤 표현식을 반환 함으로 `return` 이 필요 하지 않습니다.

```javascript
const listItems = chemists.map(person =>
  <li>...</li> // Implicit return!
);
```

하지만 중괄호`{}` 로 감싸져 있을 경우 명시적으로 `return`를 작성해야 합니다.

```javascript
const listItems = chemists.map(person => { // Curly brace
  return <li>...</li>;
});
```

## key 목록 항목을 순서대로 유지

잘 선택된 키는 React가 정확히 무슨 일이 일어났는지 추론하고 DOM 트리를 올바르게 업데이트하는 데 도움이 됩니다.

```javascript
const listItems = people.map(person =>
    <li key={person.id}>
      <img
        src={getImageUrl(person)}
        alt={person.name}
      />
      <p>
        <b>{person.name}</b>
          {' ' + person.profession + ' '}
          known for {person.accomplishment}
      </p>
    </li>
  );
```

### 각 목록 항목에 여러개의 DOM 노드 표시 할 경우

`<>…</>` 경우 키를 전달 할수  없으므로 `<div>` 그룹화 하거나 `<Fragment>`을 사용

```javascript
import { Fragment } from 'react';

// ...

const listItems = people.map(person =>
  <Fragment key={person.id}>
    <h1>{person.name}</h1>
    <p>{person.bio}</p>
  </Fragment>
);
```

### Key 규칙 및 소스

key는 데이터페이스의 고유한 데이터 베이스 key/ID를 사용

또는 로컬에서 생성된 uuid, 증분 카운터나 패키지 사용.

**키는 형제 간에 고유.** ( 다른 배열의 JSX 노드에 동일한 키를 사용하는 것은 괜찮음)

**키는 변경되어서는 안됨.** (키의 목적이 무산됩니다).

## React에 키가 필요한 이유는 무엇?

키를 사용하면 형제 항목 간에 항목을 고유하게 식별.

재정렬로 인해 위치가 변경되더라도 키는 React가 수명 내내 해당 항목을 식별.

### key를 map에 주어진 index 및 랜덤 값을 사용할 경우

배열에서 항목의 index를 key로 사용하고 싶을 수도 있겟지만 실제로 key를 전혀 지정하지 않으면 React는 이를 사용함. 그러나 항목이 삽입되거나 삭제되거나 배열의 순서가 바뀌면 시간이 지남에 따라 항목을 렌더링하는 순서가 변경되어버림. index를 key로 사용하면 종종 미묘하고 혼란스러운 버그가 발생.

마찬가지로 `key={Math.random()}`과 같이 즉석에서 key를 생성하지 말 것. 이렇게 하면 렌더링 간에 key가 일치하지 않아 모든 컴포넌트와 DOM이 매번 다시 생성될 수 있음. 속도가 느려질 뿐만 아니라 목록 항목 내부의 사용자 입력도 손실. 대신 **데이터에 기반한 안정적인 ID를 사용.**

컴포넌트는 key를 소품으로 받지 않는다는 점에 유의하세요. React 자체에서 힌트로만 사용됩니다. 컴포넌트에 ID가 필요한 경우 별도의 props로 전달해야 합니다. `<Profile key={id} userId={id} />`

⏮️ Previous : [Conditional Rendering](./010-%EB%A6%AC%EC%95%A1%ED%8A%B8%20Conditional%20rendering.md)

⏭️ Next : [Keeping Components Pure](./012-%EB%A6%AC%EC%95%A1%ED%8A%B8%20Keeping%20components%20pure.md)
