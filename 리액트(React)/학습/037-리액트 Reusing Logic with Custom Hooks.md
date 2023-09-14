# Reusing Logic with Custom Hooks

> 커스텀훅으로 로직 재사용하기

React에는 `useState`, `useContext`, `useEffect`와 같은 몇 가지 빌트인 훅이 있습니다. 때로는 데이터를 페치하거나, 사용자가 온라인 상태인지 추적하거나, 채팅방에 연결하는 등 좀 더 구체적인 목적을 위한 훅이 있었으면 좋겠다는 생각을 할 수 있습니다.

React에서 이러한 훅을 찾지 못할 수도 있지만 애플리케이션의 필요에 따라 자신만의 훅을 만들 수 있습니다.

## 배울 내용들

1. 커스텀 훅이란 무엇이며, 직접 작성하는 방법
2. 컴포넌트 간에 로직을 재사용하는 방법
3. 커스텀 훅의 이름을 만들고 구조화하는 방법
4. 커스텀 훅을 추출해야 하는 시기와 이유

## 커스텀 훅: 컴포넌트간의 로직 공유

대부분의 앱이 그렇듯이 네트워크에 크게 의존하는 앱을 개발한다고 가정해 보겠습니다. 사용자가 앱을 사용하는 동안 실수로 네트워크 연결이 끊어진 경우 사용자에게 주의를 줄 경우 어떻게 하면 좋을까요? 이럴 경우에 컴포넌트에는 두 가지가 필요합니다.

1. 네트워크가 온라인 상태인지 여부를 추적하는 state
2. 전역 `[online](https://developer.mozilla.org/en-US/docs/Web/API/Window/online_event)` 및 `[offline](https://developer.mozilla.org/en-US/docs/Web/API/Window/offline_event)` 이벤트를 구독하고, state를 업데이트하는 Effect

이렇게 하면 컴포넌트가 네트워크 state와 동기화된 상태로 유지됩니다. 다음과 같이 시작할 수 있습니다:

```jsx
import { useState, useEffect } from 'react';

export default function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```

네트워크를 켜고 끄고, 동작에 따라 이 `StatusBar(상태 표시줄)`이 어떻게 업데이트되는지 확인해 보세요

이제  다른 컴포넌트에서도 동일한 로직을 사용하고 싶다고 가정해 봅시다. 네트워크가 꺼져 있을 때 비활성화되고 저장 대신 다시 연결 중이 표시되는 저장 버튼을 구현하고 싶다고 가정해 보겠습니다.

시작하려면 `isOnline` state와 Effect를 복사하여 `SaveButton`에 붙여넣으면 됩니다:

```jsx
import { useState, useEffect } from 'react';

export default function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}
```

네트워크를 끄면 버튼의 모양이 변경되는지 확인합니다.

이 두 컴포넌트는 잘 작동하지만 두 컴포넌트 간의 로직이 중복되는 것은 안타까운 일입니다. 두 컴포넌트의 시각적 모양은 다르지만 당신은 두 컴포넌트 사이의 로직을 재사용하고 싶을 것입니다.

### 컴포넌트에서 커스텀 훅 추출하기

`useState` 와 `useEffect`와 같은, 만들어진 `useOnlineStatus` 훅이 있다고 잠깐만 가정해봅시다. 이 두 컴포넌트들은 단순화 될 수 있고 두 컴포넌트 간의 중복을 제거할 수 있습니다

```jsx
function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}
```

이러한 빌트인 훅은 없지만, 직접 만들 수 있습니다.`useOnlineStatus` 이라는 함수를 선언하고 앞서 작성한 컴포넌트에서 중복된 코드를 모두 이 함수로 옮깁니다.

```jsx
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

이 함수의 마지막에 `isOnline`의 값을 return하고, 컴포넌트들이 이 값을 읽을 수 있게 합니다.

네트워크를 켜고 끄면서 두 컴포넌트가 모두 업데이트되는지 확인합니다.

이제 컴포넌트에는 반복적인 로직이 많지 않습니다. **더 중요한 것은, 컴포넌트 내부의 코드가 (브라우저 이벤트에 가입하여) *어떻게 할 것인가*가 아니라 *무엇을 할 것인가*(온라인 상태 사용!)를 설명한다는 점입니다.**

로직을 커스텀 훅으로 추출하면 외부 시스템이나 브라우저 API를 처리하는 방법에 대한 지저분한 세부 사항을 숨길 수 있습니다. 컴포넌트의 코드는 구현이 아니라 의도를 표현합니다.

### 훅의 이름은 언제나 `use`로 시작됩니다.

React 애플리케이션은 컴포넌트로 빌드됩니다. 컴포넌트는 빌트인이든 커스텀이든 상관없이 훅으로 빌드됩니다. 다른 사람이 만든 커스텀 훅을 사용하는 경우가 많지만, 가끔은 직접 작성할 수도 있습니다!

이때는 다음 명명 규칙을 따라야 합니다.

1. **React 컴포넌트 이름은** `StatusBar`나 `SaveButton`과 같이 **대문자로 시작해야 합니다.** 또한 React 컴포넌트는 JSX와 같이 React가 표시하는 방법을 알고 있는 것을 반환해야 합니다.
2. **훅의 이름은** `useState`(빌트인)이나 `useOnlineStatus`(커스텀)처럼 **`use`로 시작**해야 하고, 그 다음의 첫글자는 대문자여야 합니다. 훅은 임의의 값을 반환할 수 있습니다.

이 규칙은 컴포넌트를 보고 state, Effect 및 기타 React 기능이 어디에 숨어 있는지 항상 알 수 있도록 보장합니다.

예를 들어, 컴포넌트 내부에 `getColor()` 함수 호출이 있다면, 그 이름이 `use`로 시작하지 않기 때문에 내부에 React state를 포함할 수 없다는 것을 확신할 수 있습니다. 하지만 `useOnlineStatus()`와 같은 함수 호출은 내부에 다른 훅에 대한 호출을 포함할 가능성이 높습니다!

### 노트

Linter가 React용으로 구성된 경우, 이 명명 규칙을 적용합니다. 위의 샌드박스로 스크롤하여 `useOnlineStatus` 를 `getOnlineStatus`로 변경합니다. 이제 더는 내부에서 `useState` 나 `useEffect` 를 호출할 수 없다는 것을 알 수 있습니다. 오직 훅과 컴포넌트만이 다른 훅을 호출할 수 있습니다!

### ****렌더링 시에 호출되는 모든 함수에 use 접두사를 써야 하나요?****

아니요. 훅을 *호출*하지 않는 함수는 훅이 될 필요가 없습니다.

함수가 훅을 호출하지 않는다면 `use` 접두사를 사용하지 마세요. 대신 `use` 접두사가 없는 일반 함수로 작성하세요. 예를 들어, 아래의 `useSorted`는 Hook을 호출하지 않으므로 대신 `getSorted`로 호출하세요.

```jsx
// 🔴 이러지 마세요: 훅을 사용하지 않는 훅
function useSorted(items) {
  return items.slice().sort();
}
// ✅ 좋습니다: 훅을 사용하지 않는 일반 함수
function getSorted(items) {
  return items.slice().sort();
}
```

이렇게 하면 코드가 조건을 포함하여 어디서나 이 일반 함수를 호출할 수 있습니다.

```jsx
function List({ items, shouldSort }) {
  let displayedItems = items;
  if (shouldSort) {
    // ✅ getSorted()는 훅이 아니므로 조건부로 호출해도 괜찮음
    displayedItems = getSorted(items);
  }
  // ...
}
```

함수가 내부에 하나 이상의 훅을 사용하는 경우 함수에 `use` 접두사를 지정해야 합니다(따라서 훅으로 만들어야 합니다.

```jsx
// ✅ 좋습니다: 다른 훅을 사용하는 훅
function useAuth() {
  return useContext(Auth);
}
```

엄밀히 말하자면 이것은 React에 의해 강제되지 않습니다. 원칙적으로 다른 훅을 호출하지 않는 훅을 만들 수 있습니다.

이는 종종 혼란스럽고 제한적이므로 이 패턴은 피하는 것이 가장 좋습니다. 하지만 드물게 도움이 되는 경우가 있을 수 있습니다.

예를 들어, 함수에 지금은 훅을 사용하지 않지만 나중에 훅 호출을 추가할 계획이 있을 수 있습니다. 이 경우 `use`접두사를 사용하여 이름을 지정하는 것이 좋습니다:

```jsx
// ✅ 좋습니다: 나중에 다른 훅을 사용할 가능성이 있는 훅
function useAuth() {
  // TODO: 인증 기능이 구현되면 다음 줄로 바꿀 것:
  // return useContext(Auth);
  return TEST_USER;
}
```

그러면 컴포넌트가 조건부로 호출할 수 없게 됩니다.

이것은 실제로 내부에 Hook 호출을 추가할 때 중요해질 것입니다. 내부에서 Hook을 사용할 계획이 없다면 Hook으로 만들지 마세요.

### 커스텀 훅은 state 자체가 아닌 상태적인 로직을 공유합니다.

앞의 예제에서는 네트워크를 켜고 끌 때 두 컴포넌트가 함께 업데이트되었습니다. 그러나 하나의 `isOnline` state 변수가 두 컴포넌트 간에 공유된다고 생각하는 것은 잘못된 생각입니다. 

```jsx
function StatusBar() {
  const isOnline = useOnlineStatus();
}

function SaveButton() {
  const isOnline = useOnlineStatus();
}
```

중복을 제거하기 전과 같은 방식으로 동작하고 있습니다:

```jsx
function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
  }, []);
}

function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
  }, []);
}
```

이들은 두 완전히 독립적인 state 변수 및 Effect입니다! 단지 네트워크가 켜져 있는지 여부에 관계없이 동일한 외부 값과 동기화했기 때문에 동시에 동일한 값을 갖게 된 것입니다.

이를 더 잘 설명하기 위해 다른 예시가 필요합니다. 이 `Form` 컴포넌트를 생각해 봅시다.

```jsx
import { useState } from 'react';

export default function Form() {
  const [firstName, setFirstName] = useState('Mary');
  const [lastName, setLastName] = useState('Poppins');

  function handleFirstNameChange(e) {
    setFirstName(e.target.value);
  }

  function handleLastNameChange(e) {
    setLastName(e.target.value);
  }

  return (
    <>
      <label>
        First name:
        <input value={firstName} onChange={handleFirstNameChange} />
      </label>
      <label>
        Last name:
        <input value={lastName} onChange={handleLastNameChange} />
      </label>
      <p><b>Good morning, {firstName} {lastName}.</b></p>
    </>
  );
}
```

각 양식 필드에는 몇 가지 반복되는 로직이 있습니다:

1. state(`firstName` 및 `lastName`)가 있습니다.
2. 변경 핸들러(`handleFirstNameChange` 및 `handleLastNameChange`)가 있습니다.
3. 해당 input에 대한 `value` 및 `onChange` 속성을 지정하는 JSX 조각이 있습니다.

반복 로직을 이 `useFormInput` 커스텀 훅으로 추출할 수 있습니다.

```jsx
import { useState } from 'react';

export function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  function handleChange(e) {
    setValue(e.target.value);
  }

  const inputProps = {
    value: value,
    onChange: handleChange
  };

  return inputProps;
}
```

`value`라는 state variable(state 변수)를 하나만 선언하는 것을 주목하세요.

하지만 `Form` 컴포넌트는 `useFormInput`을 두 번 호출합니다.

```jsx
function Form() {
  const firstNameProps = useFormInput('Mary');
  const lastNameProps = useFormInput('Poppins');
  // ...
```

이것이 바로 두 개의 state 변수를 선언하는 것처럼 작동하는 이유입니다!

**커스텀 훅을 사용하면 상태 로직(stateful logic)은 공유할 수 있지만 state 자체는 공유할 수 없습니다. 각 훅 호출은 동일한 훅에 대한 다른 모든 호출과 완전히 독립적입니다.** 

이것이 바로 위의 두 코드가 완전히 동일한 이유입니다. 원하신다면 스크롤을 위로 올려서 비교해 보세요. 커스텀 훅을 추출하기 전과 후의 동작은 동일합니다.

여러 컴포넌트 간에 state 자체를 공유해야 하는 경우, 대신 끌어올려 전달하기를 사용하세요.

## 훅 사이에 반응형 값 전달하기

컴포넌트를 다시 렌더링할 때마다 커스텀 훅 내부의 코드가 다시 실행됩니다.

이것이 컴포넌트와 마찬가지로 커스텀 훅도 순수해야 하는 이유입니다. 커스텀 Hook의 코드를 컴포넌트 본문의 일부로 생각하세요!

커스텀 훅은 컴포넌트와 함께 다시 렌더링되기 때문에 항상 최신 props와 state를 받습니다. 

이것이 무엇을 의미하는지 이 채팅방 예시를 통해 알아보세요. 서버 URL 또는 선택한 채팅방을 변경합니다.

`serverUrl` 혹은 `roomId` 를 변경할 때마다 Effect는 변화에 반응하고 재동기화 됩니다. Effect의 의존성을 변경할 때마다 채팅이 다시 연결된다는 것은 콘솔 메시지를 통해 알 수 있습니다.

이제 Effect 코드를 커스텀 훅으로 옮깁니다:

```jsx
export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

이것은 `ChatRoom` 컴포넌트가 내부에서 어떻게 작동하는지 걱정할 필요 없이 사용자 지정 훅을 호출할 수 있습니다.

```jsx
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

이렇게 하면 더 간단해 보입니다! (하지만 기능상 동일합니다.)

이 로직이 여전히 prop과 state 변화에 반응한다는 것을 주목하세요. 서버 URL과 선택한 room을 편집해보세요.

하나의 훅의 값을 어떻게 리턴했는지 주목하세요.

```jsx
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });
```

그리고 다른 훅에 인풋으로 전달합니다.

```jsx
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });
```

`ChatRoom` 컴포넌트가 다시 렌더링할 때마다 최신 `roomId`와`serverUrl`을 Hook에 전달합니다. 이것이 바로 리렌더링 후 값이 달라질 때마다 Effect가 채팅에 다시 연결되는 이유입니다. 

(음악 처리 소프트웨어로 작업해 본 적이 있다면 이런 식으로 Hook을 연결하면 리버브나 코러스 추가와 같이 여러 오디오 효과를 연결하는 것을 떠올릴 수 있습니다. 마치 `useState`의 출력이 `useChatRoom`의 input에 피드되는 것과 같습니다.)

### 커스텀 훅에게 이벤트 핸들러 전달하기

### 주의사항

**이 섹션에서는 아직 안정된 버전의 React로 출시되지 않은 실험적인 API에 대해 설명합니다.**

더 많은 컴포넌트에서 `useChatRoom`을 사용하기 시작하면 다른 컴포넌트에서 그 동작을 사용자 정의할 수 있을 것입니다. 

예를 들어, 현재 메시지가 도착했을 때 무엇을 해야 하는지에 대한 로직은 Hook 내부에 하드코딩되어 있습니다.

```jsx
export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

만약에 이 로직을 다시 컴포넌트 안으로 이동하고 싶다고 가정해 봅시다.

```jsx
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl,
    onReceiveMessage(msg) {
      showNotification('New message: ' + msg);
    }
  });
```

이 기능을 사용하려면 커스텀 훅을 변경하여 `onReceiveMessage` 를 이름 옵션 중 하나로 사용하세요.

```jsx
export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onReceiveMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl, onReceiveMessage]);// ✅ 모든 의존성이 선언됨
}
```

이 방법은 작동하지만 커스텀 Hook이 이벤트 핸들러를 수락할 때 한 가지 더 개선할 수 있습니다.

`onReceiveMessage`에 의존성을 추가하면 컴포넌트가 다시 렌더링될 때마다 채팅이 다시 연결되므로 이상적이지 않습니다. 이 이벤트 핸들러를 Effect Event로 감싸 의존성에서 제거하세요:

```jsx
import { useEffect, useEffectEvent } from 'react';
// ...

export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  const onMessage = useEffectEvent(onReceiveMessage);

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]); // ✅ 모든 의존성이 선언됨
}
```

이제 `ChatRoom` 컴포넌트가 다시 렌더링할 때마다 채팅이 다시 연결되지 않습니다. 다음은 이벤트 핸들러를 커스텀 Hook에 전달하는 데모입니다:

이제 더 이상 `useChatRoom`이 *어떻게* 작동하는지 알 필요 없이 사용할 수 있습니다. 다른 컴포넌트에 추가하고 다른 옵션을 전달해도 동일한 방식으로 작동합니다. 이것이 바로 커스텀 Hook의 힘입니다.

## 언제 커스텀 훅을 사용할 것인가

중복되는 모든 코드에 대해 커스텀 훅을 추출할 필요는 없습니다. 약간의 중복은 괜찮습니다. 예를 들어, 앞서처럼 단일 `useState` 호출을 감싸기 위해 `useFormInput` 훅을 추출하는 것은 불필요할 수 있습니다.

하지만 Effect를 작성할 때마다 커스텀 훅으로 감싸는 것이 더 명확할지 고려하세요. Effect는 자주 필요하지 않으므로, 만약 Effect를 작성한다면 외부 시스템과 동기화하거나 React에 빌트인 API가 없는 작업을 수행하기 위해 React 외부로 나가야 한다는 뜻입니다. Effect를 커스텀 훅으로 감싸면 의도와 데이터 흐름 방식을 정확하게 전달할 수 있습니다.

예를 들어, 도시 목록을 표시하는 드롭다운과 선택한 도시의 지역 목록을 표시하는 드롭다운 두 개를 표시하는 `ShippingForm` 컴포넌트를 생각해 봅시다. 다음과 같은 코드로 시작할 수 있습니다:

```jsx
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  // 이 Effect는 국가의 도시들을 페치합니다
  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]);

  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);
  // 이 Effect는 선택된 도시의 장소들을 페치합니다
  useEffect(() => {
    if (city) {
      let ignore = false;
      fetch(`/api/areas?city=${city}`)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [city]);
```

이 코드는 상당히 반복적이지만 이러한 Effect는 서로 분리하여 유지하는 것이 맞습니다. 서로 다른 두 가지를 동기화하므로 하나의 Effect로 병합해서는 안 됩니다. 

대신, 위의 `ShippingForm` 컴포넌트 사이의 공통 로직을 자체 `useData` 훅으로 추출하여 단순화할 수 있습니다:

```jsx
function useData(url) {
  const [data, setData] = useState(null);
  useEffect(() => {
    if (url) {
      let ignore = false;
      fetch(url)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setData(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [url]);
  return data;
}
```

이제 `ShippingForm` 컴포넌트의 두 Effect를 모두 `useData` 호출로 바꿀 수 있습니다:

```jsx
function ShippingForm({ country }) {
  const cities = useData(`/api/cities?country=${country}`);
  const [city, setCity] = useState(null);
  const areas = useData(city ? `/api/areas?city=${city}` : null);
```

커스텀 훅을 추출하면 데이터 흐름을 명시적으로 만들 수 있습니다. `url`을 입력하면 `data`를 가져올 수 있습니다.

`useData` 안에 Effect를 숨기면 `ShippingForm` 컴포넌트에서 작업하는 사람이 불필요한 의존성을 추가하는 것을 방지할 수 있습니다.

이상적으로는 시간이 지나면 앱의 Effect 대부분이 커스텀 훅에 포함될 것입니다.

### ****커스텀 훅은 구체적인 고수준 사용 사례에 집중하세요****

먼저 커스텀 훅의 이름을 선택하세요. 명확한 이름을 고르는 데 어려움을 겪는다면 Effect가 컴포넌트의 나머지 로직과 너무 결합되어 있어 아직 추출할 준비가 되지 않았다는 의미일 수 있습니다.

커스텀 훅의 이름은 코드를 자주 작성하지 않는 사람이라도 커스텀 훅이 무엇을 하고, 무엇을 취하고, 무엇을 반환하는지 짐작할 수 있을 정도로 명확해야 합니다:

- ✅ `useData(url)`
- ✅ `useImpressionLog(eventName, extraData)`
- ✅ `useChatRoom(options)`

외부 시스템과 동기화할 때 커스텀 훅의 이름은 좀 더 기술적이고 해당 시스템과 관련된 전문 용어를 사용할 수 있습니다. 해당 시스템에 익숙한 사람이 이해할 수 있는 이름이라면 괜찮습니다:

- ✅ `useMediaQuery(query)`
- ✅ `useSocket(url)`
- ✅ `useIntersectionObserver(ref, options)`

**커스텀 훅은 구체적인 고수준 사용 사례에 집중하세요.** `useEffect` API 자체에 대한 대안 및 편의 래퍼 역할을 하는 커스텀 “생명주기” 훅을 생성하거나 사용하지 마세요:

- 🔴 `useMount(fn)`
- 🔴 `useEffectOnce(fn)`
- 🔴 `useUpdateEffect(fn)`

예를 들어`useMount` 훅은 일부코드가 **마운트 할 때**에만 실행됩니다.

```jsx
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');
  // 🔴 이러지 마세요: 커스텀 "생명주기" 훅 사용
  useMount(() => {
    const connection = createConnection({ roomId, serverUrl });
    connection.connect();

    post('/analytics/event', { eventName: 'visit_chat' });
  });
}
// 🔴 이러지 마세요: 커스텀 "라이브사이클" 훅 생성
function useMount(fn) {
  useEffect(() => {
    fn();
  }, []); // 🔴 React 훅 useEffect에 의존성 누락: 'fn'
}
```

**`useMount`와 같은 커스텀 생명주기 훅은 React 패러다임에 잘 맞지 않습니다.** 

예를 들어, 이 코드 예시에는 실수가 있지만(`roomId` 및 `serverUrl`변경에 반응하지 않음), linter는 직접적인 `useEffect` 호출만 확인하기 때문에 경고하지 않습니다. 당신의 훅에 대해서 알지 못합니다.

Effect를 사용할 것이라면 React API를 직접 사용하세요:

```jsx
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');
	// ✅ 좋습니다: 목적별로 분리된 두 원시 Effect

  useEffect(() => {
    const connection = createConnection({ serverUrl, roomId });
    connection.connect();
    return () => connection.disconnect();
  }, [serverUrl, roomId]);

  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_chat', roomId });
  }, [roomId]);
}
```

그러면 다른 고수준 사용 사례에 대한 커스텀 훅을 추출할 수 있습니다(반드시 그럴 필요는 없습니다):

```jsx
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');
	// ✅ 매우 좋습니다: 용도에 따라 이름을 지정한 커스텀 훅
  useChatRoom({ serverUrl, roomId });
  useImpressionLog('visit_chat', { roomId });
}
```

**좋은 커스텀 훅은 호출 코드가 수행하는 작업을 제한하여 보다 선언적으로 만듭니다**. 

예를 들어, `useChatRoom(options)`은 채팅방에만 연결할 수 있고, `useImpressionLog(eventName, extraData)`는 애널리틱스에 노출 로그만 전송할 수 있습니다.

커스텀 훅 API가 사용 사례를 제한하지 않고 매우 추상적일 경우, 장기적으로는 해결하는 것보다 더 많은 문제를 야기할 가능성이 높습니다.

### 커스텀 훅은 더 나은 패턴으로 마이그레이션하는데 도움을 줍니다.

Effect는 탈출구입니다. React를 벗어나야 할 때, 그리고 사용 사례에 더 나은 빌트인 솔루션이 없을 때 사용합니다.

시간이 지남에 따라 React 팀의 목표는 더 구체적인 문제에 대한 더 구체적인 솔루션을 제공함으로써 앱에서 Effect의 수를 최소한으로 줄이는 것입니다.

Effect를 커스텀 훅으로 감싸면 이러한 솔루션이 제공될 때 코드를 더 쉽게 업그레이드할 수 있습니다.

이 예제로 돌아가 보겠습니다:

위의 예제에서는,  `useState`와 `useEffect`의 페어로 `useOnlineStatus`를 구성했습니다. 하지만 이것은 최적의 방법은 아닙니다. 고려하지 않은 여러 케이스들이 있습니다.

예를 들어, 컴포넌트가 마운트될 때 `isOnline`이 이미 `true`라고 가정하지만, 네트워크가 이미 오프라인 상태였다면 이는 틀릴 수 있습니다.

브라우저 `[navigator.onLine](https://developer.mozilla.org/ko/docs/Web/API/Navigator/onLine)` API를 사용하여 이를 확인할 수 있지만, 서버에서 React 앱을 실행하여 초기 HTML을 생성하는 경우 이를 직접 사용하면 코드가 깨질 수 있습니다. 요컨대, 이 코드는 개선될 수 있습니다.

다행히 React 18에는 이 모든 문제를 해결해 주는 `useSyncExternalStore`라는 전용 API가 포함되어 있습니다. 이 새로운 API를 활용하기 위해 재작성된 `useOnlineStatus` 훅은 다음과 같습니다.

```jsx
import { useSyncExternalStore } from 'react';

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

export function useOnlineStatus() {
  return useSyncExternalStore(
    subscribe,
    () => navigator.onLine, // 클라이언트에서 값을 가져오는 방법
    () => true // 서버에서 값을 가져오는 방법
  );
}
```

이 마이그레이션을 위해 **컴포넌트를 변경할 필요가 없다는 점**을 주목하세요.

```jsx
function StatusBar() {
  const isOnline = useOnlineStatus();
}

function SaveButton() {
  const isOnline = useOnlineStatus();
}
```

이것이 종종 커스텀 훅으로 Effect를 감싸는 것이 좋은 또 다른 이유입니다:

1. Effect와의 데이터 흐름을 매우 명확하게 만들 수 있습니다.
2. 컴포넌트가 Effect의 정확한 구현보다는 의도에 집중할 수 있습니다.
3. React가 새로운 기능을 추가할 때 컴포넌트를 변경하지 않고도 해당 Effect를 제거할 수 있습니다.

디자인 시스템과 유사하게 앱의 컴포넌트에서 공통된 관용구를 추출하여 커스텀 훅으로 만드는 것이 도움이 될 수 있습니다. 이렇게 하면 컴포넌트의 코드가 의도에 집중할 수 있고, 원시 Effect를 자주 작성하는 것을 피할 수 있습니다. 

React 커뮤니티에서 관리하고 있는 훌륭한 커스텀 훅도 많이 있습니다.

### ****React는 데이터 페칭을 위해 빌트인 솔루션을 제공할건가요?****

아직 세부 사항을 작업 중이지만, 앞으로는 다음과 같이 데이터 페칭을 할 수 있을 것으로 예상합니다:

```jsx
import { use } from 'react'; // 아직 동작하지 않습니다!
function ShippingForm({ country }) {
  const cities = use(fetch(`/api/cities?country=${country}`));
  const [city, setCity] = useState(null);
  const areas = city ? use(fetch(`/api/areas?city=${city}`)) : null;
```

앱에서 위의 `useData`와 같은 커스텀 훅을 사용하면 모든 컴포넌트에 원시 Effect를 수동으로 작성하는 것보다 최종적으로 권장되는 접근 방식으로 마이그레이션하는 데 더 적은 변경이 필요할 것입니다. 다만 이전 접근 방식도 여전히 잘 작동하므로 원시 Effect를 작성하는 것이 만족스럽다면 계속 사용할 수 있습니다.

### 여러가지 방법이 있습니다.

브라우저 요청 `[requestAnimationFrame](https://developer.mozilla.org/ko/docs/Web/API/window/requestAnimationFrame)` API를 사용하여 페이드인 애니메이션을 처음부터 구현한다고 가정해 보겠습니다.

애니메이션 루프를 설정하는 Effect로 시작할 수 있습니다. 애니메이션의 각 프레임 동안 ref로 유지하는 DOM 노드의 불투명도를 `1`에 도달할 때까지 변경할 수 있습니다. 코드는 다음과 같이 시작할 수 있습니다.

```jsx
import { useState, useEffect, useRef } from 'react';

function Welcome() {
  const ref = useRef(null);

  useEffect(() => {
    const duration = 1000;
    const node = ref.current;

    let startTime = performance.now();
    let frameId = null;

    function onFrame(now) {
      const timePassed = now - startTime;
      const progress = Math.min(timePassed / duration, 1);
      onProgress(progress);
      if (progress < 1) {
        // 아직 칠해야 할 프레임이 남아있습니다
        frameId = requestAnimationFrame(onFrame);
      }
    }
    function onProgress(progress) {
      node.style.opacity = progress;
    }

    function start() {
      onProgress(0);
      startTime = performance.now();
      frameId = requestAnimationFrame(onFrame);
    }

    function stop() {
      cancelAnimationFrame(frameId);
      startTime = null;
      frameId = null;
    }
    start();
    return () => stop();
  }, []);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

컴포넌트의 가독성을 높이기 위해 로직을 `useFadeIn` 커스텀 훅으로 추출할 수 있습니다.

```jsx
import { useEffect } from 'react';

export function useFadeIn(ref, duration) {
  useEffect(() => {
    const node = ref.current;

    let startTime = performance.now();
    let frameId = null;

    function onFrame(now) {
      const timePassed = now - startTime;
      const progress = Math.min(timePassed / duration, 1);
      onProgress(progress);
      if (progress < 1) {
        // 아직 칠해야 할 프레임이 남아있습니다
        frameId = requestAnimationFrame(onFrame);
      }
    }

    function onProgress(progress) {
      node.style.opacity = progress;
    }

    function start() {
      onProgress(0);
      startTime = performance.now();
      frameId = requestAnimationFrame(onFrame);
    }

    function stop() {
      cancelAnimationFrame(frameId);
      startTime = null;
      frameId = null;
    }

    start();
    return () => stop();
  }, [ref, duration]);
}
```

`useFadeIn` 코드를 그대로 유지할 수도 있지만 더 리팩토링할 수도 있습니다.

예를 들어, 애니메이션 루프를 설정하는 로직을 `useFadeIn`에서 추출하여 `useAnimationLoop`라는 새로운 커스텀 훅으로 만들 수 있습니다:

하지만 꼭 그렇게 할 필요는 없습니다. 일반 함수와 마찬가지로 궁극적으로 코드의 여러 부분 사이의 경계를 어디에 그릴지는 사용자가 결정합니다.

예를 들어, 매우 다른 접근 방식을 취할 수도 있습니다. Effect에 로직을 유지하는 대신 대부분의 명령형 로직을 JavaScript [클래스](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes) 내부로 옮길 수 있습니다:

```jsx
import { useState, useEffect } from 'react';
import { FadeInAnimation } from './animation.js';

export function useFadeIn(ref, duration) {
  useEffect(() => {
    const animation = new FadeInAnimation(ref.current);
    animation.start(duration);
    return () => {
      animation.stop();
    };
  }, [ref, duration]);
}
```

```jsx
export class FadeInAnimation {
  constructor(node) {
    this.node = node;
  }
  start(duration) {
    this.duration = duration;
    this.onProgress(0);
    this.startTime = performance.now();
    this.frameId = requestAnimationFrame(() => this.onFrame());
  }
  onFrame() {
    const timePassed = performance.now() - this.startTime;
    const progress = Math.min(timePassed / this.duration, 1);
    this.onProgress(progress);
    if (progress === 1) {
      this.stop();
    } else {
      // 아직 칠해야 할 프레임이 남아있습니다
      this.frameId = requestAnimationFrame(() => this.onFrame());
    }
  }
  onProgress(progress) {
    this.node.style.opacity = progress;
  }
  stop() {
    cancelAnimationFrame(this.frameId);
    this.startTime = null;
    this.frameId = null;
    this.duration = 0;
  }
}
```

Effects를 사용하면 React를 외부 시스템에 연결할 수 있습니다. 

예를 들어, 여러 애니메이션을 체인으로 연결하기 위해 Effect 간의 조정이 더 많이 필요할수록 위의 샌드박스에서처럼 Effect와 훅에서 해당 로직을 완전히 추출하는 것이 더 합리적입니다. 그러면 추출한 코드가 외부 시스템이 됩니다. 이렇게 하면 React 외부로 이동한 시스템으로 메시지를 보내기만 하면 되기 때문에 Effects를 단순하게 유지할 수 있습니다.

위의 예시에서는 페이드인 로직이 JavaScript로 작성되어야 한다고 가정했습니다. 하지만 이 특정 페이드인 애니메이션은 일반 [CSS 애니메이션](https://developer.mozilla.org/ko/docs/Web/CSS/CSS_Animations/Using_CSS_animations)으로 구현하는 것이 더 간단하고 훨씬 더 효율적입니다.

때로는 훅이 전혀 필요 없을 때도 있습니다!

⏮️ Previous : [Removing Effect Dependencies](./036-리액트%20Removing%20Effect%20Dependencies.md)