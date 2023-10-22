# &lt;select&gt;

## 참조

### **`<select>`**

[브라우저 빌트인 &lt;select&gt; 컴포넌트](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select)로 옵션들과 함께 셀렉트 박스를 렌더링할 수 있습니다.

```typescript
<select>
  <option value="someOption">Some option</option>
  <option value="otherOption">Other option</option>
</select>
```

### Props

`<select>`는 모든 공통 엘리먼트의 props를 지원합니다

- `value`: 문자열(혹은 `multiple={true}`일 경우 문자열로 구성된 배열). 어떤 옵션이 선택되는지를 제어합니다. 각 문자열 값은 `<select>` 안에 있는 `<option>`들의 `value`와 일치합니다.
- `defaultValue`: 문자열(혹은 [multiple={true}](https://react-ko.dev/reference/react-dom/components/select#enabling-multiple-selection)일 경우 문자열로 구성된 배열). 초기 선택값을 지정합니다.

다음 `<select>` prop들은 비제어 및 제어 컴포넌트 모두에 영향을 미칩니다

- [autoComplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select#attr-autocomplete) : 문자열. 가능한 [자동 완성 동작](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete#values)을 지정합니다.
- [autoFocus](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select#attr-autofocus) : 불리언. `true`일 경우 마운트시 엘리먼트에 초점이 맞춰집니다.
- `children`: `<select>` 는 [&lt;option&gt;](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option), [&lt;optgroup&gt;](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/optgroup), [&lt;datalist&gt;](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/optgroup) 컴포넌트를 자식으로 받습니다. 최종적으로 위 허용된 컴포넌트들 중 하나(`<option>`, `<optgroup`, `<datalist>`)를 렌더링하는 컴포넌트도 괜찮습니다. `option` 태그들을 렌더링하는 컴포넌트의 경우, 각 `<option>`에는 반드시 `value`가 있어야 합니다.

### **주의사항**

- HTML과 달리, `<option>`에 `selected` 속성을 전달하는 것은 지원되지 않습니다. 대신, 비제어 컴포넌트의 경우 `<select defaultValue>`를, 제어 컴포넌트의 경우 `<select value>`를 사용하세요.
- 문자열 `value` prop을 제공하면 제어 컴포넌트로 취급합니다.
- 생명주기 동안 제어 컴포넌트와 비제어 컴포넌트 사이를 전환할 수 없습니다.
- 제어컴포넌트는 값을 동기적으로 업데이트 하는 `onChange` 이벤트 핸들러가 필요합니다.

## 사용법

셀렉트 박스를 표시하려면 `<option>` 컴포넌트 목록을 `<select>` 안에 넣어 렌더하세요. 각 `<option>`에는 폼 제출시 데이터가 될 `value`가 있어야 합니다.

```typescript
export default function FruitPicker() {
  return (
    <label>
      Pick a fruit:
      <select name="selectedFruit">
        <option value="apple">Apple</option>
        <option value="banana">Banana</option>
        <option value="orange">Orange</option>
      </select>
    </label>
  );
}
```

### **셀렉트 박스에 label 제공하기**

흔히 `<select>`를 `<label>` 태그 안에 위치시킵니다. 이렇게 하면 해당 label이 셀렉트 박스와 연결되어 있음을 의미하게 됩니다.

사용자가 label을 클릭하면 브라우저가 셀렉트 박스에 초점을 맞춥니다.

스크린 리더는 사용자가 셀렉트 박스에 초점을 맞추면 label 캡션을 읽어주므로, 접근성을 위해서도 이렇게 하는 것이 필수적입니다.

### 주의 사항

HTML과 달리, 개별 `<option>`에 `selected` 속성을 전달하는 것은 지원되지 않습니다.

### **form 제출시 셀렉트 박스 값 읽기**

셀렉트 박스를 [&lt;form&gt;](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form)으로 감싸고, form 안에 [&lt;button type="submit"&gt;](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button)을 넣으세요. 그러면 `<form onSubmit>` 이벤트 핸들러가 호출됩니다.

기본적으로 브라우저는 form 데이터를 현재 URL로 전송하고 페이지를 새로고침 합니다. 

`e.preventDefault()`를 호출하여 이 동작을 재정의할 수 있습니다.

form 데이터를 읽으려면 [new FormData(e.target)](https://developer.mozilla.org/en-US/docs/Web/API/FormData)를 사용하세요.

### 노트

`<select name="selectedFruit" />`과 같이 `<select>`에 `name`를 지정하세요. 이렇게 지정한 `name`은 `{ selectedFruit: "orange" }`와 같이 form 데이터의 키로 사용될 것입니다.

`<select multiple={true}>`의 경우, [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData)에는 선택된 값들이 각각 이름-값 쌍으로 분리되어 들어갑니다. 위 예시의 콘솔 로그를 자세히 살펴보세요.
