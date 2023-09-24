# Effect Hooks(useInsertionEffect)

> Effect 훅들(useInsertionEffect)

`useInsertionEffect`는 `useEffect`의 버전 중 하나로, DOM 변이 전에 실행됩니다.

```typescript
useInsertionEffect(setup, dependencies?)
```

`useInsertionEffect`는 CSS-in-JS 라이브러리 작성자를 위한 훅입니다. 

CSS-in-JS 라이브러리에서 작업 중에 스타일을 주입하고자 하는 경우가 아니라면, `useEffect`나 `useLayoutEffect`가 더 나을 수 있습니다.

## 참조

### **`useInsertionEffect(setup, dependencies?)`**

`useInsertionEffect`를 호출하여 DOM 변이 전에 스타일을 주입합니다.

```typescript
import { useInsertionEffect } from 'react';

// CSS-in-JS 라이브러리 내부에서
function useCSS(rule) {
  useInsertionEffect(() => {
    // ... <style> 태그를 여기에 주입합니다.
  });
  return rule;
}
```

### 매개변수

- `setup` : `useEffect`랑 동일합니다.
- `dependencies` : `useEffect`랑 동일합니다.

### 반환값

- `useInsertionEffect`는 `undefined`를 반환합니다.

### 주의사항

- `useInsertionEffect` 내부에서는 state를 업데이트할 수 없습니다.
- `useInsertionEffect`가 실행될 때까지는 refs가 아직 첨부되지 않았고, DOM이 아직 업데이트되지 않았습니다.

## 사용법

### **CSS-in-JS 라이브러리에서 동적 스타일 삽입하기**

CSS-in-JS에는 세 가지 일반적인 접근 방식이 있습니다.

1. 컴파일러를 사용하여 CSS 파일로 정적 추출
2. 인라인 스타일. 예: `<div style={{ opacity: 1 }}>`
3. 런타임에 `<style>` 태그 삽입

**런타임 환경에서 `<style>` 태그 주입은 다음 두 가지 이유로 권장하지 않습니다.**

1. 런타임 주입은 브라우저에서 스타일을 훨씬 더 자주 다시 계산하도록 합니다.
2. 런타임 주입이 React 라이프사이클에서 잘못된 시점에 발생하면 속도가 매우 느려질 수 있습니다.

첫 번째 문제는 해결할 수 없지만 `useInsertionEffect`를 사용하면 두 번째 문제를 해결할 수 있습니다.

`useInsertionEffect`를 호출하여 DOM 변경 전에 스타일을 주입합니다.

```typescript
// CSS-in-JS 라이브러리 코드 내부에
let isInserted = new Set();
function useCSS(rule) {
  useInsertionEffect(() => {
    // 앞서 설명한 것처럼 <style> 태그의 런타임 주입은 권장하지 않습니다.
    // 하지만 런타임 주입을 해야한다면, useInsertionEffect에서 수행하는 것이 중요합니다.
    if (!isInserted.has(rule)) {
      isInserted.add(rule);
      document.head.appendChild(getStyleForRule(rule));
    }
  });
  return rule;
}

function Button() {
  const className = useCSS('...');
  return <div className={className} />;
}
```

### 스타일을 주입하는 예시

```typescript
// 컴포넌트 정의
function Button({ children }) {
  return <button className="button">{children}</button>;
}

// 스타일 정의
const style = document.createElement("style");
style.textContent = `
  .button {
    background-color: blue;
    color: white;
    padding: 10px;
    border-radius: 5px;
  }
`;
document.head.appendChild(style);
```

⏮️ Previous : [Effect Hooks(useLayoutEffect)](./007-Effect%20Hooks(useLayoutEffect).md)

⏭️ Next: [Performance Hooks(useMemo)](./009-Performance%20Hooks(useMemo).md)