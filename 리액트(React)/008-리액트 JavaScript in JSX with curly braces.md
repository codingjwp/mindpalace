# JavaScript in JSX with curly braces

> 중괄호를 사용한 JSX의 자바스크립트.

## 배울 내용들

- 따옴표로 문자열 전달.
- 중괄호를 사용하여 JSX내에 Javascript 변수 참조.
- 중괄호를 사용하여 JSX내에 Javascript 함수 호출.
- 중괄호를 사용하여 JSX내에 Javascript 객체 사용.

## 따옴표로 문자열 전달

문자열 속성을 JSX  전달 하기위한 방식

```javascript
export default function ImgBox() {
  return (<img src="assets/apple.jpg" alt="apple" />)
}
```

동적으로 속성 값을 전달하기 위한 방식

```javascript
export default function ImgBox() {
  const srcUrl = "assets/apple.jpg";
  const description = "apple";
  return (<img src={srcUrl} alt={description} />)
}
```

## 중괄호 사용

JSX에 변수 중괄호 사용.

```javascript
export default function TodoList() {
  const name = 'Gregorio Y. Zara';
  return (
    <h1>{name}</h1>
  )
}
```

JSX에 함수 중괄호 사용.

```javascript
function nameDate(data){
  return data;
}

export default function TodoList() {
  return (<h1>{nameDate('Gregorio Y. Zara')}</h1>)
}
```

**double curlies: JSX의 CSS 객체**
style사용시 camelCase를 사용해야함.

```javascript
export default function TodoList() {
  return (
    <ul style={{
      backgroundColor: 'black',
      color: 'pink'
    }}>
      <li>Improve the videophone</li>
      <li>Prepare aeronautics lectures</li>
      <li>Work on the alcohol-fuelled engine</li>
    </ul>
  );
}
```

JSX 객체 중괄호 사용

```javascript
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

<div style={person.theme}>
  <h1>{person.name}'s Todos</h1>
</div>
```

Previous : [Writing Markup with JSX](./007-%EB%A6%AC%EC%95%A1%ED%8A%B8%20Writing%20markup%20with%20JSX.md)

Next : [Passing Props to a Component](./009-%EB%A6%AC%EC%95%A1%ED%8A%B8%20Passing%20props%20to%20a%20component.md)
