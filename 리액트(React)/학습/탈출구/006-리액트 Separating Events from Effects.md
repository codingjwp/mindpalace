# Separating Events from Effects

> 이벤트와 Effect 분리

이벤트 핸들러는 같은 상호 작용을 다시 수행할 때만 다시 실행됩니다.

이벤트 핸들러와 달리, Effect는 prop 또는 state 변수와 같은 일부 값을 마지막 렌더링 때와 다른 값으로 읽게 되면 다시 동기화됩니다.

때로는 일부 값에 대한 응답으로 다시 실행되는 Effect와 그렇지 않은 Effect의 혼합이 필요할 때도 있습니다.

## 배울 내용들

1. 이벤트 핸들러와 Effect 중에서 선택하는 방법
2. Effect는 반응형이고 이벤트 핸들러는 반응형이 아닌 이유
3. Effect 코드의 일부가 반응하지 않기를 원할 때 해야 할 일
4. Effect Event란 무엇이며 Effect에서 추출하는 방법
5. Effect Event를 사용하여 Effect에서 최신 props 및 state를 읽는 방법

## 이벤트 핸들러와 Effect중 선택하기

먼저, 이벤트 핸들러와 Effect의 차이점에 대해 다시 한번 살펴보겠습니다.

채팅방 컴포넌트를 구현한다고 상상해보세요. 요구 사항을 다음과 같습니다:

1. 컴포넌트는 선택한 채팅방에 자동으로 연결되어야 합니다.

2. 전송 버튼을 클릭하면, 채팅에 메시지를 전송해야 합니다.

이미 코드를 구현했지만, 이 코드를 어디에 놓아야 할지 확실하지 않습니다.

이벤트 핸들러와 Effects 중 어떤 것을 사용해야 할까요? 이 질문에 대답할 때마다, 코드가 실행되어야하는 이유를 생각해야합니다.

### 이벤트 핸들러는 특정 상호 작용에 대한 응답으로 실행됩니다.

사용자 관점에서 메시지를 보내는 것은 특정 전송 버튼을 클릭했기 때문에 발생되어야 합니다.

메시지가 다른 시간이나 다른 이유로 보내지면 사용자들은 화를 낼 것입니다.

이것이 메시지를 보내는 것이 이벤트 핸들러여야하는 이유입니다.

이벤트 핸들러를 사용하면 특정 상호 작용에 대한 처리를 할 수 있습니다:

```typescript
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');
  function handleSendClick() {
    sendMessage(message);
  }
  return (
    <>
      <input value={message} onChange={e => setMessage(e.target.value)} />
      <button onClick={handleSendClick}>Send</button>;
    </>
  );
}
```

이벤트 핸들러를 사용하면 사용자가 버튼을 누를 때만 `sendMessage(message)` 가 실행된다는 것을 확신할 수 있습니다.

### Effect는 동기화 필요할 때마다 실행됩니다.

컴포넌트는 채팅방에 연결한 상태로 유지해야 합니다. 그러기 위한 코드는 어디에 있어야 할까요?

코드를 실행해야 하는 이유는 특정 상호 작용이 아닙니다.

사용자가 채팅방 화면으로 이동한 이유나 방법은 중요하지 않습니다. 사용자들이 채팅방 화면을 보고 상호작용 할 수 있어야하기 때문에, 컴포넌트는 선택한 채팅 서버에 계속 연결되어 있어야 합니다.

앱의 초기 화면이 채팅방 컴포넌트이고, 사용자가 어떤 상호작용도 수행하지 않았더라도 여전히 채팅 서버에 연결되어야 합니다. 이것이 바로 Effect인 이유입니다:

```typescript
function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId]);
}
```

이 코드를 사용하면, 사용자가 수행한 특정 상호작용과는 무관하게, 항상 현재 선택된 채팅 서버에 대한 활성화된 연결이 있음을 확신할 수 있습니다.

사용자가 앱을 열었을 때 뿐만 아니라, 다른 채팅방을 선택하거나 다른 화면으로 이동했다가 다시 돌아와도 Effect가 현재 선택된 방과 동기화되어 컴포넌트에 항상 현재 선택된 채팅서버가 연결된 상태가 유지되며, 필요할 때마다 다시 연결됩니다.

## 반응형 값 및 반응형 로직

직관적으로, 이벤트 핸들러는 버튼을 클릭하는 등 항상 “수동”으로 촉발시킨다고 말할 수 있습니다. 반면에 Effect는 으로 동기화 상태를 유지하는 데 필요한 만큼 자주 다시 실행됩니다.

이에 대해 더 정확하게 생각할 수 있는 방법이 있습니다.

컴포넌트 본문 내부에 선언된 props, state, 변수를 반응형 값이라고 합니다.

이 예제에서 `serverUrl`은 반응형 값이 아니지만 `roomId`와 `message`는 반응형 값입니다.

이들은 렌더링 데이터 흐름에 참여합니다:

```typescript
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');
}
```

이와 같은 반응형 값은 리렌더링으로 인해 변경될 수 있습니다. 예를 들어, 사용자가 `message`를 수정하거나 드롭다운에서 다른 `roomId`를 선택할 수 있습니다. 이벤트 핸들러와 Effect는 변경 사항에 다르게 반응합니다:

- **이벤트 핸들러 내부의 로직은 반응형이 아닙니다.** 사용자가 동일한 상호작용을 다시 수행하지 않는 한 다시 실행되지 않습니다. 이벤트 핸들러는 변경에 반응하지 않고 반응형 값을 읽을 수 있습니다.
- **Effects 내부의 로직은 반응형입니다.** Effect에서 반응형 값을 읽는 경우 의존성으로 지정해야 합니다. 그런 다음 리렌더링으로 인해 해당 값이 변경되면 React는 새 값으로 Effect의 로직을 다시 실행합니다.

이 차이점을 설명하기 위해 이전 예제를 다시 살펴보겠습니다.

### 이벤트 핸들러 내부의 로직은 반응형이 아닙니다.

이 코드 라인를 살펴보세요. 이 로직이 반응형이어야 할까요, 아닐까요?

```typescript
sendMessage(message);
```

사용자의 관점에서 볼 때 **`message`가 변경되었다고 해서 메시지를 보내겠다는 뜻은 아닙니다.**

사용자가 입력 중이라는 의미일 뿐입니다. 즉, 메시지를 전송하는 로직은 반응적이어서는 안 됩니다. 반응형 값이 변경되었다는 이유만으로 다시 실행되어서는 안 됩니다.

이것이 바로 이벤트 핸들러에 속하는 이유입니다:

이벤트 핸들러는 반응형이 아니므로 사용자가 보내기 버튼을 클릭할 때만 `sendMessage(message)`가 실행됩니다.

### Effect 내부의 로직은 반응형 입니다.

이제 이 라인으로 돌아가 보겠습니다

```typescript
const connection = createConnection(serverUrl, roomId);
connection.connect();
```

사용자 입장에서 보면, **`roomId`가 변경되었다는 것은 다른 룸에 연결하고 싶다는 의미입니다.**

즉, 방에 연결하기 위한 로직은 반응형이어야 합니다.

이러한 코드 라인은 반응형 값을 따라잡고 값이 달라지면 다시 실행되기를 원합니다. 이것이 바로 Effect에 속하는 이유입니다:

```typescript
useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect()
    };
  }, [roomId]);
```

Effect는 반응형이므로 `createConnection(serverUrl, roomId)` 및 `connection.connect()` 코드는 `roomId`의 모든 고유값에 대해 실행됩니다.

Effect는 현재 선택된 방에 따라 채팅 연결을 동기화합니다.

## Effect에서 비반응형 로직 추출하기

반응형 로직과 비반응형 로직을 함께 사용하려는 경우 상황이 더 까다로워집니다.

예를 들어, 사용자가 채팅에 연결할 때 알림을 표시하고 싶다고 가정해 봅시다.

props에서 현재 테마(dark or light)를 읽어 올바른 색상으로 알림을 표시합니다:

```typescript
function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    // ...
```

그러나, `theme`는 반응형 값이며(리렌더링의 결과로 변경될 수 있음), Effect에서 읽는 모든 반응형 값은 의존성으로 선언해야 합니다.

이제 `theme`를 Effect의 의존성으로 지정해야 합니다:

```typescript
function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    return () => {
      connection.disconnect()
    };
  }, [roomId, theme]);
```

`roomId` 가 변경되면, 예상한대로 채팅이 다시 연결됩니다.

하지만 `theme`도 의존성이기 때문에 dark 테마와 light 테마 사이를 전환할 때마다 채팅이 또 다시 연결됩니다. 이러한 경우는 좋지 못합니다.

다시 말해, 이 라인이 반응형 Effect안에 있더라도 이 라인이 반응형 Effect가 되는 것을 원하지 않는다는 뜻입니다.

```typescript
showNotification('Connected!', theme);
```

이 비반응형 로직을 주변의 반응형 Effect로부터 분리할 수 있는 방법이 필요합니다.

### Effect Event 선언하기

### 주의사항

**이 섹션에서는 아직 안정된 버전의 React로 출시되지 않은 실험적인 API에 대해 설명합니다.**

이 비반응형 로직을 Effect에서 추출하려면 `useEffectEvent`라는 특수 Hook을 사용합니다

```typescript
import { useEffect, useEffectEvent } from 'react';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });
```

여기서 `onConnected`는 **Effect Evnet**라고 불리며, Effect 로직의 일부이지만 이벤트 핸들러처럼 동작합니다.

그 내부의 로직은 반응형으로 동작하지 않으며, 항상 props와 state의 최신 값을 “확인”합니다.

이제 Effect 내부에서 `onConnected` **Effect Event**를 호출할 수 있습니다:

```typescript
function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      onConnected();
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);
```

이렇게 하면 문제가 해결됩니다.

Effect의 의존성 목록에서 `onConnected` 를 제거해야 한다는 점에 유의하세요. **Effect Event는 반응형 이벤트가 아니므로 의존성에서 생략해야 합니다.**

새 동작이 예상대로 작동하는지 확인합니다

Effect Event는 이벤트 핸들러와 매우 유사하다고 생각할 수 있습니다.

가장 큰 차이점은 이벤트 핸들러는 사용자 상호작용에 대한 응답으로 실행되는 반면, Effect Event는 Effect에서 사용자가 촉발한다는 점입니다. 

Effect Event를 사용하면 Effect의 반응성과 반응형으로 동작해서는 안 되는 코드 사이의 사슬을 끊을 수 있습니다.

### Effect Event로 최신 prop 및 state 읽기

### 주의사항

**이 섹션에서는 아직 안정된 버전의 React로 출시되지 않은 실험적인 API에 대해 설명합니다.**

Effect Event를 사용하면 억제하고 싶을 수 있는 많은 의존성 linter 패턴을 수정할 수 있습니다.

예를 들어, 페이지 방문을 기록하는 Effect가 있다고 가정해 보겠습니다:

```typescript
function Page() {
  useEffect(() => {
    logVisit();
  }, []);
}
```

나중에 사이트에 여러 경로를 추가합니다. 이제 `Page`컴포넌트는 현재 경로가 포함된 `url` prop을 받습니다. `logVisit` 호출 시 `url`을 전달하고 싶지만 의존성 linter가 불평합니다:

코드로 무엇을 하고 싶은지 생각해 보세요. 

각 URL이 서로 다른 페이지를 나타내므로 서로 다른 URL에 대해 별도로 방문을 기록하려고 합니다. 

다시 말해, 이 `logVisit` 호출은 `url`에 반드시 반응해야 합니다. 그렇기 때문에 이 경우 의존성 linter를 따르고 `url`을 의존성으로 추가하는 것이 합리적입니다:

```typescript
function Page({ url }) {
  useEffect(() => {
    logVisit(url);
  }, [url]);
}
```

이제 모든 페이지 방문 기록 시 장바구니에 있는 품목의 수를 포함하려고 한다고 가정해 보겠습니다.

```typescript
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  useEffect(() => {
    logVisit(url, numberOfItems);
  }, [url]); // 🔴 React Hook 사용 효과에 누락된 종속성이 있습니다: numberOfItems
}
```

Effect 내부에서 `numberOfItems`를 사용했기 때문에, Linter는 이를 의존성으로 추가하라고 요청합니다. 

그러나 `logVisit` 호출이 `numberOfItems`에 대해 반응하는 것을 원하지 않습니다. 사용자가 장바구니에 무언가를 넣고 `numberOfItems`가 변경되는 것이 사용자가 페이지를 다시 방문했다는 것을 의미하지는 않습니다.

즉, 페이지 방문 어떤 의미에서 이벤트입니다. 이는 정확한 순간에 발생합니다.

코드를 두 부분으로 나눠봅시다:

```typescript
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    onVisit(url);
  }, [url]);
}
```

여기서 `onVisit`는 Effect Event이며 그 안의 코드는 반응형이 아닙니다.

그렇기 때문에 변경 시 주변 코드가 다시 실행될 것을 걱정할 필요 없이 `numberOfItems`(또는 다른 반응형 값!)를 사용할 수 있습니다.

반면에 Effect 자체는 반응형으로 유지됩니다. Effect 내부의 코드는 `url` prop을 사용하므로, Effect는 다른 `url`로 다시 렌더링할 때마다 다시 실행됩니다. 그러면 `onVisit` Effect Event가 호출됩니다.

결과적으로 `url`이 변경될 때마다 `logVisit`을 호출하고 항상 최신`numberOfItems`를 읽게 됩니다. 그러나 `numberOfItems`가 자체적으로 변경되면 코드가 다시 실행되지 않습니다

### 노트

매개변수 없이 `onVisit()`을 호출하고 그 안에 있는 `url`을 읽을 수 있는지 궁금할 수 있습니다:

```typescript
const onVisit = useEffectEvent(() => {
    logVisit(url, numberOfItems);
  });

  useEffect(() => {
    onVisit();
  }, [url]);
```

이 방법도 작동하지만 이 `url`을 Effect Event에 명시적으로 전달하는 것이 좋습니다. 

Effect Event에 인자로 `url`을 전달하면 다른 `url`을 가진 페이지를 방문하는 것이 사용자 관점에서 별도의 이벤트를 구성한다는 의미입니다. `visitedUrl` 은 발생한 이벤트의 일부입니다:

```typescript
const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    onVisit(url);
  }, [url]);
```

Effect Event가 명시적으로 `visitedUrl`을 요청하기 때문에 이제 Effect의 의존성에서 실수로 `url`을 제거할 수 없습니다. 

`url` 의존성을 제거하면(별개의 페이지 방문이 하나로 계산되게 함) linter에서 이에 대해 경고합니다. 

`onVisit`이 `url`에 대해 반응하기를 원한다면, (반응하지 않는) Effect Event 내부에서 `url`을 읽는 대신 Effect에서 `url`을 전달합니다. 이는 Effect 내부에 비동기 로직이 있는 경우 특히 중요합니다:

```typescript
const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    setTimeout(() => {
      onVisit(url);
    }, 5000);
  }, [url]);
```

여기서 `onVisit` 내부의 `url`은 (이미 변경되었을 수 있는) 최신 `url`에 해당하지만 `visitedUrl`은 원래 이 Effect(및 `onVisit` 호출)를 실행하게 만든 `url`에 해당합니다.

### 대신 의존성 린터를 억제해도 괜찮나요.

기존 코드베이스에서는 때때로 다음과 같이 Lint 규칙이 억제된 것을 볼 수 있습니다:

```typescript
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  useEffect(() => {
    logVisit(url, numberOfItems);
    // 🔴 Avoid suppressing the linter like this:
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [url]);
  // ...
}
```

`useEffectEvent`가 React의 안정적인 API가 된 후에는 **Linter를 억제하지 않는 것이 좋습니다**.
이 규칙을 억제하는 첫 번째 단점은 코드에 도입한 새로운 반응형 의존성에 Efect가 반응해야 할 때 React가 더 이상 경고하지 않는다는 것입니다.

이 예제에서 `url`을 의존성에 추가한 이유는 React가 이를 상기시켜줬기 문입니다.

Linter를 비활성화하면 해당 Effect를 이후에 수정할 때, 더 이상 이러한 경고를 받지 않게 됩니다. 이것은 버그로 이어집니다.

**Linter를 억제하지 않으면 오래된 값으로 인한 문제가 발생하지 않습니다.**
`useEffectEvent`를 사용하면 Linter에 거짓말을 할 필요가 없으며 코드가 예상대로 작동합니다.
그렇다고 해서 `useEffectEvent`가 항상 올바른 해결책이라는 의미는 아닙니다. 반응하지 않으려는 코드 줄에만 적용해야 합니다. 
Linter를 억제하는 다른 올바른 대안에 대해서는 Effect 의존성 제거하기를 읽어보세요.

### Effect Event의 제한사항

### 주의사항

**이 섹션에서는 아직 안정된 버전의 React로 출시되지 않은 실험적인 API에 대해 설명합니다.**

Effect Event는 사용할 수 있는 방법이 매우 제한적입니다:

- **Effect 내부에서만 호출할 수 있습니다.**
- **다른 컴포넌트나 Hook에 전달하지 마세요.**

예를 들어, 다음과 같이 Effect Event를 선언하고 전달하지 마세요:

```typescript
function Timer() {
  const [count, setCount] = useState(0);

  const onTick = useEffectEvent(() => {
    setCount(count + 1);
  });

  useTimer(onTick, 1000); // 🔴 Avoid: Passing Effect Events

  return <h1>{count}</h1>
}

function useTimer(callback, delay) {
  useEffect(() => {
    const id = setInterval(() => {
      callback();
    }, delay);
    return () => {
      clearInterval(id);
    };
  }, [delay, callback]); // Need to specify "callback" in dependencies
}
```

대신 항상 Effect Event를 사용하는 Effect 바로 옆에 Effect Event를 선언하세요:

```typescript
function Timer() {
  const [count, setCount] = useState(0);
  useTimer(() => {
    setCount(count + 1);
  }, 1000);
  return <h1>{count}</h1>
}

function useTimer(callback, delay) {
  const onTick = useEffectEvent(() => {
    callback();
  });

  useEffect(() => {
    const id = setInterval(() => {
      onTick(); // ✅ Good: Only called locally inside an Effect
    }, delay);
    return () => {
      clearInterval(id);
    };
  }, [delay]); // No need to specify "onTick" (an Effect Event) as a dependency
}
```

Effect Event는 Effect 코드의 비반응형 조각이며 이를 사용하는 Effect 옆에 있어야 합니다.

⏮️ Previous : [Lifecycle of Reactive Effects](./005-리액트%20Lifecycle%20of%20Reactive%20Effects.md)

⏭️ Next : [Removing Effect Dependencies](./007-리액트%20Removing%20Effect%20Dependencies.md)