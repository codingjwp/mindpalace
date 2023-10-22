# createRoot

## 참조

### **`createRoot(domNode, options?)`**

`createRoot`를 호출하면 브라우저 DOM 엘리먼트 안에 콘텐츠를 표시할 수 있는 React 루트를 생성합니다.

```typescript
import { createRoot } from 'react-dom/client';

const domNode = document.getElementById('root');
const root = createRoot(domNode);
```

React는 `domNode`에 대한 루트를 생성하고 그 안에 있는 DOM을 관리합니다. 루트를 생성한 후에는 `root.render`를 호출해 그 안에 React 컴포넌트를 표시해야 합니다.

```typescript
root.render(<App />);
```

온전히 React만으로 빌드된 앱에서는 일반적으로 루트 컴포넌트에 대한 `createRoot` 호출이 하나만 있습니다. 

페이지의 일부에 React를 **뿌려서** 사용하는 페이지의 경우에는 루트를 필요한 만큼 많이 작성할 수도 있습니다.

### 매개변수

- `domNode`: [DOM 엘리먼트.](https://developer.mozilla.org/en-US/docs/Web/API/Element) React는 이 DOM 엘리먼트에 대한 루트를 생성하고 렌더링된 React 콘텐츠를 표시하는 `render`와 같은 함수를 루트에서 호출할 수 있도록 합니다.
- `options`: React 루트에 대한 옵션이 있는 객체입니다. 선택적으로 적용할 수 있습니다.
    - `onRecoverableError`: React가 오류로부터 자동으로 복구될 때 호출되는 콜백.
    - `identifierPrefix` : React가 `useId`에 의해 생성된 ID에 사용하는 문자열 접두사. 같은 페이지에서 여러개의 루트를 사용할 때 충돌을 피하는 데 유용합니다.

### 반환값

- `createRoot`는 `render`와 `unmount` 두 가지 메서드가 있는 객체를 반환합니다.

### 주의사항

- 앱이 서버에서 렌더링되는 경우 `createRoot()`는 사용할 수 없습니다. 대신 `hydrateRoot()`를 사용하세요.
- 앱에 `createRoot` 호출이 오직 하나만 있을 가능성이 높습니다. 프레임워크를 사용하는 경우 프레임워크가 이 호출을 대신 수행할 수도 있습니다.
- 컴포넌트의 자식이 아닌 DOM 트리의 다른 부분(예: 모달 또는 툴팁)에 JSX 조각을 렌더링하려는 경우, `createRoot` 대신 `createPortal`을 사용하세요.

### **`root.render(reactNode)`**

`root.render`를 호출하여 JSX 조각(“React 노드”)을 React 루트의 브라우저 DOM 노드에 표시합니다.

### **`root.unmount()`**

`root.unmount`를 호출하면 React 루트 내부에서 렌더링된 트리를 삭제합니다.

## 사용법

이것은 매우 느리게 느껴질 수 있습니다! 이 문제를 해결하기 위해 서버에서 또는 빌드 중에 컴포넌트로부터 초기 HTML을 생성할 수 있습니다.

그러면 방문자는 JavaScript 코드가 로드되기 전에 텍스트를 읽고, 이미지를 보고, 링크를 클릭할 수 있습니다. 이 최적화를 기본적으로 수행하는 프레임워크를 사용하는 것이 좋습니다. 실행 시점에 따라 이를 서버 사이드 렌더링 **(SSR)** 또는 정적 사이트 생성 **(SSG)** 이라고 합니다