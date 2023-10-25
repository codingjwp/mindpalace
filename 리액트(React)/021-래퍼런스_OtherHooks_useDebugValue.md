# Other Hooks(useDebugValue)

> 다른 훅들(useDebugValue)

`useDebugValue` 는 React 개발자 도구에서 커스텀 훅에 레이블을 추가해주는 React 훅입니다.

```typescript
useDebugValue(value, format?)
```

## 참조

### `useDebugValue(value, format?)`

읽을 수 있는 디버그 값을 표시하려면 **커스텀 훅**의 최상위 레벨에서 `useDebugValue`을 호출합니다.

```typescript
import { useDebugValue } from 'react';

function useOnlineStatus() {
  useDebugValue(isOnline ? 'Online' : 'Offline');
}
```

### 매개변수

- `value` : React 개발자 도구에서 표시하려는 값입니다. 모든 유형을 가질 수 있습니다.
- `format` : 포매팅 함수. 컴포넌트가 검사할 때, React 개발자 도구는 인수로 포매팅 함수를 호출한 다음 반환된 포매팅된 값을 표시합니다. 사용하지 않을 시 `value` 값이 출력됩니다.

### 반환값

- `useDebugValue`는 아무것도 반환하지 않습니다.

## 사용법

### **커스텀 훅에 레이블 추가**

커스텀 훅의 최상위 레벨에서 `useDebugValue`를 호출하여 React 개발자 도구가 읽을 수 있는 디버그 값을 표시합니다.

```typescript
import { useDebugValue } from 'react';

function useOnlineStatus() {
  useDebugValue(isOnline ? 'Online' : 'Offline');
}
```

![react devtool](https://github.com/codingjwp/mindpalace/assets/113403155/961ecf72-1e39-4663-b211-37dd48cd0687)


**모든 커스텀 훅에 디버그 값을 추가하지 마십시오. 공유 라이브러리의 일부이고 검사하기 어려운 복잡한 내부 데이터를 가진 커스텀 훅에 가장 유용합니다.**

### **디버그 값의 형식 지정 연기**

```typescript
useDebugValue(date, date => date.toDateString());
```

포매팅 함수는 디버그 값을 매개변수로 받고 변환된 값을 반환해야 합니다. 컴포넌트가 검사할 때, React 개발자 도구가 이 함수를 호출하고 그 결과를 표시합니다.

이렇게 사용하면 컴포넌트를 실제로 조사하지 않는 한 비용이 많이 들 수 있는 포메팅 로직을 실행하지 않아도 됩니다.

예를 들어, `date`가 날짜 값인 경우, 컴포넌트를 렌더링 할 때마다 `toDateString()`을 호출하지 않습니다.