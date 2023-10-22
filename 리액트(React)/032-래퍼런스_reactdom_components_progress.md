# &lt;progress&gt;

## 참조

### **`<progress>`**

진행률 표시기를 렌더링하려면 [브라우저 빌트인 &lt;progress&gt;](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress) 컴포넌트를 렌더링합니다.

```typescript
<progress value={0.5} />
```

### **Props**

`<progress>`는 모든 일반적인 요소의 props를 지원합니다.

- [max](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress#attr-max) : 숫자. 최대값을 지정합니다. 기본값은 `1`입니다.
- [value](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress#attr-value) : `0`에서 `max` 사이의 숫자, 혹은 정해지지 않은 경우 `null`입니다. 수행한 작업의 양을 지정합니다.

## **사용법**

### **진행률 표시기 제어**

진행률 표시기를 표시하려면 `<progress>` 컴포넌트를 렌더링합니다. 

`0`과 `max` 사이의 숫자 `value`를 전달할 수 있습니다. `max` 값을 전달하지 않으면 기본적으로 `1`로 간주됩니다

작업 진행 중이 아닌 경우, 진행률 표시기를 불확정 상태로 설정하려면 `value={null}`을 전달합니다.

```typescript
export default function App() {
  return (
    <>
      <progress value={0} />
      <progress value={0.5} />
      <progress value={0.7} />
      <progress value={75} max={100} />
      <progress value={1} />
      <progress value={null} />
    </>
  );
}
```