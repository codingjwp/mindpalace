# Refs와 Portals

## Refs vs State

|Refs|State|
|:---|:---|
|첫번째 컴포넌트 렌더링 사이클에서 Refs는 정의 되지 않습니다.|UI에 직접 반영되는 값을 사용합니다.|
|Refs의 참조 값이 변경되어도 컴포넌트는 재렌더링 되지 않습니다.|UI에 직접 반영되는 값을 사용합니다.|
|DOM 요소에 직접적으로 엑세스하는데 사용합니다.|UI에 직접적인 영향을 주지 않는 값을 사용하면 안됩니다.|

Refs 참조는 다른 변수와 다르게 초기화되거나 지워지지 않습니다.

## Portals

보통의 react 컴포넌트는 APP 컴포넌트의 하위로 작성이 됩니다.

하지만 `react-dom` 을 이용한 `createPortals`는 index.html에 작성되어 있는 다른 element로 렌더링 되도록 할 수 있습니다.

```html
  <body>
    <div id="modal"></div>
    <div id="content">
      <header>
        <h1>The <em>Almost</em> Final Countdown</h1>
        <p>Stop the timer once you estimate that time is (almost) up</p>
      </header>
      <div id="root"></div>
    </div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
```

아래 코드와 같이 `createPortal(JSX코드, 옮길 element)`를 작성해주시고 개발자 도구에서 찾아 보면 `<div id="modal">` 안에 작성되어 있는 걸 확인 할 수 있습니다.

```javascript
import { forwardRef, useImperativeHandle, useRef } from "react"
import { createPortal } from 'react-dom';

const ResultModal = forwardRef(function ResultModal({ targetTime, remainingTime, onReset }, ref) {
  const dialogRef = useRef();
  const userLost = remainingTime <= 0;
  const fommatedRemaingingTime = (remainingTime / 1000).toFixed(2);
  const score = Math.round((1 - remainingTime / (targetTime * 1000)) * 100);

  useImperativeHandle(ref, () => {
    return {
      open() {
        dialogRef.current.showModal();
      }
    }
  });
  return createPortal(
    <dialog ref={dialogRef} className="result-modal" onClose={onReset}>
      {userLost ? <h2>You Lost</h2> : <h2>You Score: {score}</h2> }
      <p>The target timewas <strong>{targetTime} second.</strong></p>
      <p>Yout stopped the timer with <strong>{fommatedRemaingingTime} seconds left.</strong></p>
      <form method="dialog" onSubmit={onReset}>
        <button>Close</button>
      </form>
    </dialog>,
    document.getElementById('modal')
  )
});

export default ResultModal
```
