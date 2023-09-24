# Responding to Event

> 이벤트에 응답하기

React를 사용하면 JSX에 Event 핸들러를 추가할 수 있음. 

Event 핸들러는 **click**, **hover**, **input의 focus**등과 같은 상호작용에 반응하여 발생하는 자체 함수임.

## 배울 내용들

- Event 핸들러를 작성하는 다양한 방법
- 부모 컴포넌트로부터 Event 핸들링 로직을 전달하는 방법
- 이벤트가 전파되는 방식과 중지하는 방법.

## 이벤트 핸들러 추가방법

Event 핸들러를 추가하려면 먼저 함수를 정의한 다음 이를 적절한 JSX 태그에 props으로 전달.

```javascript
export default function Button() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}
```

`handleClick` 함수를 정의한 다음 `<button>`에 **prop**으로 전달

Event 핸들러 함수(예: `handleClick`)는:

- 일반적으로 컴포넌트 안에 정의
- `handle`로 시작하는 이름 뒤에 이벤트이름이 오도록 함. (관례상 `handle`뒤에 이벤트 이름을 붙이는 것이 일반적)
- **JSX**에 인라인으로 Event 핸들러 정의할 수 있음(화살표 함수도 사용 가능).

```javascript
<button onClick={function handleClick() {
  alert('You clicked me!');
}}>

//or

<button onClick={() => {
  alert('You clicked me!');
}}>
```

### 주의할 점

1. Event 핸들러에 전달되는 함수는 호출하는 게 아니라 전달되어야 함.
2. 호출 할 경우 React가 이를 기억하고 사용자가 버튼을 클릭할 때만 함수를 호출하도록 지시.
3. 하지만 전달 할 경우 **rendering** 중에 즉시 함수를 시작 이는 `JSX { }` 내부의 Javascript가 바로 실행되기 때문.

```javascript
// 함수의 전달(올바름)
<button onClick={handleClick}/>
// 함수 호출(잘못됨)
<button onClick={handleClick()}/>
```

1. 인라인으로 코드를 작성할 시 에는 다른 방식으로 나타남.
2. `alert('...')` 와 같이 코드를 호출하면 클릭시 실행되지 않고, 컴포넌트가 렌더링될 때마다 실행
3. `() => alert('...')}` 익명 함수로 감싸주면 클릭시 실행 가능.

```javascript
// 함수의 전달(올바름)
<button onClick={() => alert('...')}/>
// 함수 호출(잘못됨)
<button onClick={alert('...')}/>
```

### Event  핸들러에서 props 읽기

Event 핸들러는 컴포넌트 내부에서 선언되기 때문에 컴포넌트 props에 접근할 수 있음.

```javascript
function AlertButton({ message, children }) {
  return (
    <button onClick={() => alert(message)}>
			{/* ... */}
    </button>
  );
}
```

### Event 핸들러에서 props로 전달

부모 컴포넌트가 자식 Event 핸들러를 지정하고 싶을 때 자식 컴포넌트가 부모로부터 받은 **prop**를 event 핸들러로 아래의 코드와 같이 전달

`handlePlayClick`함수를 `Button` 컴포넌트에 `onClick` **props**로 전달 한다.

```javascript
function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}

function PlayButton({ movieName }) {
  function handlePlayClick() {
    alert(`Playing ${movieName}!`);
  }

  return (
    <Button onClick={handlePlayClick}>
      Play "{movieName}"
    </Button>
  );
}

export default function Toolbar() {
  return (<PlayButton movieName="Kiki's Delivery Service" />);
}
```

### Event 핸들러 props 이름 정하기

`<button>`, `<div>`와 같은 기본 제공 컴포넌트 경우 `onClick`과 같은 [브라우저 이벤트 이름](https://react-ko.dev/reference/react-dom/components/common#common-props)만 지원. 하지만 자체 컴포넌트를 빌드할 때는 Event 핸들러 **props**의 이름을 원하는 방식으로 지정가능.

관례상 Event 핸들러 **props**는 **on**으로 시작하고 그뒤에 대문자로 작성.

컴포넌트가 여러 상호작용을 지원하는 경우, 앱별 개념에 따라 Event 핸들러 **props**의 이름을 지정 가능.

### 추가 내용

Event 핸들러에 적절한 HTML 태그를 사용해야합니다.

예를 들어 `<div onClick={handleClick}>`대신 `<button onClick={handleClick}>`을 사용 할 것.

실제 브라우저의 `<button>` 을 사용하면 키보드 탐색과 같은 기본 브라우저 동작을 사용할 수 있음.

## 이벤트 전파

Event 핸들러는 컴포넌트에 있을 수 있는 모든 하위 컴포넌트의 이벤트도 포착. 이벤트가 트리 위로 **버블** 또는 **전파** 되는 것을 이벤트가 발생한 곳에서 시작하여 트리 위로 올라 간다고 함.

```javascript
export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('You clicked on the toolbar!');
    }}>
      <button onClick={() => alert('Playing!')}>
        Play Movie
      </button>
    </div>
  );
}
```

해당 버튼을 클릭하면 `alert('Playing!')` 가 실행되고 `alert('You clicked on the toolbar!')` 이 실행 됩니다. 

### 주의할 점

첨부한 **JSX 태그**에서만 작동하는 `onScroll`을 제외한 모든 이벤트는 React에서 전파됨.

### 전파 중지하기

Event 핸들러는 event 객체를 유일한 인수로 받음. 이 객체를 사용하여 이벤트에 대한 정보를 읽을 수 있음.

`e.stopPropagation();` 를 이용해서 상위 컴포넌트에 도달하지 못하도록 할수 있음.

### 캡처 단계 이벤트

드물지만 하위 요소에서 전파가 중지된 경우에도 하위 요소의 모든 이벤트를 포착해야 하는 경우가 있음. 예를 들어 전파 로직에 관계없이 모든 클릭을 분석도구에 기록하고자 하면 이벤트 이름 끝에 `Capture`을 추가하면 작업을 수행할 수 있음.

```javascript
<div onClickCapture={() => { /* this runs first | 먼저 실행됨 */ }}>
  <button onClick={e => e.stopPropagation()} />
  <button onClick={e => e.stopPropagation()} />
</div>
```

캡처 이벤트는 라우터나 분석과 같은 코드에는 유용하지만 앱코드에는 잘 사용하지 않음.

### 전파의 대안으로 핸들러 전달하기(??)

```javascript
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  );
}
```

이 패턴은 전파에 대한 **대안**을 제공. 

자식 컴포넌트가 이벤트를 처리하는 동시에 부모 컴포넌트가 몇 가지 추가 동작을 지정할 수 있게 해줌.

**propagation**과 달리 자동이 아니지만 이 패턴의 장점은 특정 이벤트의 결과로 실행되는 전체 체인 코드를 명확하게 따라갈 수 있다는 점.

전파에 의존하고 있고 어떤 핸들러가 실행되고 왜 실행되는지 추적하기 어려운 경우 대신 이 접근 방식을 시도해 볼 수 있음.

### 기본 동작 방지

일부 브라우저 이벤트 경우 연결된 기본 동작이 있음. 예를 들어 `<form>` **submit** 이벤트는 내부의 버튼을 클릭할 때 발생하여 기본족으로 전체 페이지를 다시 로드 함.

이벤트 객체 에서 `e.preventDefault()` 를 호출하면 전체 페이지를 다시 로드하는 동작을 방지.

`e.stopPropagation()` 과 `e.preventDefault()`를 혼동 하지말기.

- `[e.stopPropagation()](https://developer.mozilla.org/docs/Web/API/Event/stopPropagation)`은 위 태그에 연결된 이벤트 핸들러의 실행을 중지.
- `[e.preventDefault()](https://developer.mozilla.org/docs/Web/API/Event/preventDefault)`는 해당 이벤트가 있는 몇 가지 이벤트에 대해 기본 브라우저 동작을 방지.

## 이벤트 핸들러에 부작용이 생길수 있나요?

이벤트 핸들러는 부작용이 가장 많이 발생하는 곳.

렌더링 함수와 달리 이벤트 핸들러는 **순수할 필요가 없기 때문에** 타이핑에 대한 응답으로 **input** 값을 변경하거나 **button** 클릭에 대한 응답으로 목록을 변경하는 등 무엇을 변경하기에 좋은 곳.

하지만 일부 정보를 변경할려면 먼저 정보를 저장할 방법이 필요. state를 사용하여 이 작업을 수행.

⏮️ Previous: [Adding Interactivity](./000-리액트%20Adding%20Interactivity.md)

⏭️ Next: [State: A Components’s Memory](./002-리액트%20State%20A%20Component’s%20Memory.md)