# React DOM Components

> React DOM 컴포넌트

React는 모든 브라우저 빌트인 [HTML](https://developer.mozilla.org/en-US/docs/Web/HTML/Element) 및 [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG/Element) 컴포넌트를 지원합니다.

## **기본 컴포넌트들**

모든 브라우저 빌트인 컴포넌트는 일부 props 및 이벤트를 지원합니다.

여기에는 `ref` 및 `dangerouslySetInnerHTML`과 같은 React 전용 props도 포함됩니다.

## **폼 컴포넌트들**

다음 브라우저 빌트인 컴포넌트들은 사용자 입력을 허용합니다

이들은 `value` prop을 전달하면 제어 컴포넌트가 된다는 점에서 특별합니다.

## **다른 모든 HTML 컴포넌트들**

React는 모든 브라우저 빌트인 HTML 컴포넌트를 지원합니다.

### 노트

DOM 표준과 유사하게 React는 prop의 이름에 `camelCase` 규칙을 사용합니다. 예를 들어, `tabindex` 대신 `tabIndex`를 작성합니다. 온라인 변환기를 사용하여 기존 HTML을 JSX로 변환할 수 있습니다.

### **사용자정의 HTML 엘리먼트**

`<my-element>` 처럼 대시`(-)`가 있는 태그를 렌더링하면 React는 사용자정의 HTML 엘리먼트를 렌더링한다고 가정합니다. 

React에서 사용자정의 엘리먼트를 렌더링하는 것은 빌트인 브라우저 태그를 렌더링하는 것과는 다르게 작동합니다.

- 모든 사용자정의 엘리먼트의 props는 문자열로 직렬화되며, 항상 속성(attribute)을 통해 설정할 수 있습니다.
- 사용자정의 엘리먼트는 `className` 대신 `class`를, `htmlForfor`를 받습니다.
- 브라우저 빌트인 HTML 엘리먼트를 `[is](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/is)` 속성을 사용하여 렌더링하는 경우에도 커스텀 요소로 취급됩니다.

### 노트

React 패키지를 최신 실험 버전으로 업그레이드하여 사용해 볼 수 있습니다

- `react@experimental`
- `react-dom@experimental`

실험 버전의 React에는 버그가 있을 수 있습니다. 상용 환경에서는 사용하지 마세요

## **모든 SVG 컴포넌트들**

React는 모든 브라우저 빌트인 SVG 컴포넌트를 지원합니다. 여기에는 다음이 포함됩니다

### 노트

DOM 표준과 유사하게, React는 prop 이름에 `camelCase` 규칙을 사용합니다. 예를 들어, `tabindex` 대신 `tabIndex`를 작성합니다. 온라인 변환기를 사용하여 기존 SVG를 JSX로 변환할 수 있습니다

네임스페이스 속성은 콜론(:) 없이 작성해야 합니다 (아래 예시)

- `xlink:actuate`는 `xlinkActuate`가 됩니다
- `xlink:arcrole`는 `xlinkArcrole`가 됩니다

📄 [**Common components (e.g. &lt;div&gt;)**](./001-Common%20components.md)

📄 [**&lt;input&gt;**](./002-input.md)

📄 [**&lt;option&gt;**](./003-option.md)

📄 [**&lt;progress&gt;**](./004-progress.md)

📄 [**&lt;select&gt;**](./005-select.md)

📄 [**&lt;textarea&gt;**](./006-textarea.md)
