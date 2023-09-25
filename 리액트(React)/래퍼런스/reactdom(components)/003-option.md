# &lt;option&gt;

## 참조

### **`<option>`**

[브라우저 빌트인 `<option>` 컴포넌트](https://developer.mozilla.org/ko/docs/Web/HTML/Element/option)를 사용하면 [&lt;select&gt;](https://react-ko.dev/reference/react-dom/components/select) 박스 안에 옵션을 렌더링할 수 있습니다.

```typescript
<select>
  <option value="someOption">Some option</option>
  <option value="otherOption">Other option</option>
</select>
```

### **Props**

`<option>` 은 모든 일반적인 엘리먼트 props를 지원합니다.

- [disabled](https://developer.mozilla.org/ko/docs/Web/HTML/Element/option#disabled) : boolean값. `true` 인 경우 옵션을 선택할 수 없으며 흐리게 보입니다.
- [label](https://developer.mozilla.org/ko/docs/Web/HTML/Element/option#label) : string값. 옵션의 의미를 지정합니다. 지정하지 않으면 옵션 내부의 텍스트가 사용됩니다.
- [value](https://developer.mozilla.org/ko/docs/Web/HTML/Element/option#value) : 폼에서 부모 요소인 `<select>`를 제출할 때, 이 옵션이 선택된 경우에 사용됩니다.

### 주의사항

React는 `<option>`에서 `selected` 속성을 지원하지 않습니다. 

대신 비제어 셀렉트 박스의 경우에는 `<select defaultValue>`로, 제어 셀렉트 박스의 경우에는 `<select value>`로, 이 옵션의 `value`를 부모에 전달하세요.

⏮️ Previous : [&lt;input&gt;](./002-input.md)

⏭️ Next : [&lt;progress&gt;](./004-progress.md)