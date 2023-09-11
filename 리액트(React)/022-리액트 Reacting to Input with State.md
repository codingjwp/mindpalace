# Reacting to Input with State

> state로 입력에 반응하기

React는 UI를 조작하는 선언적인 방법을 제공.

UI를 개별적으로 직접 조작하는 대신 컴포넌트가 있을 수 있는 다양한 상태를 기술하고, 사용자 입력에 반응하여 각 상태들의 사이를 전환.

## 배울 내용들

1. 선언형  UI 프로그래밍과 명령형 UI 프로그래밍 차이점
2. 컴포넌트가 있을 수 있는 다양한 시각적 state를 열거하는 방법
3. 코드에서 다른 시각적 state 간의 변경을 촉발하는 방법

## 선언형 UI와 명령형 UI의 차이점

사용자의 행동에 따라 UI가 어떻게 변하는지 대해 생각해 보자.

사용자가 답변을 제출할 수 있는 양식에 대해 생각을 해 보았을 때.

- 폼에 무언가를 입력하면 **Submit** 버튼이 **활성화** 됨
- **Submit** 버튼을 누르면 폼과 버튼이 **비활성화** 되고 스피너가 나타남.
- 네트워크 요청이 성공하면 form은 숨겨질 것이고 감사 메세지가 나타남.
- 네트워크 요청이 실패하면 오류 메세지가 보일 것이고 form은 다시 **활성화** 됨

위 내용은 **명령형 프로그래밍**에서 인터랙션을 구현하는 방법에 직접적으로 해당

컴퓨터에 스피너부터 버튼까지 각 요소에 명령을 내려 컴퓨터에 **어떻게** UI를 업데이트할 **내용을 지시**해야 하므로 이를 **명령형** 이라고 부름.

### 명령형  UI 프로그래밍 예시 코드

```javascript
async function handleFormSubmit(e) {
  e.preventDefault();
  disable(textarea);
  disable(button);
  show(loadingMessage);
  hide(errorMessage);
  try {
    await submitForm(textarea.value);
    show(successMessage);
    hide(form);
  } catch (err) {
    show(errorMessage);
    errorMessage.textContent = err.message;
  } finally {
    hide(loadingMessage);
    enable(textarea);
    enable(button);
  }
}

function handleTextareaChange() {
	textarea.value.length === 0 ? disable(button) : enable(button);
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

function enable(el) {
  el.disabled = false;
}

function disable(el) {
  el.disabled = true;
}

function submitForm(answer) {
  // Pretend it's hitting the network.
  return new Promise((resolve, reject) => {
    setTimeout(() => {
			answer.toLowerCase() == 'istanbul' ? resolve()
				: reject(new Error('Good guess but a wrong answer. Try again!'));
    }, 1500);
  });
}

let form = document.getElementById('form');
let textarea = document.getElementById('textarea');
let button = document.getElementById('button');
let loadingMessage = document.getElementById('loading');
let errorMessage = document.getElementById('error');
let successMessage = document.getElementById('success');
form.onsubmit = handleFormSubmit;
textarea.oninput = handleTextareaChange;
```

UI를 조작하는 것은 위와 같이 단순히 고립된 예제에서는 잘 동작하지만 더 복잡한 시스템에서는 관리하기가 기하급수적으로 어려워짐.

React는 직접 UI를 즉 컴포넌트를 직접 활성화, 비활성화 하지 않고 보여주거나 숨기지도 않음. **대신 표시할 내용을 선언하면** React가 UI를 업데이트할 방법을 알아냅니다.

## UI를 선언적인 방식으로 생각하기

위에 명령형 방식을 React로 다시 구현하는 과정을 해보자.

- 컴포넌트의 다양한 시작적 상태를 **식별**
- 상태 변화를 촉발하는 요소를 **파악**
- useState를 사용하여 메모리의 상태를 **표현**
- 비필수적인 state 변수를 **제거**
- 이벤트 핸들러를 **연결하여** state를 설정

### 스탭 1: 컴포넌트의 다양한 시작적 상태 식별

사용자에게 표시될  수 있는 UI의 다양한 **상태**를 모두 시각화

- **비어있음(Empty)** : form의 Submit 버튼은 비활성화
- **입력중(Typing)** : form의 Submit 버튼은 활성화
- **제출중(Submitting)** : form은 완전히 비활성화 되었고 Spinner가 표시됨
- **성공시(Success)** : form대신 Thank you메세지가 표시됨
- **실패시(Error)** : 입력중 상태와 동일하지만 추가로 오류메세지 표시됨

### 한 번에 여러 시각적 상태 표시

컴포넌트에 시각적 상택 ㅏ많은 경우 한페이지에 모두 표시하는 것이 편리할 수 있음.

이런 페이지를 보통 “living styleguide”또는 “storybook”이라 부릅니다.

### 스탭 2: 상태변경을 촉발하는 요인 파악하기

두 종류의 입력에 대한 응답으로 상태 변경을 촉발할 수 있음.

- **사람의 입력** : 버튼 클릭, 필드 입력, 링크 이동 등
- **컴퓨터의 입력 :** 네트워크 에서 응답 도착, 시간초과, 이미지 로딩 등

두 경우 모두 **state 변수를 설정해야 UI를 업데이트 할 수 있음**

- **text 입력을 변경(**사람) 하면 textbox가 비어있는지 여부에 따라 비어있음 state에서 입력중 state로 또는 그 반대로 전환해야 함.
- **제출 버튼 클릭**(사람)하면 제출중 state로 전환해야함
- **네트워크 응답 성공(**컴퓨터)시 성공 state로 전환해야함
- **네트워크 요청 실패(**컴퓨터)시 일치하는 오류 메세지와 함께 오류 state로 전환해야함

사람 입력에는 종종 이벤트 핸들러가 필요.

이 흐름을 시각화하는데 도움되도록 종이에 각 상태가 적힌 원 그리고 상태 사이의 변경사항을 화살표로 그리면 흐름을 파악할 수 있을 뿐 아니라 구현하기 훨씬 전에 버그를 분류할 수 있음

![Untitled](Reacting%20to%20Input%20with%20State%2017284c9cfc17409f906577885805f412/Untitled.png)

### 스탭 3: 메모리의 상태를 `useState`로 표현하기

메모리에서 컴포넌트의 시각적 상태를 `useState` 로 표현해야 함 이 과정은 단순함이 핵심. 각 상태 조각은 움직이는 조각이며 **가능한 적은 수의 움직이는 조각을 원함.** 복잡할 수록 버그만 더 발생함.

반드시!! **필요한 state부터** 시작하세요. 예를 들면 입력에 대한 `answer`를 저장하고 가장 마지막에 발생한  `error`도 저장해야 함

```javascript
const [answer, setAnswer] = useState('');
const [error, setError] = useState(null);
```

그런 다음 앞서 설명한 시각적 상태 중 어떤 상태를 표시할지를 나타내는 state 변수가 필요함.

가장 좋은 방법을 즉시 생각하기 힘들다면 가능한 모든 시각적 상태를 확실하게 다룰 수 있는 만큼 충분한 state를 추가하는 것 부터 시작

```javascript
const [isEmpty, setIsEmpty] = useState(true);
const [isTyping, setIsTyping] = useState(false);
const [isSubmitting, setIsSubmitting] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);
const [isError, setIsError] = useState(false);
```

### 스탭 4: 비필수적인 state 변수 제거

state 콘텐츠의 중복을 피해 필수적인 것만 추적하며 state구조를 리팩토링하는데 약간의 시간을 투자하면 컴포넌트를 더 쉽게 이해하고 중복을 줄이며 의도하지 않은 경우를 피할 수 있음.

**state가 사용자에게 보여주기를 원하는 유효한 UI를 나타내지 않는 경우를 방지**

state 변수에 대해 물어볼 수 있는 몇가지 질문들.

- **state가 모순을 야기하나요?** : 예를 들어 `isTyping`과 `isSubmitting`은 동시에 `true`일 수 없음. 이러한 모순은 일반적으로 state가 충분히 제약되지 않았음을 의미. 두 boolean 조합은 네가지가 가능하지만 유효한 state는 세가지 뿐, `typing`, `submitting`, `success` 를 하나의 status로 결합
- **다른 state 변수에 이미 같은 정보가 있는가?** : `isEmpty`와 `isTyping`은 동시에 `true`가 될수 없음. 이를 각 state 변수로 분리하면 동기화되지 않아 버그가 발생할 위험이 있음. `isEmpty`를 제거하고 대신 `answer.length === 0` 으로 확인 가능
- **다른 state 변수를 뒤집으면 동일한 정보를 얻을 수 있는가?** : `isError`는 `error !== null`을 대신 확인 할 수 있기 때문에 필요하지 않음

```javascript
const [answer, setAnswer] = useState('');
const [error, setError] = useState(null);
const [status, setStatus] = useState('typing'); // 'typing', 'submitting','success'
```

### reducer로 불가능한 state 제거하기

`typing`, `submitting`, `success` 이 세가지 state 변수는 이 **form**의 상태를 충분히 잘 표현 하였지만 여전히 완전히 설명되지 않은 중간 상태가 몇 가지 있음. 예를 들어 `null`이 아닌 `error`는 state가 `success`일 때는 의미가 없습니다. state를 좀더 정확하게 모델링 할려면 reducer로 분리 하면 됩니다. 이에 대한 자세한 내용은 뒤에서 배움.

### 스탭 5: 이벤트 핸들러를 연결하여 state 설정하기

모든 상호작용을 state 변화로 표현하면 나중에 기존 상태를 깨지 않고도 새로운 시각적 상태를 도입할 수 있으며 인터랙션 자체의 로직을 변경하지 않고도 각 state에 표시되어야 하는 항목을 변경할 수 있음.

```javascript
import { useState } from 'react';

export default function Form() {
  const [answer, setAnswer] = useState('');
  const [error, setError] = useState(null);
  const [status, setStatus] = useState('typing');

  if (status === 'success') {
    return <h1>That's right!</h1>
  }

  async function handleSubmit(e) {
    e.preventDefault();
    setStatus('submitting');
    try {
      await submitForm(answer);
      setStatus('success');
    } catch (err) {
      setStatus('typing');
      setError(err);
    }
  }

  function handleTextareaChange(e) {
    setAnswer(e.target.value);
  }

  return (
    <>
			{/* ... 생략 */}
      <form onSubmit={handleSubmit}>
        <textarea
          value={answer}
          onChange={handleTextareaChange}
          disabled={status === 'submitting'}
        />
				{/* ... 생략 */}
        <button disabled={ answer.length === 0 || status === 'submitting'}>
          Submit
        </button>
        {error !== null &&
          <p className="Error">
            {error.message}
          </p>
        }
      </form>
    </>
  );
}

function submitForm(answer) {
  // Pretend it's hitting the network.
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let shouldError = answer.toLowerCase() !== 'lima'
      if (shouldError) {
        reject(new Error('Good guess but a wrong answer. Try again!'));
      } else {
        resolve();
      }
    }, 1500);
  });
}
```

⏮️ Previous : [Managing State](./021-리액트%20Managing%20State.md)

⏭️ Next : [Choosing the State Structure](./023-리액트%20Choosing%20the%20State%20Structuremd)