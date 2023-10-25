# Other Hooks(useId)

> 다른 훅들(useId)

`useId`는 접근성 속성에 전달할 수 있는 고유 ID를 생성하기 위한 React 훅입니다.

```typescript
const id = useId()
```

## 참조

### `useId()`

`useId`는 컴포넌트의 최상위 수준에서 호출하여 고유한 ID를 생성합니다.

```typescript
function PasswordField() {
  const passwordHintId = useId();
```

### 매개변수

- `useId`는 매개변수를 사용하지 않습니다.

### 반환값

- `useId`는 특정 컴포넌트 내 특정 `useId` 와 관련된 고유 ID 문자열를 반환합니다.

### 주의사항

- `useId`를 목록에서 **키를 생성하기 위해 사용하지 마세요.** 키는 데이터에서 생성되어야 합니다.

## 사용법

### **접근성 속성에 대한 고유 ID 생성**

```typescript
function PasswordField() {
  const passwordHintId = useId();
	return (
	<>
	  <input type="password" aria-describedby={passwordHintId} />
	  <p id={passwordHintId}>
	</>
	);
}
```

[aria-describedby](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-describedby)와 같은 [HTML 접근성 속성](https://developer.mozilla.org/ko/docs/Web/Accessibility/ARIA) 은 두 태그가 서로 연관되어 있음을 알 수 있습니다. 예를 들어, 한 요소(input 등)에 대한 설명을 다른 요소(문단 등)가 대신하도록 지정할 수 있습니다.

**ID를 하드코딩하는 것은 React에서 좋은 방법은 아닙니다**. 컴포넌트는 페이지에서 두 번 이상 렌더링될 수 있지만 ID는 고유해야합니다! ID를 하드코딩하는 대신, `useId`로 고유한 ID를 생성하세요.

### 주의사항

서버 렌더링을 사용하면, `useId`와 클라이언트에서 동일한 컴포넌트 트리가 필요합니다.** 서버와 클라이언트에서 렌더링한 트리가 정확히 일치하지 않으면 생성된 ID가 일치하지 않습니다.

### **중복 카운터보다 useId가 더 나은 이유는 무엇일까?**

`useId` 의 주요 이점은 React가 서버 렌더링과 함께 작동하도록 보장한다는 것입니다.

클라이언트 컴포넌트가 hydration 되는 순서가 서버 HTML이 생성된 순서와 일치하지 않을 수 있기 때문에, 증가 카운터로는 이를 보장하기가 매우 어렵습니다.

React 내부에서 `useId`는 호출한 컴포넌트의 **부모 경로**에서 생성됩니다. 그렇기 때문에 클라이언트와 서버 트리가 동일하면 렌더링 순서와 상관 없이 **부모 경로**가 일치하게 되므로, `useId` 역시 일치할 것입니다.

### **여러 관련 요소에 대한 ID 생성**

여러 관련 요소에 ID를 제공해야 하는 경우, `useId`를 호출하여 해당 요소들이 공유하는 접두사를 생성할 수 있습니다.

```typescript
<label htmlFor={id + '-firstName'}>First Name:</label>
<input id={id + '-firstName'} type="text" />
<label htmlFor={id + '-lastName'}>Last Name:</label>
<input id={id + '-lastName'} type="text" />
```

### **생성된 모든 ID에 공유 접두사 지정하기**

단일 페이지에서 여러 개의 독립적인 React 애플리케이션을 렌더링하는 경우,  `createRoot` 또는 `hydrateRoot`를 호출하여 `identifierPrefix`에 옵션으로 전달하세요.

이렇게 하면 생성된 모든 식별자가 지정한 고유한 접두사로 시작하기 때문에 서로 다른 두 앱에서 생성된 ID가 충돌하지 않습니다.

```typescript
const root1 = createRoot(document.getElementById('root1'), {
  identifierPrefix: 'my-first-app-'
});
root1.render(<App />);

const root2 = createRoot(document.getElementById('root2'), {
  identifierPrefix: 'my-second-app-'
});
root2.render(<App />);
```

아래 코드와 같이 실행 할 경우 결과 값 입니다.

`Generated identifier: :my-first-app-r0
Generated identifier: :my-second-app-r1`

```typescript
const passwordHintId = useId();
console.log('Generated identifier:', passwordHintId)
```