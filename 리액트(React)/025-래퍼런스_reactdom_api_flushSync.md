# flushSync

## 참조

### `flushSync(callback)`

`flushSync`를 호출하면 React가 보류 중이던 작업을 강제로 flush하고 DOM을 동기적으로 업데이트 합니다.

```typescript
import { flushSync } from 'react-dom';

flushSync(() => {
  setSomething(123);
});
```

부분의 경우 `flushSync`를 피할 수 있습니다. `flushSync`는 최후의 수단으로 사용하세

### 매개변수

- `callback` : 함수. React는 즉시 이 콜백을 호출하고 여기에 포함된 모든 업데이트를 동기적으로 flush합니다. 또한 보류 중인 모든 업데이트, Effect 또는 Effect 내부의 업데이트를 flush할 수도 있습니다. 이 `flushSync` 호출의 결과로 업데이트가 일시 중단되면 폴백이 다시 표시될 수 있습니다.

### 반환값

`flushSync` 는 `undefined`를 반환합니다.

### 주의사항

- `flushSync`는 자주 사용되지 않으며 앱 성능을 저하시킬 수 있습니다.
- `flushSync`는 보류 중인 Suspense 경계를 강제로 `fallback` state로 표시할 수 있습니다.
- `flushSync`는 보류 중인 Effect들을 실행하고, 반환하기 전에 포함된 모든 업데이트를 동기적으로 적용할 수 있습니다.
- `flushSync`는 콜백 내부의 업데이트를 flush하기 위해, 필요한 경우 콜백 외부의 업데이트를 flush할 수도 있습니다. 예를 들어, 클릭으로 인해 보류 중인 업데이트가 있는 경우, React는 콜백 내부의 업데이트를 flush하기 전에 해당 업데이트를 먼저 flush할 수 있습니다.

## 사용법

### **서드파티 통합을 위한 업데이트 flush하기**

동기식 업데이트를 강제해야 할 수도 있습니다.

예를 들어, `onbeforeprint` 브라우저 API를 사용하면 인쇄 대화 상자가 열리기 직전에 페이지를 변경할 수 있습니다. 

이 기능은 문서를 인쇄할 때 더 보기 좋게 표시할 수 있는 사용자 지정 인쇄 스타일을 적용하는 데 유용합니다. 