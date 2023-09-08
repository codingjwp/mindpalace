# Conditional rendering

> 조건부 렌더링

컴포넌트는 여러 조건에 따라 다른 것을 표시해야하는 경우가 많음.

React에서 `if문, &&, ?` 연산자 같은 자바스크립트 구문을 사용해 조건부로 JSX를 렌더링 할 수 있음.

## 배울 내용들

- 조건에 따라 다른 JSX를 반환하는 방법
- JSX를 조건부로 포함하거나 제외하는 방법
- React 코드베이스에서 흔히 접할 수 있는 조건부 구문 단축키

## 조건에 따른 JSX 반환법

### if else문을 사용한 JSX 렌더링

```javascript
function Item({ name, isPacked }) {
  if (isPacked) {
    return <li className="item">{name} ✔</li>;
  }
  return <li className="item">{name}</li>;
}
```

## 조건부로 아무것도 반환하지 않는 null

조건에 따라 아무것도 렌더링 안하고 싶을 경우 적용.

```javascript
if (isPacked) {
  return null;
}
return <li className="item">{name}</li>;
```

실제로 컴포넌트에서 null을 반환하는 것은 렌더링하려는 개발자를 놀라게 할 수 있기 때문에 일반적이지 않음  

부모 컴포넌트의 JSX에 컴포넌트를 조건부로 포함하거나 제외하는 경우가 더 많음.  

## 조건부 삼항 연산자 (? :)

```javascript
return (
  <li className="item">
    {isPacked ? name + ' ✔' : name}
  </li>
);
```

## 논리 AND 연산자 (&&)

조건이 `true` 일 경우 렌더링 아닐 경우 아무것도 렌더링하지 않기.

```javascript
return (
  <li className="item">
    {name} {isPacked && '✔'}
  </li>
);
```

### `&&` 왼쪽에 숫자를 넣지 마세요

`messageCount`가 `0`일 때 아무것도 렌더링하지 않는다고 생각하지만 실제로는 0자체를 렌더링 함.

```javascript
messageCount && <p>New messages</p>
```

이 문제를 해결 하기 위해 왼쪽을 boolean으로 만들기

```javascript
messageCount > 0 && <p>New messages</p>
```

⏮️ Previous : [Passing Props to a Component](./009-%EB%A6%AC%EC%95%A1%ED%8A%B8%20Passing%20props%20to%20a%20component.md)

⏭️ Next : [Rendering Lists](./011-%EB%A6%AC%EC%95%A1%ED%8A%B8%20Rendering%20lists.md)
