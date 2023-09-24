# Updating Objects in State

> 객체 state 업데이트

state는 객체를 포함해서, 어떤 종류의 Javascript 값이든 저장할 수 있음.

하지만 React state에 있는 객체를 직접 변이해서는 안됨.

대신 객체를 업데이트 하려면 새 객체를 생성하거나 복사본을 만들어 해당 복사본을 사용하도록 state를 설정

## 배울 내용들

- React state에서 객체를 올바르게 업데이트 하는 방법.
- 중첩된 객체를 변이하지 않고 업데이트하는 바법
- 불변성이 무엇이며 불변성을 깨뜨리지 않는 방법
- Immer로 객체 복사를 덜 반복적으로 만드는 방법

## 변이란 무엇인가?

지금까지 숫자, 문자열, 불리언값으로 작업을 했으나 이러한 종류의 Javascript 값은 **불변,** 즉 변이할 수 없거나 읽기 전용 입니다.(다시 렌더링을 촉발하여 값을 바꿀 수 있음)

```javascript
setx(5);
```

`x` state가 `0`에서 `5`로 변경 되었지만 숫자 `0` 자체는 변경되지 않았고 Javascript에서는 숫자, 문자열, 분리언과 같은 빌트인 원시 자료형 값을 변경할 수 없음.

```javascript
const [position, setPosition] = useState({x: 0, y: 0});
position.x = 5;
```

기술적으로 객체 자체의 내용을 변경하는 것은 가능하며 **이를 변이라고 합니다.**

React state의 객체는 기술적으로 변이할 수 있지만 **불변하는 것 처럼 취급**해야 하며 객체를 직접 변이하는 대신 항상 교체해야함

## state를 읽기 전용으로 취급

**state에 넣은 모든 Javascript 객체를 읽기 전용으로 취급해야함**

```javascript
const [position, setPosition] = useState({
    x: 0,
    y: 0
  });

onPointerMove={e => {
  position.x = e.clientX;
  position.y = e.clientY;
}}
```

위 코드는 이전 렌더링에서 position에 할당된 객체를 수정하지만 state 설정자 함수를 사용하지 않으면 React에서 객체가 변이되었다는 사실을 알지 못함.

그렇기 떄문에 React에서는 아무 반응도 하지 않음.

state 변이는 경우에 따라 작동할 수 있지만 권장하지 않으며 렌더링에서 접근할 수 있는 state 값은 읽기 전용으로 취급해야 함

이 경우 실제 렌더링을 촉발 하려면 **새 객체를 생성하고 state 설정자 함수에 전달**

```javascript
onPointerMove={e => {
  setPosition({
    x: e.clientX,
    y: e.clientY
  });
}}
```

### 지역 변이는 괜찮음

아래 코드는 **기존 객체의 state**를 수정하기 때문에 문제

```javascript
position.x = e.clientX;
position.y = e.clientY;
```

그러나 이와 같은 코드는 방금 생성한 새로운 객체를 변이하는 것이기 때문에 **완전히 괜찮음**

```javascript
const nextPosition = {};
nextPosition.x = e.clientX;
nextPosition.y = e.clientY;
setPosition(nextPosition);
```

변이는 이미 state가 있는 기존 객체를 변경할 때만 문제.

방금 생성한 객체를 변경해도 다른 코드가 아직 참조하지 않으므로 괜찮으며 객체를 변경해도 해당 객체에 의존하는 다른 객체에 실수로 영향을 미치지 않음.

이러한 것을 **지역 변이**라고 하며, 렌더링하는 동안에도 지역변이를 수행 할 수 있음.

## **전개 구문을 사용하여 객체 복사하기**

기본 데이터를 새로 만드는 객체의 일부로 포함시키고 싶을 때.

하나의 필드만 업데이트 하고 다른 모든 필드는 이전 값을 유지하고 싶을 때.

모든 속성을 개별적으로 복사할 필요 없도록 `...` 객체 전개 구문을 사용.

```javascript
setPerson({
	...person,
	firstName: e.target.value
})
```

`...` 객체 전개 구문 얕은 구문으로 한 단계 깊이만 복사 함. 속도는 빠르지만 중첩된 프로퍼티를 업데이트하려면 두 번 이상 사용해야하 함.

### 여러 필드에 단일 이벤트 핸들러 사용

객체 내에서 `[` 및 `]` 중괄호를 사용하여 동적이름을 가진 프로퍼티를 지정할 수 도 있음.

다음은 세 개의 다른 이벤트 핸들러 대신 단일 이벤트 핸들러를 사용한 예시.

```javascript
import { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com'
  });

  function handleChange(e) {
    setPerson({
      ...person,
      [e.target.name]: e.target.value
    });
  }

  return (
    <>
      <label>
        First name:
        <input
          name="firstName"
          value={person.firstName}
          onChange={handleChange}
        />
      </label>
      <label>
        Last name:
        <input
          name="lastName"
          value={person.lastName}
          onChange={handleChange}
        />
      </label>
      <label>
        Email:
        <input
          name="email"
          value={person.email}
          onChange={handleChange}
        />
      </label>
      <p>
        {person.firstName}{' '}
        {person.lastName}{' '}
        ({person.email})
      </p>
    </>
  );
}
```

## 중첩된 객체 업데이트

```javascript
const [person, setPerson] = useState({
  name: 'Niki de Saint Phalle',
  artwork: {
    title: 'Blue Nana',
    city: 'Hamburg',
    image: 'https://i.imgur.com/Sd1AgUOm.jpg',
  }
});
```

`person.artwork.city` 를 업데이트 할려면 변이를 사용하여 업데이트 하는 방법이 명확합니다. 하지만 React에서는 state를 불면으로 취급하기 때문에 `city` 를 변경하려면 새 `artwork` 객체를 생성한 다음 새 `artwork`을 가리키는 새로운 `person` 객체를 생성해야함

```javascript
const nextArtwork = { ...person.artwork, city: 'New Delhi' };
const nextPerson = { ...person, artwork: nextArtwork };
setPerson(nextPerson);
```

또는 단일 함수 호출로 작성할 수도 있음.

```javascript
setPerson({
  ...person, // Copy other fields
  artwork: { // but replace the artwork
             // 대체할 artwork를 제외한 다른 필드를 복사합니다.
    ...person.artwork, // with the same one
    city: 'New Delhi' // but in New Delhi!
                      // New Delhi'는 덮어씌운 채로 나머지 artwork 필드를 복사합니다!
  }
});
```

### 객체는 실제로 중첩되지 않습니다.

```javascript
let obj = {
  name: 'Niki de Saint Phalle',
  artwork: {
    title: 'Blue Nana',
    city: 'Hamburg',
    image: 'https://i.imgur.com/Sd1AgUOm.jpg',
  }
};
```

중첩은 객체의 동작 방식을 고려해 보자면 정확한 방식은 아님.

코드가 실행될 때 중첩된 객체 같은 것은 존재하지 않고 실제로는 서로 다른 두 객체를 보고 있는 것 입니다.

```javascript
let obj1 = {
  title: 'Blue Nana',
  city: 'Hamburg',
  image: 'https://i.imgur.com/Sd1AgUOm.jpg',
};

let obj2 = {
  name: 'Niki de Saint Phalle',
  artwork: obj1
};

let obj3 = {
  name: 'Copycat',
  artwork: obj1
};
```

`obj3.artwork.city`를 변이하면 `obj2.artwork.city`와 `[obj2.city](http://obj2.city)` 모두에 영향을 미침. 왜냐하면 `obj3.artwork`, `obj2.artwork`, `obj1` 이 동일한 객체이기 떄문.

프로퍼티를 사용하여 서로를 가리키는 별도의 객체임

### Immer로 간결한 업데이트 로직 작성(라이브러리)

state가 깊게 중첩된 경우 그것을 평평하게 만드는 것을 고려할 수 있지만 state 구조를 변경하고 싶지 않다면 중첩된 전개 구문보다 더 간편한 방법을 선호할 수 있음.

Immer는 변이 구문을 사용하여 작성하더라도 자동으로 사본을 생성해주는 편리항 인기 라이브러리 입니다.

Immer를 사용하면 작성한 코드가 규칙을 깨고 객체를 변이 하는 것 처럼 보이나 일반 변이와 달리 이전 state를 덮어쓰지 않음

Immer를 사용해 보려면

1. `npm install use-immer`를 실행하여 Immer를 의존성으로 추가
2. 그런 다음 `import { useState } from 'react'`를 `import { useImmer } from 'use-immer'`로 바꿉니다.

```javascript
import { useImmer } from 'use-immer';

export default function Form() {
  const [person, updatePerson] = useImmer({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
  });

  function handleNameChange(e) {
    updatePerson(draft => {
      draft.name = e.target.value;
    });
  }

  function handleTitleChange(e) {
    updatePerson(draft => {
      draft.artwork.title = e.target.value;
    });
  }

  function handleCityChange(e) {
    updatePerson(draft => {
      draft.artwork.city = e.target.value;
    });
  }

  function handleImageChange(e) {
    updatePerson(draft => {
      draft.artwork.image = e.target.value;
    });
  }
//.... 생략
}
```

⏮️ Previous: [Queueing a Series of State Updates](./005-리액트%20Queueing%20a%20Series%20of%20State%20Updates.md)

⏭️ Next: [Updating Arrays in State](./007-리액트%20Updating%20Arrays%20in%20State.md)