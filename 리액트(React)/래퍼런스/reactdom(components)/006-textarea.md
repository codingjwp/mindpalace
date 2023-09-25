# &lt;textarea&gt;

## 참조

### **`<textarea>`**

textarea을 표시하려면 [브라우저 빌트인 &lt;textarea&gt; 컴포넌트](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea)를 렌더링 합니다.

```typescript
<textarea name="postContent" />
```

### Props

`value`를 전달할 때는 전달된 value를 업데이트 하는 `onChange` 핸들러도 함께 전달해야 합니다.

- `value`: 문자열. textarea 내부의 텍스트를 제어합니다.

`<textarea>`가 비제어 컴포넌트인 경우에는, 대신 `defaultValue`를 전달할 수 있습니다

- `defaultValue`: 문자열. textarea의 초기값을 지정합니다

### **주의사항**

- `<textarea>something</textarea>`처럼 자식 요소를 전달하는 것은 허용되지 않습니다. 초기값은 `defaultValue`를 사용하세요.
- 문자열 `value` prop을 제공하면 제어 컴포넌트로 취급됩니다.
- 제어 컴포넌트이면서 동시에 비제어 컴포넌트일 수는 없습니다.
- 생명주기 동안 제어 컴포넌트와 비제어 컴포넌트 사이를 전환할 수 없습니다.
- 제어컴포넌트는 값을 동기적으로 업데이트 하는 `onChange` 이벤트 핸들러가 필요합니다.

## 사용법

textarea를 표시하려면 `<textarea>`를 렌더하세요.

[rows](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#rows) 및 [cols](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#cols) 속성으로 기본 크기를 정할 수 있지만, 기본적으로는 사용자가 재조정할 수 있습니다.

재조정을 비활성화하려면 CSS에서`resize: none`을 지정하세요.

### **textarea에 label 제공하기**

```typescript
export default function NewPost() {
  return (
    <label>
      Write your post:
      <textarea name="postContent" rows={4} cols={40} />
    </label>
  );
}
```

### 주의사항

HTML과 달리, 초기 텍스트를 `<textarea>Some content</textarea>`와 같이 자식 요소로 전달하는 것은 지원하지 않습니다.

## 문제해결

### **키를 누를 때마다 커서가 처음으로 이동합니다**

제어 textarea의 경우 `onChange` 중에 state 변수를 DOM의 값으로 업데이트해야 합니다.

```typescript
function handleChange(e) {
  // 🔴 버그: e.target.value가 아닌 다른 값으로 업데이트 시도
  setFirstName(e.target.value.toUpperCase());
}
```

⏮️ Previous : [&lt;select&gt;](./005-select.md)

⏭️ Next : [Reat DOM APIs](../reactdom(apis)/000-React%20DOM%20APIs.md)