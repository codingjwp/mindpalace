# **Learn React > Quick Start(빠른 시작)**

Welcome to the React documentation! This page will give you an introduction to the 80% of React concepts that you will use on a daily basis.

> React 문서에 오신것을 환영합니다. 이 페이지에서 여러분이 사용하게 될 React 개념의 80%에 대해 소개합니다.

You will learn

- How to create and nest components
- How to add markup and styles
- How to display data
- How to render conditions and lists
- How to respond to events and update the screen
- How to share data between components

> 학습내용  
>
> - 컴포넌트 생성 및 중첩하는 방법  
> - 마크업과 스타일을 추가하는 법  
> - 데이터를 표시하는 법  
> - 조건과 목록을 렌더링하는 법  
> - 이벤트에 응답하고 화명을 업데이트 하는 법  
> - 컴포넌트 간에 데이터를 공유하는 법  

## **Creating and nesting components(컴포넌트 생성 및 중첩하는 방법)**

React apps are made out of components. A component is a piece of the UI (user interface) that has its own logic and appearance. A component can be as small as a button, or as large as an entire page.

> 리액트 앱은 컴포넌트로 만들어집니다.  
> 컴포넌트는 고유한 로직과 모양을 가진 UI(User Interfcae)의 일부 입니다.  
> 컴포넌트는 버튼처럼 작을수도 있고 전체 페이지처럼 클 수도 있습니다.  

React components are JavaScript functions that return markup

> 리액트 컴포넌트는 MarkUp을 반환하는 자바스크립트 함수 입니다.

```javascript
function MyButton() {
  return (
    <button>I'm a button</button>
  )
}
```

Now that you’ve declared `MyButton`, you can nest it into another component.

> 이제 `MyButton`을 선언했으므로 다른 컴포넌트에 중첩할 수 있습니다.

```javascript
export default function MyApp() {
  return (
    <div>
      <h1>Welcom to my app</h1>
      <MyButton />
    </div>
  )
}
```

Notice that <MyButton /> starts with a capital letter. That’s how you know it’s a React component. React component names must always start with a capital letter, while HTML tags must be lowercase.

> `<MyButton />`가 ***대문자***로 시작하는 것을 주목하세요.  
> 이것이 React 컴포넌트라는 것을 알수 있는 방법입니다.  
> React 컴포넌트는 이름은 항상 ***대문자***로 시작해야하고 HTML 태그는 ***소문자***로 시작해야합니다.  

The export default keywords specify the main component in the file. If you’re not familiar with some piece of JavaScript syntax, MDN and javascript.info have great references.

> `export default` 기본 키워드는 주요 구성요소를 지정합니다.  
> 자바스크립트 구문이 익숙하지 않으시면 [MDN](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export), [javascript.info](https://javascript.info/import-export)를 참조하세요.  

## **Writing markup with JSX(JSX로 마크업 작성)**

The markup syntax you’ve seen above is called JSX. It is optional, but most React projects use JSX for its convenience. All of the tools we recommend for local development support JSX out of the box.

> 위에본 마크업 구문을 ***JSX***라고 합니다.  
> 선택 사항이지만 대부분의 React 프로젝트는 편의성을 위해 JSX를 사용합니다.
> 로컬 개발에 권장하는 모드 도구는 ***JSX***를 기본적으로 지원합니다.  

JSX is stricter than HTML. You have to close tags like `<br />`. Your component also can’t return multiple JSX tags. You have to wrap them into a shared parent, like a `<div>...</div>` or an empty `<>...</>` wrapper

> ***JSX***는 HTML보다 더 엄격합니다.  
> `<br />` 같은 태그는 닫아야합니다.  
> 컴포넌트는 또한 여러 개의 JSX 태그를 반환할 수 없습니다.
> `<div>...</div>`또는 빈 `<>...</>` 래퍼와 같이 공유 부모태그로 래핑을 해야합니다..

```javascript
function AboutPage() {
  return (
    <>
      <h1>About</h1>
      <p>Hello there.<br/>How do you do?</p>
    </>
  )
}
```

If you have a lot of HTML to port to JSX, you can use an [online converter](https://transform.tools/html-to-jsx).

> JSX로 변환할 HTML이 많은경우 [온라인 변환기](https://transform.tools/html-to-jsx)를 사용할 수 있습니다.

## **Adding Styles(스타일 추가하기)**

In React, you specify a CSS class with className. It works the same way as the HTML class attribute:

> React에서는 ***className***으로 Css 클래스를 지정합니다.  
> HTML 클래스 속성와 같은 방식으로 작동합니다.

```html
<img className="avatar">
```

Then you write the CSS rules for it in a separate CSS file:

> 그런다음 별도의 CSS파일에 해당하는 CSS 규칙을 작성합닏.

```CSS
.avatar {
  boder-radius: 50%;
}
```

React does not prescribe how you add CSS files. In the simplest case, you’ll add a `<link>` tag to your HTML. If you use a build tool or a framework, consult its documentation to learn how to add a CSS file to your project.

> React는 CSS 파일을 추가하는 방법을 규정하지 않습니다.  
> 가장 간단한 방법은 HTML `<link>` 태그를 추가하면 됩니다.  
> 빌드 도구나 프레임워크를 사용하는 경우 해당 문서를 참조하여 프로젝트에 CSS 파일 추가하는 방식을 알아보세요.

## **Displaying data(데이터 표시)**

JSX lets you put markup into JavaScript. Curly braces let you “escape back” into JavaScript so that you can embed some variable from your code and display it to the user.

> JSX를 사용하면 자바스크립트에 `MarkUp`을 넣을 수 있습니다.  
> `{}(중괄호)`를 사용하면 코드에서 일부 변수를 삽입하여 사용자에게 표시할 수 있도록 자바스크립트로 `이스케이프 백(escape back)` 할 수 있습니다.  

```javascript
return (
  <h1>
    {user.name}
  </h1>
)
```

You can also “escape into JavaScript” from JSX attributes, but you have to use curly braces instead of quotes. For example, className="avatar" passes the "avatar" string as the CSS class, but src={user.imageUrl} reads the JavaScript user.imageUrl variable value, and then passes that value as the src attribute:

> JSX 속성에서도 자바스크립트로 이스케이프를 할 수 있지만 `""(따옴표)`대신 중괄호를 사용해야 합니다.  
> 예를 들어 : ***className="문자열"***, ***src={이스케이프}***

```javascript
return (
  <img
    className="avatar"
    src={user.imageUrl}
  />
);
```

You can put more complex expressions inside the JSX curly braces too, for example, string concatenation

> JSX 중괄호 안에 문자열 연결과 같이 더 복잡한 표현식을 넣을수 있습니다.

```javascript
const user = {
  name: 'Hedy Lamarr',
  imageUrl: 'https://i.imgur.com/yXOvdOSs.jpg',
  imageSize: 90,
};

export default function Profile() {
  return (
    <>
      <h1>{user.name}</h1>
      <img
        className="avatar"
        src={user.imageUrl}
        alt={'Photo of ' + user.name}
        style={{
          width: user.imageSize,
          height: user.imageSize
        }}
      />
    </>
  );
}
```

In the above example, `style={{}}` is not a special syntax, but a regular `{}` object inside the `style={ }` JSX curly braces. You can use the style attribute when your styles depend on JavaScript variables.

> 위의 예에서 `style={{}}`은 특별한 구문이 아니라 `style={ }` JSX 중괄호 안에 있는 일반 `{}` 객체입니다.  
> 스타일이 자바스크립트 변수에 의존할 때 스타일 속성을 사용할 수 있습니다.  

## **Conditional rendering(조건부 렌더링)**

In React, there is no special syntax for writing conditions. Instead, you’ll use the same techniques as you use when writing regular JavaScript code. For example, you can use an if statement to conditionally include JSX:

> React에서는 조건을 작성하기 위한 특별한 문법이 없습니다.  
> 대신 일반 자바스크립트 코드를 작성할 때 사용하는 것과 동일한 기법을 사용하면 됩니다.  
> 예를 들어, if 문을 사용하여 조건부로 JSX를 포함할 수 있습니다:  

```javascript
let content;
if (isLoggedIn) {
  content = <AdminPannel />;
} else {
  content = <LoginPannel />;
}

return (
  <div>
    {content}
  </div>
)
```

If you prefer more compact code, you can use the conditional ? operator. Unlike if, it works inside JSX:

> 보다 간결한 코드를 선호하는 경우 ? 연산자를 사용할 수 있습니다.  
> ?연산자는 JSX내부에서 작동합니다.

```javascript
<div>
  {isLoggedIn ? <AdminPannel /> : <LoginPannel />}
</div>
```

All of these approaches also work for conditionally specifying attributes. If you’re unfamiliar with some of this JavaScript syntax, you can start by always using `if...else`.

> 이 모든 접근 방식은 조건부로 속성을 지정할 때도 작동합니다.  
> 이러한 자바스크립트 구문에 익숙하지 않다면 항상 `if...else`를 사용하는 것으로 시작할 수 있습니다.  

## **Rendering lists(렌더링 목록)**

You will rely on JavaScript features like for loop and the array map() function to render lists of components. For example, let’s say you have an array of products:

> 컴포넌트 목록을 렌더링하기 위해 for루프나 map() 함수 같은 자바스크립트 기능을 사용하게 됩니다.
> 예를 들어

```javascript
const products = [
  { title: 'Cabbage', id: 1 },
  { title: 'Garlic', id: 2 },
  { title: 'Apple', id: 3 },
];
```

Inside your component, use the map() function to transform an array of products into an array of `<li>` items:

> 컴포넌트 내에서 map() 함수를 사용하여 `products`를 `<li>` 항목배열로 변환 합니다.

```javascript
const listItems = products.map(product =>
  <li key={product.id}>
    {product.title}
  </li>
);

return (
  <ul>{listItems}</ul>
);
```

Notice how `<li>`has a key attribute. For each item in a list, you should pass a string or a number that uniquely identifies that item among its siblings. Usually, a key should be coming from your data, such as a database ID. React uses your keys to know what happened if you later insert, delete, or reorder the items.

> `<li>`에 ***key*** 속성이 있는 것을 주목하세요.  
> 목록의 각 항목에 해당 항목을 고유하게 식별하는 문자열 또는 숫자를 전달해야합니다.  
> 일반적으로 키는 ***데이터베이스 ID***와 같은 데이터에서 가져와야합니다.  
> React에서는 나중에 항목을 삽입 삭제 또는 재정렬를 할때 어떤 일이 일어났는지 알기 위해 ***key***를 사용합니다.

## **Responding to events(이벤트 응답하기)**

You can respond to events by declaring event handler functions inside your components:
> 컴포넌트 내부에 이벤트 헨들러 함수를 선언하여 이벤트에 응답할 수 있습니다.

```javascript
function MyButton() {
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

Notice how `onClick={handleClick}` has no parentheses at the end! Do not call the event handler function: you only need to pass it down. React will call your event handler when the user clicks the button.

> `onClick={handleClick}` 끝에 괄호가 없는 것을 주목하세요!  
> 이벤트 핸들러 함수를 호출하지 말고 전달만 하면 됩니다.  
> React는 사용자가 버튼을 클릭할 때 이벤트 핸들러를 호출합니다.

## **Updating the screen(화면 업데이트)**

Often, you’ll want your component to “remember” some information and display it. For example, maybe you want to count the number of times a button is clicked. To do this, add state to your component.

> 컴포넌트가 특정 정보를 ***기억***하여 표시하기를 원하는 경우가 종종 있습니다.  
> 예를 들어 버튼이 클릭된 횟수를 세고 싶을 수 있습니다.  
> 이렇게 하려면 컴포넌트에 상태를 추가하세요.

First, import `useState` from React

> 먼저 React 에서 `useState`를 가져옵니다.

```javascript
import { useState } from React
```

Now you can declare a state variable inside your component

> 컴포넌트 내에서 상태 변수를 선언할 수 있습니다.

```javascript
function MyButton() {
  const [count, setCount] = useState(0);
}
```

You’ll get two things from useState: ***the current state (count)***, and the function that lets you update it ***(setCount)***. You can give them any names, but the convention is to write `[something, setSomething]`.

> useState에서 두 가지를 얻을 수 있습니다. ***현재상태(count)*** 와 이를 업데이트를 할 수 있는 ***함수 (setCount)***  
> 어떤 이름을 지정할수 있지만 `[something, setSomething]`으로 작성하는 것이 일반적입니다.  

The first time the button is displayed, count will be 0 because you passed 0 to `useState()`. When you want to change state, call `setCount()` and pass the new value to it. Clicking this button will increment the counter:

> 버튼이 처음 표시될떄는  `useState()`에 0을 전달하였기 떄문에 0이 표시됩니다.  
> 상태를 변경할려면 `setCount()`를 호출하고 새 값을 전달합니다.  
> 버튼을 클릭하면 카운터가 중가합니다.  

```javascript
function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}
```

React will call your component function again. This time, count will be 1. Then it will be 2. And so on.

> React가 컴포넌트 함수를 다시 호출합니다.  
> 이번에는 1 그 다음에 2가 됩니다.  

If you render the same component multiple times, each will get its own state. Click each button separately:

> 동일한 컴포넌트를 여러 번 렌더링하면 각각 고유한 상태를 갖게 됩니다.
> 각 버튼을 개별적으로 클릭하세요.

```javascript
import { useState } from 'react';

export default function MyApp() {
  return (
    <div>
      <h1>Counters that update separately</h1>
      <MyButton />
      <MyButton />
    </div>
  );
}

function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}
```

Notice how each button “remembers” its own count state and doesn’t affect other buttons.

> 각 버튼이 자체 카운트 상태를 ***기억***하고 다른 버튼에 영향을 주지 않는 방식을 주목하세요.

## **Using Hooks(훅 사용)**

Functions starting with use are called Hooks. useState is a built-in Hook provided by React. You can find other built-in Hooks in the [API reference.](https://react.dev/reference/react) You can also write your own Hooks by combining the existing ones.

> 사용으로 시작하는 함수를 `Hook`이라고 합니다.  
> `useState`는 React에서 제공하는 ***내장 Hook*** 입니다.  
> 다른 내장 Hook은 [API 참조](https://react.dev/reference/react)에서 찾을 수 있습니다.  
> 기본의 Hook을 조합하여 자신만의 Hook을 작성할 수 있습니다.

Hooks are more restrictive than other functions. You can only call Hooks at the top of your components (or other Hooks). If you want to use useState in a condition or a loop, extract a new component and put it there.

> Hook은 다름 함수보다 더 제한적입니다.  
> 컴포넌트(다른 Hook)의 맨 위에 있는 Hook만 호출할 수 있습니다.  
> 조건이나 루프에서 useState를 사용하려면 새 컴포넌트를 추출하여 거기에 넣으세요.

## **Sharing data between components(구성 요소 간 데이터 공유)**

In the previous example, each `MyButton` had its own independent count, and when each button was clicked, only the count for the button clicked changed:

> 이전 예제에서 MyButton에 독립적인 count가 있었고 각 button을 클릭하면 클릭한 버튼 카운터만 변경되었습니다.

However, often you’ll need components to share data and always update together.

> 하지만 데이터를 공유하고 항상 함꼐 업데이트하는 컴포넌트가 필요한 경우가 많습니다.

To make both `MyButton` components display the same count and update together, you need to move the state from the individual buttons “upwards” to the closest component containing all of them.

> 두 `MyButton` 컴포넌트가 동일한 개수를 표시하고 함꼐 업데이트되도록 하려면 개별 버튼에서 모든 버튼이 포함된 가장 가까운 컴포넌트로 상태를 ***upwards***으로 이동해야합니다.

Now when you click either button, the count in `MyApp` will change, which will change both of the counts in `MyButton`. Here’s how you can express this in code.

> 이제 두 버튼 중 하나를 클릭하면 `MyApp`의 ***count***가 변경되고 이에 따라 `MyButton`의 카운트도 모두 변경됩니다.  
> 이를 코드로 표현하는 방법은 다음과 같습니다.

First, move the state up from `MyButton` into `MyApp`

> 먼저 `MyButton` 상태를 `MyApp`으로 옮깁니다.

```javascript
export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update separately</h1>
      <MyButton />
      <MyButton />
    </div>
  );
}

function MyButton() {
  // ... we're moving code from here ...
}
```

Then, pass the state down from `MyApp` to each `MyButton`, together with the shared click handler. You can pass information to `MyButton` using the JSX curly braces, just like you previously did with built-in tags like `<img>`:

> 그런 다음 클릭 핸들러와 함께 `MyApp`에서 각각의 `MyButton`으로 상태를 전달합니다.  
> 이전 `<img>`와 같은 기본 태그를 사용했던 것 처럼 JSX 중괄호를 사용하여 `MyButton`에 정보를 전달 할 수 있습니다.

```javascript
export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update together</h1>
      <MyButton count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
    </div>
  );
}
```

The information you pass down like this is called ***props***. Now the `MyApp` component contains the `count` state and the `handleClick` event handler, and passes both of them down as props to each of the buttons.

> 이렇게 전달한 정보를 ***props(프로퍼티)*** 라고 합니다.  
> `MyApp` 컴포넌트는 `count` 상태와 `handleClick` 이벤트 핸들러를 포함하며 이 두가지를 각 버튼에 props로 전달합니다.

Finally, change `MyButton` to read the props you have passed from its parent component:

> 마지막으로 부모 컴포넌트에서 전달한 프로퍼티를 읽도록 `MyButton` 컴포넌트를 변경합니다.

```javascript
function MyButton({ count, onClick }) {
  return (
    <button onClick={onClick}>
      Clicked {count} times
    </button>
  );
}
```

When you click the button, the `onClick` handler fires. Each button’s `onClick` prop was set to the `handleClick` function inside `MyApp`, so the code inside of it runs. That code calls `setCount(count + 1)`, incrementing the count state variable. The new count value is passed as a prop to each button, so they all show the new value. This is called “lifting state up”. By moving state up, you’ve shared it between components.

> 버튼을 클릭하면 `onClick` 핸들러가 실행됩니다.  
> 각 버튼의 `onClick` props는 `MyApp`내부의 `handleClick` 함수로 설정되어있으므로 그 안에 있는 코드를 실행됩니다.  
> `handleClick`는 `setCount(count + 1)`를 호출하여 카운트 상태 변수를 증가시킵니다.  
> 새로운 카운트 값은 각 버튼에 프로퍼티로 전달되므로 모든 버튼에 새로운 값이 표시됩니다.  
> 이를 ***lifting state up(상태 올리기)*** 라고 합니다.  
> 상태를 위로 이동하면 컴포넌트 간에 상태를 공유하게 됩니다.
