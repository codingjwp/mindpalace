# &lt;input&gt;

[브라우저 빌트인 `<input>` 컴포넌트](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input)로 다양한 form input을 렌더링할 수 있습니다.

```typescript
<input />
```

## **참조**

### `<input>`

input을 표시하려면 [브라우저 빌트인 `<input>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input) 컴포넌트를 렌더링 하세요.

```typescript
<input name="myInput" />
```

### Props

다음 props 중 하나를 전달하여 [input을 제어](https://react-ko.dev/reference/react-dom/components/input#controlling-an-input-with-a-state-variable)할 수 있습니다.

- [checked](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement#checked) : 불리언. 체크박스 input 또는 라디오 버튼의 선택 여부를 제어합니다.
- [value](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement#value) : 문자열. 텍스트 input의 텍스트를 제어합니다. (라디오 버튼의 경우 해당 form data를 지정합니다).

다음 `<input>` props는 비제어 input에만 관련이 있습니다

- [defaultChecked](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement#defaultChecked) : 불리언. `type="checkbox"` 및 `type="radio"`의 [기본값](https://react-ko.dev/reference/react-dom/components/input#providing-an-initial-value-for-an-input)을 특정합니다.
- [defaultValue](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement#defaultValue) : 문자열. 텍스트 input의 기본값을 특정합니다.

### **주의사항**

- 체크박스는 `value`(또는 `defaultValue`)가 아닌 `checked`(또는 `defaultChecked`)가 필요합니다.
- 텍스트 input이 문자열 `value` prop을 받으면 제어된 것으로 처리됩니다.
- 체크박스나 라디오 버튼이 불리언 `checked` prop을 받으면 제어된 것으로 처리됩니다.
- input은 동시에 제어되거나 제어되지 않을 수 없습니다.
- input은 수명 동안 제어되거나 제어되지 않는 상태로 전환될 수 없습니다.
- 제어되는 모든 입력에는 `onChange` 이벤트 핸들러가 필요하며, 이 핸들러는 지원 값을 동기적으로 업데이트합니다.

## 사용법

### **input에 대한 label 제공하기**

일반적으로 모든 `<input>`은 [&lt;label&gt;](https://developer.mozilla.org/ko/docs/Web/HTML/Element/label) 태그 안에 배치됩니다. 이는 이 label이 해당 input과 연결되어 있음을 브라우저에 알려줍니다.

사용자가 label을 클릭하면 브라우저가 자동으로 input에 초점을 맞춥니다.

이는 접근성 측면에서도 필수적입니다: 사용자가 관련 input에 초점을 맞추면 스크린 리더가 label 캡션을 알립니다.

`<input>`을 `<label>`에 중첩할 수 없는 경우 동일한 ID를 `<input id>` 및 [&lt;label htmlFor&gt;](https://developer.mozilla.org/en-US/docs/Web/API/HTMLLabelElement/htmlFor)에 전달하여 연결합니다.

한 컴포넌트의 여러 인스턴스 간에 충돌을 피하려면 `useId`를 사용하여 이러한 ID를 생성하십시오.

### **form 제출 시 input 값 읽기**

input 주위에 [&lt;form&gt;](https://developer.mozilla.org/ko/docs/Web/HTML/Element/form)을 추가하고 그 안에 [&lt;button type="submit"&gt;](https://developer.mozilla.org/ko/docs/Web/HTML/Element/button)을 추가합니다. 그러면 `<form onSubmit>` 이벤트 핸들러가 호출됩니다.

기본적으로 브라우저는 form 데이터를 현재 URL로 전송하고 페이지를 새로 고 `e.preventDefault()`를 호출하여 이 동작을 재정의할 수 있습니다.

[new FormData(e.target)](https://developer.mozilla.org/ko/docs/Web/API/FormData)로 form 데이터를 읽습니다.

```typescript
function handleSubmit(e) {
    // 브라우저에서 페이지 리로드 방지
    e.preventDefault();
    // form 데이터 읽기
    const form = e.target;
    const formData = new FormData(form);
    // formData를 페치 본문으로 직접 전달할 수 있습니다:
    fetch('/some-api', { method: form.method, body: formData });
    // 또는 일반 객체로 작업할 수도 있습니다:
    const formJson = Object.fromEntries(formData.entries());
    console.log(formJson);
  }
```

모든 `<input>`에 `name`을 지정하세요(예: `<input name="firstName" defaultValue="Taylor" />`).

지정한 `name`은 form 데이터에서 키로 사용됩니다(예: `{ firstName: "Taylor" }`).

### 주의 사항

기본적으로 `<form>`안에 있는 *모든* `<button>`은 제출됩니다. 의외일 수 있습니다!

`Button` 커스텀  컴포넌트가 있는 경우 `<button>` 대신 [&lt;button type="button"&gt;](https://developer.mozilla.org/ko/docs/Web/HTML/Element/input/button)을 반환하는 것을 고려하세요.

그런 다음 form을 제출해야 *하는* 버튼에 `<button type="submit">`을 사용하세요.

### **모든 키 입력 리렌더링 최적화하기**

제어 input을 사용할 때는 모든 키 입력에 state를 설정합니다.

state가 포함된 컴포넌트가 큰 트리를 다시 렌더링하면 속도가 느려질 수 있습니다. 리렌더링 성능을 최적화할 수 있는 몇 가지 방법이 있습니다.

예를 들어, 모든 키 입력 시 모든 페이지 콘텐츠를 다시 렌더링하는 form으로 시작한다고 가정해 보겠습니다

```typescript
function App() {
  const [firstName, setFirstName] = useState('');
  return (
    <>
      <form>
        <input value={firstName} onChange={e => setFirstName(e.target.value)} />
      </form>
      <PageContent />
    </>
  );
}
```

`<PageContent />`는 input state에 의존하지 않으므로 input state를 자체 컴포넌트로 이동할 수 있습니다.

```typescript
function App() {
  return (
    <>
      <SignupForm />
      <PageContent />
    </>
  );
}

function SignupForm() {
  const [firstName, setFirstName] = useState('');
  return (
    <form>
      <input value={firstName} onChange={e => setFirstName(e.target.value)} />
    </form>
  );
}
```

이제 모든 키 입력에 대해 `SignupForm`만 리렌더링하므로 성능이 크게 향상됩니다.
