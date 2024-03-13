# 컴포넌트, JSX, 속성 상태 등

## 목차

- [컴포넌트의 모든것](#컴포넌트의-모든것)
- [컴포넌트를 사용하면 좋은 점](#컴포넌트를-사용하면-좋은-점)
- [JSX와 리액트 컴포넌트](#jsx와-리액트-컴포넌트)
- [리액트 컴포넌트 처리 과정 & 컴포넌트 트리 생성법](#리액트-컴포넌트-처리-과정--컴포넌트-트리-생성법)
- [동적 값 출력 및 활용](#동적-값-출력-및-활용)
- [이미지 파일 로딩](#이미지-파일-로딩)
- [prop으로 컴포넌트 재사용](#prop으로-컴포넌트-재사용)
- [이벤트 처리하기](#이벤트-처리하기)
- [ui를 업데이트 하지 않는법](#ui를-업데이트-하지-않는-법)

## 컴포넌트의 모든것

컴포넌트는 잠재적으로 재사용이 가능한 구성요소

### 컴포넌트를 사용하면 좋은 점

- 작은 블록을 만들고 이를 통해 UI 구성 하여 재사용이 가능합니다.
- 비슷한 코드가 저장됩니다.
- 디자인과 개발원칙을 따르며 관삼사가 분리됩니다.

## JSX와 리액트 컴포넌트

JSX(Javascript syntax Extension) 문법 확장자 사용합니다.  
브라우저에서 사용이 불가능 합니다. 그렇기 떄문에 개발 서버에서 브라우저에 적용가능하게 변환시킵니다.

컴포넌트로 인식되기 위한 두가지 규칙

- 함수 제목이 대문자로 사용되어야 합니다.
- 함수에서 렌더링 가능한 JSX 값이 반환되어야 합니다.


## 리액트 컴포넌트 처리 과정 & 컴포넌트 트리 생성법

아래의 코드 `creatRoot`에서 HTML에 있는 `<div id="root">`를 기준으로 JSX 파일을 생성합니다.

```javascript
import ReactDOM from "react-dom/client";

import App from "./App.jsx";

const entryPoint = document.getElementById("root");
ReactDOM.createRoot(entryPoint).render(<App />);
```

리액트 컴포넌트 트리에서 모든 전반적인 JSX 코드를 결합하여 전반적인 DOM을 생성합니다.

커스텀 컴포넌트는 단순한 함수로 반한된 값(JSX)를 분석하고 HTML및 DOM 내장요소만 남게되고 이를 화면에 렌더링 합니다.  


## 동적 값 출력 및 활용

아래 `{}` 경우를 이용하여 동적 값을 출력할 수 있습니다.

> **중요: if-문, for-루프, 함수 정의 및 기타 블록 문은 여기에 허용되지 않습니다! 값을 직접 생성하는 표현식만 허용됩니다.**

```javascript
function Header() {
  const description = reactDescriptions(getRandomInt(2));
  return (
    <header>
      <img src="src/assets/react-core-concepts.png" alt="Stylized atom" />
      <h1>React Essentials</h1>
      <p>
        {description} React concepts you will need for almost any app you are going to build!
      </p>
    </header>
  )
}
```

## 이미지 파일 로딩

`src -> assets`와 같이 이미지 경로를 설정할 시 배포과정에서 이미지를 분실할 수 있습니다.

```javascript
<img src="src/assets/react-core-concepts.png" alt="Stylized atom" />
```

그런 경우를 방지하기 위한 방법

- 임포트하기 `import reactImage form './assets/react-core-concepts.png`
- 

## Prop으로 컴포넌트 재사용

이론적으로 재사용 가능한 컴포넌트도 만들지만 실제로는 한번만 사용 가능한 컴포넌트를 만들때도 있습니다.

### props

props는 컴포넌트로 데이터를 전달하고 그 데이터를 해당 컴포넌트에서 사용이 가능합니다.

props는 키와 값으로 데이터가 전달됩니다. 그렇기 때문에 키 값이 동일하면 아래와 같이 활요이 가능합니다.
```javascript
<CoreConcept {...CORE_CONCEPTS[1]}/>
```

## 이벤트 처리하기

컴포넌트 함수 내에 event 핸들러 함수를 정의하면 컴포넌트의 Props과 state에 엑세스할 수 있다는 이점이 있습니다.  

함수 이름을 사용해야하여 `handleClick()` 괄호를 포함 시키면 안됩니다.


## UI를 업데이트 하지 않는 법

기본적으로 리액트는 컴포넌트 함수를 코드 내에서 처음 발견했을때 한번 밖에 실행하지 않습니다.

컴포넌트 함수를 재실행 시켜야한다는 걸 알려주는 State(상태)




