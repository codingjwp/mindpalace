# `<StrictMode>`

`<StrictMode>`를 사용하면 개발 중에 컴포넌트에서 흔히 발생하는 버그를 조기에 발견할 수 있습니다.

```typescript
<StrictMode>
  <App />
</StrictMode>
```

## 참조

### `<StrictMode>`

`StrictMode`를 사용하여 내부의 컴포넌트 트리에 대한 추가 개발 동작 및 경고를 활성화 하세요.

Strict Mode 는 다음과 같은 개발 전용 동작을 활성화합니다.

- 불완전한 렌더링으로 인한 버그를 찾기 위해 한 번 더 렌더링합니다.
- Effect 클린업이 누락되어 발생한 버그를 찾기위해 Effect를 한 번 더 실행합니다.
- 지원 중단된 API의 사용 여부를 확인합니다.

### props

- `StrictMode`는 props를 허용하지 않습니다.

### 주의사항

`<StrictMode>`로 감싼 트리 내부에서 Strict 모드를 해제할 수 있는 방법은 없습니다.

이로써 `<StrictMode>` 내의 모든 컴포넌트가 검사된다는 확신을 가질 수 있습니다.

하나의 제품을 작업하는 두 팀이 검사의 가치에 대해 의견이 다를 경우, 합의를 하거나, `<StrictMode>`를 트리에서 아래로 이동해야 할 것입니다.

## 사용법

### **개발환경에서 이중 렌더링으로 발견된 버그 수정하기**

작성하는 React 컴포넌트는 동일한 입력(props, state, context)이 주어졌을 때 항상 동일한 JSX를 반환해야 합니다.

이 규칙을 위반하는 컴포넌트는 예측할 수 없는 동작을 하고 버그를 유발합니다. Strict Mode는 **개발 환경에서** 실수로 작성한 불순한 코드를 찾을 수 있도록 다음의 일부 함수(순수 함수만)를 **두 번** 호출합니다.

- 컴포넌트 함수 본문(최상위의 로직만 있으므로 이벤트 핸들러 내부의 코드는 포함되지 않습니다.)
- `useState`, `set` 함수, `useMemo`, `useReducer`에 전달한 함수
- `constructor`, `render`, `shouldComponentUpdate`와 같은 일부 클래스 컴포넌트 메서드 (전체 목록 보기)

### **Strict Mode로 지원 중단 경고 수정하기**

React는 `<StrictMode>` 트리 내의 컴포넌트가 더 이상 지원되지 않는 API중 하나를 사용하는 경우 경고를 표시합니다

- `findDOMNode`. 대안을 살펴보세요.
- `UNSAFE_componentWillMount`와 같은 `UNSAFE_` 클래스 라이프 사이클 메서드들. 대안을 살펴보세요.
- 예전 컨텍스트 API들 (`childContextTypes`, `contextTypes`, `getChildContext`) 대안을 살펴보세요.
- 예전 문자열 ref(this.refs). 대안을 살펴보세요.

이러한 API는 주로 오래된 클래스 컴포넌트에서 사용되므로 최신 앱에는 거의 나타나지 않습니다.