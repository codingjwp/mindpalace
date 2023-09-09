# Queueing a Series of State Updates

> 여러 state 업데이트를 큐에 담기

state 변수를 설정하면 다음 렌더링이 큐에 들어감. 그러나 경우에 따라 큐에 넣기 전에 값에 대해 여러 작업을 수행 하고 싶을때. React가 state 업데이트를 어떻게 배치하면 좋을지 이해하는 것이 도움될 것.

## 배울 내용들

1. 일관 처리(batching)이란 무엇이며 React가 여러 state 업데이트 처리하는 방법
2. 동일한 state 변수에서 여러 업데이트를 적용하는 방법

## state 업데이트 일괄 처리

```javascript
function handleNumberUp() {
	setNumber(number + 1);
	setNumber(number + 1);
	setNumber(number + 1);
}
```

이전 챕터 내용같이 **각 렌더링의 state값은 고정**되어 있으므로 첫 번쨰 렌더링의 이벤트 핸들러의 number 값은 항상 `0` 입니다.

**React는 state 업데이트를 하기 전에 이벤트 핸들러의 모든 코드가 실행될 떄 까지 기다립니다**. 이 때문에 리렌더링은 모든 setNumber() 호출이 완료된 이후에만 일어남

이럴 경우 너무 많은 리렌더링을 촉발하지 않고 여러 컴포넌트에서 나온 다수의 state변수를 업데이트 할 수 있지만 이는 이베트 핸들러와 그 안에 있는 무수한 코드가 완료될 때 까지 UI를 업데이트 되지 않는다는 의미.

이러한 동작을 **일괄 처리(batching)**라고 하며 이 동작은 React 앱을 훨씬 빠르게 실행할 수 있게 해주며 일부 변수만 업데이트된 혼란스러운 렌더링을 처리하지 않아도 됩니다.

**React는 클릭과 같은 여러 의도적인 이벤트에 대해 일괄 처리 하지 않으며** 각 클릭은 개별적으로 처리하며 일반적으로 안전한 경우에만 일괄 처리를 수행합니다.

## **다음 렌더링 전에 동일한 state 변수를 여러 번 업데이트하기**

흔한 사례는 아니지만 다음 렌더링전 동일한 state 변수를 여러번 업데이트 하고 싶다면 `setNumber(number + 1)`이 아니라 `setNumber(n ⇒ n + 1)`와 같이 **큐의 이전 state**를 기반으로 다음 state를 계산하는 함수를 전달.

이 방법은 React에게 “state 값으로 무언가를 하라”고 지시하는 방법입니다.

n ⇒ n + 1 은 **업데이터 함수(updater function)**라고 불림.

이를 state 설정자 함수에 전달 할 경우

1. React는 이벤트 핸들러의 다른 코드가 모두 실행된 후에 이 함수가 처리되도록 큐에 넣기.
2. 다음 렌더링 중에 React는 큐를 순회하여 최종 업데이트된 state를 제공.

| queued update | n | returns |
| --- | --- | --- |
| n => n + 1 | 0 | 0 + 1 = 1 |
| n => n + 1 | 1 | 1 + 1 = 2 |
| n => n + 1 | 2 | 2 + 1 = 3 |

### state를 교체한 후 업데이트를 하면 어떻게 될까?

```javascript
function handleCountUp() {
	setNumber(number + 5);
	setNumber(n => n + 1);
}
```

| queued update | n | returns |
| --- | --- | --- |
| n => n + 1 | 0 | 0 + 1 = 1 |
| n => n + 1 | 1 | 1 + 1 = 2 |
| n => n + 1 | 2 | 2 + 1 = 3 |

`setState(x)`가 실제로는 `setState(n => x)` 처럼 동작되지만 `n`이 사용되지 않는다

### **업데이트 후 state를 바꾸면 어떻게 될까?**

```javascript
function handleCountUp() {
	setNumber(number + 5);
	setNumber(n => n + 1);
	setNumber(42);
}
```

| queued update | n | returns |
| --- | --- | --- |
| “replace with 5” | 0 (unused) | 5 |
| n => n + 1 | 5 | 5 + 1 = 6 |
| “replace with 42” | 6 (unused) | 42 |

이벤트 핸들러가 완료되면 React는 리렌더링을 실행. 

리렌더링하는 동안 React는 큐를 처리.

업데이터 함수는 렌더링 중에 실행되므로, **업데이터 함수는 순수해야 하며** 결과만 반환.

업데이터 함수 내부에서 state를 변경하거나 다른 사이드 이팩트를 실행하지말 것. 

Strict 모드에서 React는 각 업데이터 함수를 두 번 실행(두 번째 결과는 버림)하여 실수를 찾을 수 있도록 도움을 줌.

### 명명 규칙

업데이터 함수 인수의 이름은 해당 state 변수의 첫 글자로 지정하는 것이 일반적.

좀더 자세한 코드를 선호하는 경우 

1. `setEnabled(enabled ⇒ !enabled)` : 전체 staet 변수 이름 반복
2. `setEnabled(prevEnabled ⇒ !prevEnabled)` : 접두가 prev를 사용

⏮️ Previous: [State as a Snapshot](./017-리액트%20State%20as%20a%20Snapshotmd)

⏭️ Next: [Updating Objects in State](./019-리액트%20Updating%20Objects%20in%20State.md)