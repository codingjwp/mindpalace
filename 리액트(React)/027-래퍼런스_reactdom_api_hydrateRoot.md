# hydrateRoot

## 참조

### **`hydrateRoot(domNode, reactNode, options?)`**

`hydrateRoot`를 호출하여 서버 환경에서 React가 이미 만들어둔 HTML에 React를 붙입니다.

```typescript
import { hydrateRoot } from 'react-dom/client';

const domNode = document.getElementById('root');
const root = hydrateRoot(domNode, reactNode);
```

React는 `domNode` 내부에 있는 HTML에 붙어서 그 내부의 DOM을 직접 관리할 것입니다. 온전히 React만으로 만든 App에서는 보통 단일 루트 컴포넌트에 대해 `hydrateRoot`를 단 한 번만 호출할 것입니다.

### 매개변수

- `domNode`: 서버에서 루트 엘리먼트로 렌더링된 [DOM element.](https://developer.mozilla.org/en-US/docs/Web/API/Element)
- `reactNode`: 앞서 존재하는 HTML에 렌더링하기 위한 “React 노드”. 주로 `ReactDOM Server`의 `renderToPipeableStream(<App />)`처럼 메서드로 렌더링된 `<App />`같은 JSX 조각들입니다.
- `options`: React 루트에 옵션을 주기 위한 객체이며 선택적으로 사용 가능합니다..
    - `onRecoverableError`: React가 에러에서 자동으로 회복되었을 때 호출하는 콜백 함수.
    - `identifierPrefix`: React가 ID로 사용하는 접두사로 `useId`로 만들어진 값. 한 페이지에 여러 루트를 사용할 경우 충돌을 피하는 데에 유용합니다. 반드시 서버에서 사용한 것과 동일한 값이어야 합니다.

### 반환값

`hydrateRoot`는 두 메서드가 포함된 객체를 반환합니다: `render`와 `unmount`[.](https://react-ko.dev/reference/react-dom/client/hydrateRoot#root-unmount)

### 주의사항

- `hydrateRoot()`는 서버에서 렌더링된 내용과 후에 렌더링된 내용이 동일할 것을 기대합니다. 동일하지 않은 부분들은 버그로 인식하고 직접 고쳐줘야 합니다.
- 개발 환경에서는 React가 hydration 중에 동일하지 않은 부분에 대해 경고해 줍니다. 속성이 동일하지 않을 경우 해당 속성이 올바르게 적용될 것이라고 보장할 수 없습니다.
- 마크업이 동일하지 않는 경우는 드물고, 모든 마크업을 검증하는 비용은 굉장히 비싸므로, 이 동작은 성능상 중요합니다.
- `hydrateRoot`는 App에서 단 한 번만 호출하게 될 것입니다. 만약 프레임워크를 사용한다면 프레임워크가 대신 호출해줄 수도 있습니다.
- App이 미리 렌더링된 HTML 없이 클라이언트에서만 렌더링하는 경우에는 `hydrateRoot()`를 쓸 수 없습니다. 대신 `createRoot()`를 사용해주세요.

## 사용법

### **서버에서 렌더링된 HTML을 hydrate하기**

```typescript
import { hydrateRoot } from 'react-dom/client';

hydrateRoot(document.getElementById('root'), <App />);
```