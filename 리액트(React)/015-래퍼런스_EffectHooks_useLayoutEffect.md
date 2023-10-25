# Effect Hooks(useLayoutEffect)

> Effect 훅들(useLayoutEffect) 

`useLayoutEffect`는 브라우저가 화면을 다시 채우기 전에 실행되는 버전의 `useEffect`입니다.

```typescript
useLayoutEffect(setup, dependencies?)
```

`useLayoutEffect`는 성능을 저하시킬 수 있습니다. 가급적이면 `useEffect`를 사용하세요.

## 참조

```typescript
useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []);
```

### 매개변수

- `setup` : useEffect와 동일
- `dependencies` : useEffect와 동일

### 리턴값

- `useLayoutEffect`는 `undefined`를 반환합니다.

### 주의사항

- LayoutEffect는 **클라이언트에서만 실행됩니다.** 서버 렌더링 중에는 실행되지 않습니다.
- `useLayoutEffect` 내부의 코드와 여기에서 예약된 모든 state 업데이트는 **브라우저가 화면을 다시 그리는 것을 차단합니다.** 과도하게 사용하면 앱이 느려집니다. 가급적이면 `useEffect`를 사용하세요.

## 사용법

### **브라우저에서 화면을 다시 그리기 전 레이아웃 측정하기**

대부분의 컴포넌트는 무엇을 렌더링할지 결정하기 위해 화면에서의 위치와 크기를 알 필요가 없습니다.

때론 그것만으로는 충분하지 않을 수 있습니다. 마우스 오버 시 요소 옆에 툴팁을 표시하는 것을 상상해 보세요.

공간이 충분하면 툴팁이 요소 위에 표시되어야 하지만, 공간이 충분하지 않으면 아래에 표시되어야 합니다.

툴팁을 올바른 최종 위치에 렌더링하려면 툴팁의 높이(즉,상단에 표시하기에 충분한지 여부)를 알아야 합니다.

### **useLayoutEffect vs useEffect**

`useLayoutEffect`는 브라우저가 다시 그리는 것을 차단합니다**

`useEffect`는 브라우저를 차단하지 않습니다**

React는 브라우저가 화면을 다시 그리기 전에 `useLayoutEffect` 내부의 코드와 그 안에서 예약된 모든 state 업데이트가 처리되도록 보장합니다.

### 노트

두 번의 패스로 렌더링하고 브라우저를 차단하면 성능이 저하됩니다. 가능하면 피하세요.

## 문제해결

### `useLayoutEffect`가 서버에서 아무것도 수행하지 않습니다**

사용자 또는 프레임워크가 **서버 렌더링**을 사용하는 경우, React 앱은 초기 렌더링을 위해 서버의 HTML로 렌더링됩니다.

이를 통해 JavaScript 코드가 로드되기 전에 초기 HTML을 표시할 수 있습니다. 문제는 서버에는 레이아웃 정보가 없다는 것입니다.

이 문제가 발생하는 경우 몇 가지 다른 옵션이 있습니다

- 컴포넌트를 클라이언트 전용으로 표시하세요. 이렇게 하면 서버 렌더링 중에 가장 가까운 `<Suspense>` 경계까지의 콘텐츠를 로딩(예: spinner 나 glimmer)으로 대체하도록 React에 지시합니다.
- **hydration** 후에만 `useLayoutEffect`를 사용하여 컴포넌트를 렌더링할 수 있습니다.
- 컴포넌트를 외부 데이터 저장소와 동기화하고 레이아웃 측정이 아닌 다른 이유로 `useLayoutEffect`에 의존하는 경우, 대신 서버 렌더링을 지원하는 `useSyncExternalStore`를 고려하세요