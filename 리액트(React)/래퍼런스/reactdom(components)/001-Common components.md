# Common components (e.g. &lt;div&gt;)

[&lt;div&gt;](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/div)와 같은 모든 브라우저 빌트인 컴포넌트는 몇 가지 일반적인 props와 이벤트를 지원합니다.

## 참조

### **기본 컴포넌트 (예: `<div>`)**

```typescript
<div className="wrapper">Some content</div>
```

### Props

아래의 특별한 React props는 모든 빌트인 컴포넌트에서 지원됩니다

- `children`: React 노드(엘리먼트, 문자열, 숫자, portal, `null`, `undefined` 및 boolean과 같은 빈 노드 또는 기타 React 노드의 배열). 컴포넌트 내부의 콘텐츠를 지정합니다. JSX를 사용할 때 일반적으로 `<div><span /></div>`와 같은 태그를 중첩하여 암묵적으로 `children` prop을 지정합니다.
- `angerouslySetInnerHTML`: 내부에 원시 HTML 문자열이 있는 `{ __html: '<p>일부 html</p>' }` 형식의 객체로, 내부에 원시 HTML 문자열이 있습니다. DOM 노드의 [innerHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) 속성을 재정의하고 전달된 HTML을 내부에 표시합니다. 이 함수는 매우 주의해서 사용해야 합니다! 내부 HTML을 신뢰할 수 없는 경우 [XSS](https://en.wikipedia.org/wiki/Cross-site_scripting) 취약점이 발생할 위험이 있습니다.
- `ref`: `useRef`또는`createRef`의 ref 객체, `ref` 콜백 함수, 또는 레거시 ref에 대한 문자열. ref는 이 노드의 DOM 엘리먼트로 채워집니다.
- `suppressContentEditableWarning`: 불리언. `true`이면, 일반적으로 함께 작동하지 않는 (일반적으로 함께 작동하지 않는) `children`과 `contentEditable={true}`가 모두 있는 엘리먼트에 대해 React가 표시하는 경고를 억제합니다. 이 값을 사용하면 `contentEditable` 콘텐츠를 수동으로 관리하는 텍스트 입력 라이브러리를 빌드할 때 사용합니다.
- `suppressHydrationWarning`: 불리언. 서버 렌더링을 사용하는 경우 일반적으로 서버와 클라이언트가 서로 다른 콘텐츠를 렌더링할 때 경고가 표시됩니다. 타임스탬프와 같은 일부 드문 경우에서는 정확한 일치를 보장하기가 매우 어렵거나 불가능합니다. `suppressHydrationWarning`을 `true`로 설정하면 React는 해당 엘리먼트의 속성과 콘텐츠가 일치하지 않을 때 경고하지 않습니다. 이는 한 단계 깊이에서만 작동하며, 탈출구로 사용하기 위한 것입니다. 과도하게 사용하지 마세요.
- `style`: CSS 스타일이 있는 객체(예: `{ fontWeight: 'bold', margin: 20 }`). DOM [style](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/style) 프로퍼티와 마찬가지로 CSS 프로퍼티 이름도`font-weight` 대신 `fontWeight`와 같이 `camelCase`로 작성해야 합니다. 문자열이나 숫자를 값으로 전달할 수 있습니다. `width: 100`과 같이 숫자를 전달하면 React는 단위가 없는 프로퍼티가 아니라면 값에 자동으로 `px`(“픽셀”)를 추가합니다. 스타일 값을 미리 알 수 없는 동적 스타일에만 `style`을 사용하는 것을 권장합니다. 그 외의 경우에는 `className`과 함께 일반 CSS 클래스를 적용하는 것이 더 효율적입니다.

### **`ref` 콜백 함수**

ref 객체(예: `useRef`에서 반환하는 객체) 대신 `ref` 속성에 함수를 전달할 수 있습니다.

```typescript
<div ref={(node) => console.log(node)} />
```

### 매개변수

`node`: DOM 노드 또는 `null`. React는 ref가 연결될 때 DOM 노드를 전달하고, ref가 분리될 때 `null`을 전달합니다. 모든 렌더링에서 `ref` 콜백에 대해 동일한 함수 참조를 전달하지 않는 한, 컴포넌트를 다시 렌더링할 때마다 콜백이 일시적으로 분리되었다가 다시 연결됩니다.

### **React 이벤트 객체**

이벤트 핸들러는 React 이벤트 객체를 받게 되며, **합성 이벤트**라고도 합니다.

```typescript
<button onClick={e => {
  console.log(e); // React 이벤트 객체
}} />
```

### **Properties**

- [bubbles](https://developer.mozilla.org/en-US/docs/Web/API/Event/bubbles) : 불리언. 이벤트가 DOM을 통해 버블을 발생시킬지 여부를 반환합니다.
- [cancelable](https://developer.mozilla.org/en-US/docs/Web/API/Event/cancelable): 불리언. 이벤트를 취소할 수 있는지 여부를 반환합니다.
- [currentTarget](https://developer.mozilla.org/en-US/docs/Web/API/Event/currentTarget): DOM 노드. React 트리에서 현재 핸들러가 연결된 노드를 반환합니다.
- [defaultPrevented](https://developer.mozilla.org/en-US/docs/Web/API/Event/defaultPrevented): 불리언. preventDefault`가 호출되었는지 여부를 반환합니다.
- [eventPhase](https://developer.mozilla.org/en-US/docs/Web/API/Event/eventPhase): 숫자. 이벤트가 현재 어떤 단계에 있는지 반환합니다.
- [isTrusted](https://developer.mozilla.org/en-US/docs/Web/API/Event/isTrusted): 불리언. 이벤트가 사용자에 의해 시작되었는지 여부를 반환합니다.
- [target](https://developer.mozilla.org/en-US/docs/Web/API/Event/target): DOM 노드. 이벤트가 발생한 노드(먼 자식일 수 있음)를 반환합니다.
- [timeStamp](https://developer.mozilla.org/en-US/docs/Web/API/Event/timeStamp): 숫자. 이벤트가 발생한 시간을 반환합니다.
- nativeEvent: DOM [Event](https://developer.mozilla.org/en-US/docs/Web/API/Event). 원본 브라우저 이벤트 객체입니다.

### 메서드

- [preventDefault()](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault): 이벤트에 대한 기본 브라우저 액션을 방지합니다.
- [stopPropagation()](https://developer.mozilla.org/en-US/docs/Web/API/Event/stopPropagation): React 트리를 통한 이벤트 전파를 중지합니다.
- isDefaultPrevented(): `preventDefault`가 호출되었는지 여부를 나타내는 불리언 값을 반환합니다.
- isPropagationStopped(): `stopPropagation`가 호출되었는지 여부를 나타내는 불리언 값을 반환합니다.
- persist(): React DOM에서는 사용되지 않습니다. React Native에서는 이벤트 이후 이벤트의 프로퍼티를 읽으려면 이 함수를 호출합니다.
- `isPersistent()`: React DOM에서는 사용되지 않습니다. React Native에서는 `persist`가 호출되었는지 여부를 반환합니다.

### **`ClipboardEvent` 핸들러 함수**

[Clipboard API](https://developer.mozilla.org/ko/docs/Web/API/Clipboard_API) 이벤트에 대한 이벤트 핸들러 유형입니다.

```typescript
<input
  onCopy={e => console.log('onCopy')}
  onCut={e => console.log('onCut')}
  onPaste={e => console.log('onPaste')}
/>
```

### 매개변수

- `e`: 추가 [ClipboardEvent](https://developer.mozilla.org/ko/docs/Web/API/ClipboardEvent) 속성을 가진 React 이벤트 객체입니다:
    - [clipboardData](https://developer.mozilla.org/en-US/docs/Web/API/ClipboardEvent/clipboardData)

## 사용법

### **내부 HTML을 위험하게 설정하는 경우**

다음과 같이 원본 HTML 문자열을 요소에 전달할 수 있습니다.

```typescript
const markup = { __html: '<p>some raw html</p>' };
return <div dangerouslySetInnerHTML={markup} />;
```

**이렇게 작성하는 것은 위험합니다. 기본 DOM [innerHTML](https://developer.mozilla.org/ko/docs/Web/API/Element/innerHTML) 속성과 마찬가지로 극도의 주의를 기울여야 합니다! 마크업이 완전히 신뢰할 수 있는 출처에서 제공되지 않는 한, 이런 식으로 [XSS](https://ko.wikipedia.org/wiki/Cross-site_scripting) 취약점을 도입하는 것은 위험한 일입니다.**

```typescript
const post = {
  // 이 콘텐츠가 데이터베이스에 저장되어 있다고 가정해 보겠습니다.
  content: `<img src="" onerror='alert("you were hacked")'>`
};

export default function MarkdownPreview() {
  // 🔴 보안 허점: 신뢰할 수 없는 input을 위험하게 dangerouslySetInnerHTML로 전달합니다.
  const markup = { __html: post.content };
  return <div dangerouslySetInnerHTML={markup} />;
}
```

HTML에 포함된 코드가 실행됩니다. 해커는 이 보안 허점을 이용해 사용자 정보를 훔치거나 사용자 대신 작업을 수행할 수 있습니다. 

**신뢰할 수 있는 위생 처리된 데이터에 대해서만 `dangerouslySetInnerHTML`를 사용하세요.**

⏮️ Previous : [React DOM Components](./000-React%20DOM%20Components.md)

⏭️ Next : [input](./002-input.md)