# Manipulating the DOM with Refs

> ref로 DOM 조작

React는 렌더링 출력과 일치하도록 [DOM](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction)을 자동으로 업데이트하므로 컴포넌트가 자주 조작할 필요가 없음. 

하지만 때로는 노드에 초점을 맞추거나 스크롤하거나 크기와 위치를 측정하기 위해 React가 관리하는 DOM 요소에 접근해야 할 수도 있음. 

React에는 이러한 작업을 수행할 수 있는 빌트인된 방법이 없으므로 DOM 노드에 대한 **ref**가 필요.

## 배울 내용

1. `ref` 어트리뷰트로 React가 관리하는 DOM 노드에 접근하는 방법
2. `ref` TYPESCRIPT 속성이 `useRef` 훅과 관련되는 방법
3. 다른 컴포넌트의 DOM 노드에 접근하는 방법
4. 어떤 경우에 React가 관리하는 DOM을 수정해도 안전한지

## 노드에 대한 ref 가져오기

React가 관리하는 DOM 노드에 접근하려면 아래 코드와 같이 작성

```typescript
import { useRef } from 'react';

const myRef = useRef(null);
<div ref={myRef}></div>
```

이 `useRef`훅은 `current`라고 하는 프로퍼티가 포함된 객체를 반환 처음에는 

`myRef.current`는 `null` 이 됨.

React가 이 `<div>`에 대한 DOM 노드를 생성하면, React는 이 노드에 대한 참조를 `myRef.current`에 넣음. 그런 다음 이벤트 핸들러에서 이 DOM 노드에 액세스하고 여기에 정의된 빌트인 [브라우저 API](https://developer.mozilla.org/docs/Web/API/Element)를 사용할 수 있음.

```typescript
myRef.current.scrollIntoView();
```

DOM 조작이 ref의 가장 일반적인 사용 사례이지만, `useRef` 훅은 timer ID와 같은 다른 것들을 React 외부에 저장하는 데 사용될 수 있음.

state와 유사하게 ref는 렌더링 사이에 유지되며 ref는 state 변수와 비슷하지만 설정할 때 리렌더링을 촉발하지 않음.

### ref 콜백을 사용하여 refs 목록을 관리하는 방법

목록의 각 항목에 대해 얼마나 많은 ref가 필요할지 알 수 없는 경우도 있으며 이런 경우에는 **일반적인 방법은 잘 작동하지 않음**

```typescript
<ul>
  {items.map((item) => {
    // Doesn't work! 작동하지 않습니다!
    const ref = useRef(null);
    return <li ref={ref} />;
  })}
</ul>
```

이는 훅을 **컴포넌트의 최상위 레벨에서만 호출**해야 하기 때문이며 반복문 또는 `map()` 내부에서는 `useRef`를 호출 할 수 없음

이 문제를 해결할 수 있는 방법 중 하나는 부모 엘리먼트에 대해 단일 ref를 가져온 다음 **querySelectorAll과 같은 DOM 조작 메서드를 사용하여 개별 하위 노드를 찾는 것**. 하지만 이 방법은 DOM 구조가 변경되면 깨질 수 있음.

또 다른 해결책은 **`ref` 속성에 함수를 전달**하는 것. 이를 `ref` 콜백이라고 합니다. React는 ref를 설정할 때가 되면 DOM 노드로, 지울 때가 되면 `null`로 ref 콜백을 호출. 이를 통해 자신만의 배열이나 [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)을 유지 관리하고, 인덱스나 일종의 ID로 모든 ref에 접근할 수 있음.

```typescript
import { useRef } from 'react';

export default function CatFriends() {
  const itemsRef = useRef(null);

  function scrollToId(itemId) {
    const map = getMap();
    const node = map.get(itemId);
    node.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function getMap() {
    if (!itemsRef.current) {
      itemsRef.current = new Map();
    }
    return itemsRef.current;
  }

  return (
    <>
      <nav>
        <button onClick={() => scrollToId(0)}>
          Tom
        </button>
        <button onClick={() => scrollToId(5)}>
          Maru
        </button>
        <button onClick={() => scrollToId(9)}>
          Jellylorum
        </button>
      </nav>
      <div>
        <ul>
          {catList.map(cat => (
            <li
							key={cat.id} 
							ref={(node) => {
                const map = getMap();
                if (node) {
                  map.set(cat.id, node);
                } else {
                  map.delete(cat.id);
                }
              }}
            >
              <img
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catList = [];
for (let i = 0; i < 10; i++) {
  catList.push({
    id: i,
    imageUrl: 'https://placekitten.com/250/200?image=' + i
  });
}
```

이 예제에서 `itemsRef`는 단일 DOM 노드를 보유하지 않음.

대신 항목 ID에서 DOM 노드로의 [Map](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Map)을 보유. 모든 목록 항목의 `ref` 콜백은 맵을 업데이트함

## 다른 컴포넌트의 DOM 노드에 접근

여러분이 만든 컴포넌트 예를 들어 <MyInput />과 같은 컴포넌트에 ref를 넣으려고하면 기본적으로 null이 반환됩니다. 

문제를 알아차리는데 도움이 되도록 React는 콘솔에 오류를 출력

기본적으로 React가 컴포넌트가 다른 컴포넌트의 DOM 노드에 접근하는 것을 허용하지 않기 때문. 심지어 자신의 자식에게도요! 이것은 의도적인 것이며 ref는 탈출구이기 때문에 아껴서 사용해야 함. 

다른 컴포넌트의 DOM 노드를 수동으로 조작하면 코드가 훨씬 더 취약해짐.

대신 DOM노드를 노출하길 원하는 컴포넌트에 해당 동작을 **설정**해야 함. 컴포넌트는 자신의 ref를 자식 중 하나에 전달 하도록 지정할 수 있음.

`MyInput`이 `forwardRef` API를 사용하는 방법은 아래 코드와 같음

```typescript
const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});
```

### 작동 방식

1. `<MyInput ref={inputRef} />`는 React에게 해당 DOM 노드를 `inputRef.current`에 넣으라고 지시하지만 이를 선택할지는 `MyInput` 컴포넌트에 달려 있으며 **기본적으로는 ref를 전달하지 않음**.
2. `MyInput` 컴포넌트를 `forwardRef`를 사용하여 선언하면, `props` 다음의 **두 번째 `ref` 인수**에 위의 `inputRef`를 받도록 설정됨.
3. `MyInput`은 수신한 `ref`를 내부의 `<input>`으로 전달됨.

디자인 시스템에서 버튼, 입력 등과 같은 저수준 컴포넌트는 해당 ref를 DOM 노드로 전달하는 것이 일반적인 패턴.

반면 양식, 목록 또는 페이지 섹션과 같은 상위 수준 컴포넌트는 일반적으로 DOM 구조에 대한 우발적 의존성을 피하기 위해 해당 DOM 노드를 노출하지 않음.

### 명령형 핸들로 API의 하위 집합 노출하기

`MyInput`은 원본 DOM input 엘리먼트를 노출하고 이를 통해 부모 컴포넌트가 이 요소에 `focus()`를 호출할 수 있음.

그런데 이렇게 하면 부모 컴포넌트가 **다른 작업(예: CSS 스타일 변경)**을 할 수있으며 노출되는 기능을 제한하고 싶을 수도 있음.

그럴 땐 `useImperativeHandle`을 사용.

```typescript
import { forwardRef,  useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef((props, ref) => {
  const realInputRef = useRef(null);
  useImperativeHandle(ref, () => ({
    focus() { realInputRef.current.focus(); },
  }));
  return <input {...props} ref={realInputRef} />;
});

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

여기서 `MyInput` 내부의 `realInputRef`는 실제 input DOM 노드를 보유. 

하지만 `useImperativeHandle`은 부모 컴포넌트에 대한 ref 값으로 고유한 특수 객체를 제공하도록 React에 지시.

`Form` 컴포넌트 내부의 `inputRef.current`에는 `focus` 메서드만 존재.

이 경우 ref 핸들은 DOM 노드가 아니라 `useImperativeHandle()` 내부에서 생성한 사용자 정의 객체가 됨.

## React가 ref를 첨부할 때

React에서 모든 업데이는 두 단계로 나뉘어짐

- **렌더링** 하는 동안 React는 컴포넌트를 호출하여 화면에 무엇이 표시되어야 하는지 파악
- **커밋** 하는 동안 React는 DOM에 변경 사항 적용

일반적으로 렌더링 중에는 ref에 액세스하는 것을 원하지 않을 것이며 DOM 노드를 보유하는 ref도 마찬가지.

React는 커밋하는 동안에 `ref.current`를 설정하며 React는 DOM이 업데이트 되기 전에는 `ref.current`의 값을 `null`로 설정하였다가, DOM이 업데이트된 직후 해당 DOM 노드로 다시 설정.

**일반적으로 이벤트 핸들러에서 ref에 접근하며** ref로 무언가를 하고 싶지만 그 작업을 수행할 특정 이벤트가 없다면, Effect가 필요할 수 있음.

### 플러싱 state는 flushSync와 동기식으로 업데이트

새 할일ㅇ르 추가하고 목록 마지막 항목으로 화면을 아래로 스크롤하는 코드에서 어떤 이유인지 항상 마지막으로 추가한 할일 바로 앞 내용으로 스크롤이 됨.

문제의 두줄

```typescript
setTodos([ ...todos, newTodo]);
listRef.current.lastChild.scrollIntoView();
```

React에서는 state 업데이트가 큐에 등록됨.

그러나 여기서는 `setTodos`가 DOM을 즉시 업데이트하지 않기 때문에 문제가 발생. 따라서 목록을 마지막 요소로 스크롤할 때 할 일이 아직 추가되지 않은 상태이며 이것이 스크롤이 항상 한 항목씩 “뒤처지는” 이유입니다.

이 문제를 해결하기 위해 React가 DOM을 동기적으로 업데이트(`flush`)하도록 강제할 수 있으며 `react-dom`에서 `flushSync`를 import하고 **state 업데이트를** `flushSync` 호출로 **감싸면** 됨.

```typescript
import { flushSync } from 'react-dom';

flushSync(() => {
  setTodos([ ...todos, newTodo]);
});
listRef.current.lastChild.scrollIntoView();
```

이렇게 하면 `flushSync`로 감싼 코드가 실행된 직후 React가 DOM을 동기적으로 업데이트하도록 지시하며 그 결과 스크롤을 시도할 때 DOM에 이미 마지막 할 일이 존재함.

```typescript
function handleAdd() {
  const newTodo = { id: nextId++, text: text };
  flushSync(() => {
    setText('');
    setTodos([ ...todos, newTodo]);      
  });
  listRef.current.lastChild.scrollIntoView({
    behavior: 'smooth',
    block: 'nearest'
  });
}
```

## ref를 이용한 DOM 조작 모법 사례

Ref는 탈출구. **React 외부로 나가야** 할 때만 사용. 일반적인 예로는 초점을 맞추거나, 스크롤 위치를 관리하거나 React가 노출하지 않는 브라우저 API를 호출하는 기능에 사용.

포커스나 스크롤 같은 비파괴적 동작을 고수한다면 문제는 발생하지 않지만 DOM을 수동으로 수정하려고 하면 React가 수행하는 변경 사항과 충돌할 위험이 존재.

다음 예시는 이 문제를 설명하기 위한 환영 메시지와 두 개의 버튼이 포함.

첫 번째 버튼은 React에서 일반적으로 사용하는 것 처럼 **조건부 렌더링**과 **state**를 사용하여 그 존재 여부를 전환.

두 번째 버턴은 `remove()` DOM API를 사용하여 React가 제어할 수 없는 DOM에서 강제로 제거

```typescript
import { useState, useRef } from 'react';

export default function Counter() {
  const [show, setShow] = useState(true);
  const ref = useRef(null);

  return (
    <div>
      <button
        onClick={() => {setShow(!show);}}>
        Toggle with setState
      </button>
      <button
        onClick={() => { ref.current.remove();}}>
        Remove from the DOM
      </button>
      {show && <p ref={ref}>Hello world</p>}
    </div>
  );
}
```

DOM 엘리먼트를 수동으로 제거한 후 `setState`를 사용하여 다시 표시하려고 하면 충돌이 발생.

이는 사용자가 DOM을 변경했고 React가 이를 계속 올바르게 관리하는 방법을 모르기 때문에.

**React가 관리하는 DOM 노드를 변경하지 말기.** 위와 같이 일관성 없는 시각적 결과나 충돌이 발생할 수 있음.

그렇다고 전혀 할 수 없는건 아니고 **React가 업데이트할 이유가 없는 DOM의 일부는 안전하게 수정할 수 있음.**