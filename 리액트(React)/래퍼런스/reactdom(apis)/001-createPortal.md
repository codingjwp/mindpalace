# createPortal

## 참조

**`createPortal(children, domNode, key?)`**

포털을 생성하려면 `createPortal`을 호출하여 몇 가지 JSX와 렌더링할 DOM 노드를 전달하세요

```typescript
import { createPortal } from 'react-dom';

<div>
  <p>This child is placed in the parent div.</p>
  {createPortal(
    <p>This child is placed in the document body.</p>,
    document.body
  )}
</div>
```

포털은 DOM 노드의 물리적 배치만 변경합니다. 다른 모든 면에서 포털에 렌더링하는 JSX는 이를 렌더링하는 React 컴포넌트의 자식 노드 역할을 합니다.

예를 들어, 자식은 부모 트리가 제공하는 컨텍스트에 접근할 수 있으며, 이벤트는 React 트리에 따라 자식에서 부모로 버블링 됩니다.

### 매개변수

- `children`: JSX 조각 (예: `<div />` 나 `<SomeComponent />`), Fragment (`<>...</>`), 문자열이나 숫자, 또는 이들의 배열과 같이 React로 렌더링할 수 있는 모든 것입니다.
- `domNode`: `document.getElementById()`가 반환하는 것과 같은 일부 DOM 노드. 노드는 이미 존재하고 있어야 합니다. 업데이트 중에 다른 DOM 노드를 전달하면 포털 콘텐츠가 다시 생성됩니다.
- `key`: 포털의 키로 사용할 고유 문자열 또는 숫자를 선택적으로 사용할 수 있다.

### 반환값

`createPortal`은 JSX에 포함되거나 React 컴포넌트에서 반환될 수 있는 React 노드를 반환합니다. React가 렌더링 출력물에서 이를 발견하면, 제공된 `children`을 제공된 `domNode` 안에 배치합니다.

### **주의사항**

포털의 이벤트는 DOM 트리가 아닌 React 트리에 따라 전파됩니다.

예를 들어, 포털 내부를 클릭했을 때 포털이 `<div onClick>`으로 감싸져 있으면 해당 `onClick` 핸들러 이벤트가 실행됩니다.

이로 인해 문제가 발생한다면, 포털 내부에서 이벤트 전파를 중지하거나 포털 자체를 React 트리에서 위로 옮기세요.

## 사용법

### **DOM의 다른 부분으로 렌더링하기**

포털을 사용하면 컴포넌트가 일부 자식을 DOM의 다른 위치로 렌더링할 수 있습니다. 

이를 통해 컴포넌트의 일부가 어떤 컨테이너에 있든 그 컨테이너에서 **탈출**할 수 있습니다.

예를 들어, 컴포넌트는 모달이나 툴팁을 페이지의 나머지 부분 위에, 외부에 표시할 수 있습니다.

포털을 생성하려면 `createPortal`의 결과를 일부 JSX와 함께 렌더링하고 포털이 있어야 할 DOM 노드를 지정합니다.

```typescript
import { createPortal } from 'react-dom';

function MyComponent() {
  return (
    <div style={{ border: '2px solid black' }}>
      <p>This child is placed in the parent div.</p>
      {createPortal(
        <p>This child is placed in the document body.</p>,
        document.body
      )}
    </div>
  );
}
```

React는 사용자가 전달한 JSX에 대한 DOM 노드를 사용자가 제공한 DOM 노드 안에 배치합니다

포털이 없다면 두 번째 `<p>`는 부모 `<div>` 안에 배치되겠지만, 포털은 이를 [document.body](https://developer.mozilla.org/en-US/docs/Web/API/Document/body)로 **텔레포트**시킵니다.

포털은 DOM 노드의 물리적 배치만 변경합니다. 다른 모든 면에서 포털에 렌더링하는 JSX는 이를 렌더링하는 React 컴포넌트의 자식 노드 역할을 합니다.

예를 들어, 자식은 부모 트리가 제공하는 컨텍스트에 접근할 수 있으며 이벤트는 여전히 React 트리에 따라 자식에서 부모로 버블링 됩니다.

### **포털로 모달 렌더링하기**

모달을 불러오는 컴포넌트가 `overflow: hidden` 또는 모달을 방해하는 다른 스타일이 있는 컨테이너 안에 있더라도, 포털을 사용하여 나머지 페이지 위에 떠 있는 모달을 만들 수 있습니다.

`ModalContent`

```typescript
export default function ModalContent({ onClose }) {
  return (
    <div className="modal">
      <div>I'm a modal dialog</div>
      <button onClick={onClose}>Close</button>
    </div>
  );
}
```

`PortalExample`

```typescript
import { useState } from 'react';
import { createPortal } from 'react-dom';
import ModalContent from './ModalContent.js';

export default function PortalExample() {
  const [showModal, setShowModal] = useState(false);
  return (
    <>
      <button onClick={() => setShowModal(true)}>
        Show modal using a portal
      </button>
      {showModal && createPortal(
        <ModalContent onClose={() => setShowModal(false)} />,
        document.body
      )}
    </>
  );
}
```

포털을 사용할 때 앱에 접근할 수 있는지를 확인하는 것이 중요합니다.

예를 들어, 사용자가 포털 안팎으로 자연스럽게 초점을 이동할 수 있도록 키보드 포커스를 관리해야 할 수 있습니다.

모달을 만들 때는 [WAI-ARIA 모달 제작 사례](https://www.w3.org/WAI/ARIA/apg/#dialog_modal)를 따르세요. 커뮤니티 패키지를 사용하는 경우 해당 패키지가 접근 가능한지, 가이드라인을 따르고 있는지 확인하세요.

⏮️ Previous : [Reat DOM APIs](./000-React%20DOM%20APIs.md)

⏭️ Next : [flushSync](./002-flushSync.md)