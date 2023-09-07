# **Learn React > Quick Start(빠른 시작)**

## **Tutorial: Tic-Tac-Toe(튜토리얼: 틱택토)**

You will build a small tic-tac-toe game during this tutorial. This tutorial does not assume any existing React knowledge. The techniques you’ll learn in the tutorial are fundamental to building any React app, and fully understanding it will give you a deep understanding of React.

> 이 튜토리얼에서는 작은 틱택토 게임을 만들 것입니다.  
> 이 튜토리얼은 기존의 React 지식을 전제로 하지 않습니다.  
> 이 튜토리얼에서 배우게 될 기술은 모든 React 앱을 빌드하는 데 기본이 되는 기술이며, 이를 완전히 이해하면 React에 대해 깊이 이해할 수 있습니다.  

### **Note**

This tutorial is designed for people who prefer to learn by doing and want to quickly try making something tangible. If you prefer learning each concept step by step, start with [Describing the UI]().

> 이 튜토리얼은 직접 해보면서 배우는 것을 선호하고 유형의 무언가를 빠르게 만들어보고 싶은 분들을 위해 설계되었습니다.  
> 각 개념을 단계별로 학습하는 것을 선호한다면 [UI 설명]()부터 시작하세요.  

The tutorial is divided into several sections:

- [Setup for the tutorial](https://react.dev/learn/tutorial-tic-tac-toe#setup-for-the-tutorial) will give you a starting point to follow the tutorial.
- [Overview](https://react.dev/learn/tutorial-tic-tac-toe#overview) will teach you the fundamentals of React: components, props, and state.
- [Completing the game](https://react.dev/learn/tutorial-tic-tac-toe#completing-the-game) will teach you the most common techniques in React development.
- [Adding time travel](https://react.dev/learn/tutorial-tic-tac-toe#adding-time-travel) will give you a deeper insight into the unique strengths of React.

> 튜토리얼은 여러 섹션으로 나뉩니다  
>  
> - [튜토리얼 설정](https://react.dev/learn/tutorial-tic-tac-toe#setup-for-the-tutorial)에서는 튜토리얼을 따라가기 위한 시작점을 제공합니다.
> - [개요](https://react.dev/learn/tutorial-tic-tac-toe#overview) 에서는 컴포넌트, 프로퍼티, state와 같은 React의 기본 사항을 알려줍니다.
> - [게임을 완료](https://react.dev/learn/tutorial-tic-tac-toe#completing-the-game)하면 React 개발에서 가장 일반적인 기술을 배울 수 있습니다.
> - [시간 여행](https://react.dev/learn/tutorial-tic-tac-toe#adding-time-travel)을 추가하면 React의 고유한 강점에 대해 더 깊은 통찰력을 얻을 수 있습니다.

## **What are you building?(무엇을 만들고 계신가요.)**

In this tutorial, you’ll build an interactive tic-tac-toe game with React.
You can see what it will look like when you’re finished here:

> 이 튜토리얼에서는 React로 대화형 틱택토 게임을 빌드해 보겠습니다.
> 완료되면 어떤 모습일지 여기에서 확인할 수 있습니다

### **Note**

You can also follow this tutorial using your local development environment. To do this, you need to:

1. Install [Node.js](https://nodejs.org/en/)
2. In the CodeSandbox tab you opened earlier, press the top-left corner button to open the menu, and then choose ***File > Export*** to ZIP in that menu to download an archive of the files locally
3. Unzip the archive, then open a terminal and `cd` to the directory you unzipped
4. Install the dependencies with `npm install`
5. Run `npm start` to start a local server and follow the prompts to view the code running in a browser

If you get stuck, don’t let this stop you! Follow along online instead and try a local setup again later.

> 로컬 개발 환경을 사용하여 이 튜토리얼을 따라할 수도 있습니다. 이렇게 하려면 다음을 수행해야 합니다
>
> 1. [Node.js](https://nodejs.org/en/) 설치  
> 2. 앞서 연 CodeSandbox 탭에서 왼쪽 상단 모서리 버튼을 눌러 메뉴를 연 다음 해당 메뉴에서 ***File > export*** ZIP으로 내보내기를 선택하여 로컬로 파일 아카이브를 다운로드합니다.  
> 3. 아카이브의 압축을 푼 다음 터미널을 열고 압축을 푼 디렉터리에 `cd`를 넣습니다.
> 4. `npm install`으로 종속 요소를 설치합니다.
> 5. `npm start`을 실행하여 로컬 서버를 시작하고 프롬프트에 따라 브라우저에서 실행 중인 코드를 확인합니다.  
>
> 막히더라도 포기하지 마세요! 대신 온라인에서 따라 하시고 나중에 로컬 설정을 다시 시도하세요.

## **Overview(개요)**

Now that you’re set up, let’s get an overview of React!

> 이제 설정이 완료되었으니 React에 대한 개요를 살펴봅시다!

### **Inspecting the starter code(스타터 코드 검사)**

1. The Files section with a list of files like `App.js`, `index.js`, `styles.css` and a folder called `public`
2. The code editor where you’ll see the source code of your selected file
3. The browser section where you’ll see how the code you’ve written will be displayed

> CodeSandBox로 작성한 코드가 존재
>
> 1. `App.js`, `index.js`, `styles.css`와 같은 파일 목록이 있는 파일 섹션과 `public`이라는 폴더가 있습니다.
> 2. 선택한 파일의 소스 코드를 볼 수 있는 코드 편집기
> 3. 작성한 코드가 어떻게 표시되는지 확인할 수 있는 브라우저 섹션입니다.

The `App.js` file should be selected in the Files section. The contents of that file in the code editor should be:

> 파일 섹션에서 `App.js` 파일을 선택해야 합니다. 코드 편집기에서 해당 파일의 내용이 표시되어야 합니다:

```javascript
export default function Square() {
  return <button className="square">X</button>;
}
```

The browser section should be displaying a square with a X in it like this:
> 브라우저 섹션에 다음과 같이 X가 표시된 사각형이 표시되어야 합니다:

![x-filled square](https://react.dev/images/tutorial/x-filled-square.png)

Now let’s have a look at the files in the starter code.
> 이제 스타터 코드의 파일을 살펴보겠습니다.

#### **`App.js`**

The code in `App.js` creates a component. In React, a component is a piece of reusable code that represents a part of a user interface. Components are used to render, manage, and update the UI elements in your application. Let’s look at the component line by line to see what’s going on:

> App.js의 코드는 컴포넌트를 생성합니다.  
> React에서 ***컴포넌트***는 사용자 인터페이스의 일부를 나타내는 재사용 가능한 코드 조각입니다.
> 컴포넌트는 애플리케이션의 UI 요소를 ***렌더링***, ***관리***, ***업데이트***하는 데 사용됩니다.  
> 컴포넌트를 한 줄씩 살펴보면서 무슨 일이 일어나는지 살펴보겠습니다:

```javascript
//export default function Square() {
  return <button className="square">X</button>;
}
```

The first line defines a function called Square. The export JavaScript keyword makes this function accessible outside of this file. The default keyword tells other files using your code that it’s the main function in your file.

> 첫 번째 줄은 ***Square***라는 함수를 정의합니다.  
> ***JavaScript*** `export default` 키워드를 사용하면 이 함수를 이 파일 외부에서 액세스할 수 있습니다.  
> 기본 키워드는 코드를 사용하는 다른 파일에 이 함수가 기본 함수임을 알려줍니다.

```javascript
export default function Square() {
//  return <button className="square">X</button>;
}
```

The second line returns a button. The return JavaScript keyword means whatever comes after is returned as a value to the caller of the function. `<button>` is a ***JSX element***. A JSX element is a combination of JavaScript code and HTML tags that describes what you’d like to display. `className="square"` is a button property or prop that tells ***CSS*** how to style the button. ***X*** is the text displayed inside of the button and `</button>` closes the JSX element to indicate that any following content shouldn’t be placed inside the button.

> 두번째 줄은 `button`을 반환합니다.  
> 반환 자바스크립트 키워드는 뒤에 오는 모든 것이 함수 호출자에게 값으로 반환된다는 의미입니다.  
> `<button>`은 JSX 요소입니다.  
> JSX 요소는 표시할 내용을 설명하는 JavaScript 코드와 HTML 태그의 조합입니다.  
> `className="square"`는 버튼 속성 또는 프로퍼티로, 버튼 스타일 지정 방법을 CSS에 알려줍니다.  
> ***X***는 버튼 내부에 표시되는 텍스트이며 `</button>`은 JSX 요소를 닫아 버튼 내부에 다음 콘텐츠를 배치해서는 안 됨을 나타냅니다.

#### **`styles.css`**

Click on the file labeled `styles.css` in the Files section of CodeSandbox. This file defines the styles for your React app. The first two CSS selectors ***( * and body )*** define the style of large parts of your app while the `.square` selector defines the style of any component where the `className` property is set to `square.` In your code, that would match the button from your Square component in the `App.js` file.

> 이 파일은 React 앱의 스타일을 정의합니다.  
> 처음 두 개의 CSS 선택기***( * 및 body)***는 앱의 많은 부분의 스타일을 정의하는 반면 `.square` 선택기는 `className` 속성이 정사각형으로 설정된 모든 구성 요소의 스타일을 정의합니다.  

#### **`index.js`**

Click on the file labeled `index.js`in the Files section of CodeSandbox. You won’t be editing this file during the tutorial but it is the bridge between the component you created in the `App.js` file and the web browser.

> 튜토리얼 중에는 이 파일을 편집하지 않지만 `App.js` 파일에서 만든 컴포넌트와 웹 브라우저 사이의 다리 역할을 합니다.

```javascript
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';
```

Lines 1-5 brings all the necessary pieces together:

- React
- React’s library to talk to web browsers (React DOM)
- the styles for your components
- the component you created in `App.js`.

The remainder of the file brings all the pieces together and injects the final product into `index.html` in the `public` folder.

> 1~5행은 필요한 모든 조각을 한데 모았습니다:
>
> - React
> - 웹 브라우저와 대화하는 React의 라이브러리(React DOM)
> - 컴포넌트의 스타일
> - `App.js`에서 생성한 컴포넌트.
>
> 나머지 파일은 모든 조각을 한데 모아 최종 결과물을 `public` 폴더의 `index.html`에 삽입합니다.

### **Building the board(보드 만들기)**

Let’s get back to App.js. This is where you’ll spend the rest of the tutorial.

Currently the board is only a single square, but you need nine! If you just try and copy paste your square to make two squares like this

> App.js로 가보겠습니다.  
> 이 튜토리얼의 나머지 부분을 여기서 보내게 될 것입니다.  
> 현재 보드에는 사각형이 하나뿐이지만 9개가 필요합니다!
> 정사각형을 복사 붙여넣기하면 이렇게 두 개의 정사각형을 만들 수 있습니다

```javascript
export default function Square() {
 // return <button className="square">X</button><button className="square">X</button>;
}
```

Error :  
`/src/App.js: Adjacent JSX elements must be wrapped in an enclosing tag. Did you want a JSX fragment <>...</>?`  

> /src/App.js: 인접한 JSX 요소는 둘러싸는 태그로 묶어야 합니다. JSX 조각 <>...</>을 원하시나요?

React components need to return a single JSX element and not multiple adjacent JSX elements like two buttons. To fix this you can use fragments ***(<> and </>)*** to wrap multiple adjacent JSX elements like this:

> React 컴포넌트는 버튼 두 개처럼 인접한 여러 개의 JSX 엘리먼트가 아닌 단일 JSX 엘리먼트를 반환해야 합니다.  
> 이 문제를 해결하려면 ***(<> 및 </>)***을 사용하여 다음과 같이 여러 개의 인접한 JSX 요소를 래핑할 수 있습니다:

```javascript
export default function Square() {
  return (
/*    
    <>
      <button className="square">X</button>
      <button className="square">X</button>
    </>
*/
  );
}
```

Now you should see

> 보여지는 결과

![Alt text](https://react.dev/images/tutorial/two-x-filled-squares.png)

Great! Now you just need to copy-paste a few times to add nine squares and…

> 훌륭합니다! 이제 복사-붙여넣기를 몇 번만 하면 사각형 9개를 추가할 수 있습니다.

![nine x-filled squares in a line](https://react.dev/images/tutorial/nine-x-filled-squares.png)

Oh no! The squares are all in a single line, not in a grid like you need for our board. To fix this you’ll need to group your squares into rows with `divs` and add some CSS classes. While you’re at it, you’ll give each square a number to make sure you know where each square is displayed.

> 이런! 사각형이 모두 한 줄로 되어 있고 보드에 필요한 격자가 아닙니다. 이 문제를 해결하려면 `div`를
> 사용하여 사각형을 행으로 그룹화하고 몇 가지 `CSS` 클래스를 추가해야 합니다.  
> 이 과정에서 각 사각형에 번호를 부여하여 각 사각형이 표시되는 위치를 알 수 있도록 합니다.

```javascript
export default function Square() {
  return (
/*
    <>
      <div className="board-row">
        <button className="square">1</button>
        <button className="square">2</button>
        <button className="square">3</button>
      </div>
      <div className="board-row">
        <button className="square">4</button>
        <button className="square">5</button>
        <button className="square">6</button>
      </div>
      <div className="board-row">
        <button className="square">7</button>
        <button className="square">8</button>
        <button className="square">9</button>
      </div>
    </>
*/
  );
}
```

The CSS defined in `styles.css` styles the divs with the `className` of `board-row`. Now that you’ve grouped your components into rows with the styled `div`s you have your tic-tac-toe board:

> `styles.css`에 정의된 CSS는 `board-row`의 `className`으로 `div`의 스타일을 지정합니다.  
> 이제 스타일이 지정된 `div`를 사용하여 컴포넌트를 행으로 그룹화했으므로 틱택토 보드가 완성되었습니다  

![tic-tac-toe board filled with numbers 1 through 9](https://react.dev/images/tutorial/number-filled-board.png)

But you now have a problem. Your component named `Square`, really isn’t a square anymore. Let’s fix that by changing the name to `Board`:

> 하지만 이제 문제가 생겼습니다.  
> `Square`이라는 컴포넌트가 더 이상 정사각형이 아닙니다.  
> 이름을 `Board로` 변경하여 문제를 해결해 봅시다

```javascript
export default function Board() {
  //...
}
```

At this point your code should look something like this
> 이 시점에서 코드는 다음과 같이 표시되어야 합니다

```javascript
export default function Board() {
  return (
    <>
      <div className="board-row">
        <button className="square">1</button>
        <button className="square">2</button>
        <button className="square">3</button>
      </div>
      <div className="board-row">
        <button className="square">4</button>
        <button className="square">5</button>
        <button className="square">6</button>
      </div>
      <div className="board-row">
        <button className="square">7</button>
        <button className="square">8</button>
        <button className="square">9</button>
      </div>
    </>
  );
}
```

### **Note**

Psssst… That’s a lot to type! It’s okay to copy and paste code from this page. However, if you’re up for a little challenge, we recommend only copying code that you’ve manually typed at least once yourself.

> 잠깐만요... 입력할 내용이 많네요! 이 페이지에서 코드를 복사하여 붙여넣어도 괜찮습니다. 하지만 약간의 도전을 원한다면 직접 한 번 이상 입력한 코드만 복사하는 것을 권장합니다.

### **Passing data through props(프로퍼티를 이용한 데이터 전달)**

Next, you’ll want to change the value of a square from empty to “X” when the user clicks on the square. With how you’ve built the board so far you would need to copy-paste the code that updates the square nine times (once for each square you have)! Instead of copy-pasting, React’s component architecture allows you to create a reusable component to avoid messy, duplicated code.

First, you are going to copy the line defining your first square (`<button className="square">1</button>`) from your `Board` component into a new `Square` component:

```javascript
function Square() {
  return <button className="square">1</button>;
}
```

```javascript
export default function Board() {
  // ...
}
```

Then you’ll update the Board component to render that `Square` component using JSX syntax:

```javascript
// ...
export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
    </>
  );
}
```

Note how unlike the browser `div`s, your own components `Board` and `Square` must start with a capital letter.

Let’s take a look:

![one-filled board](https://react.dev/images/tutorial/board-filled-with-ones.png)

Oh no! You lost the numbered squares you had before. Now each square says “1”. To fix this, you will use props to pass the value each square should have from the parent component (`Board`) to its child (`Square`).

Update the `Square` component to read the `value` prop that you’ll pass from the `Board`:

```javascript
function Square({ value }) {
  return <button className="square">1</button>;
}
```

function `Square({ value })` indicates the `Square` component can be passed a prop called value.

Now you want to display that `value` instead of `1` inside every square. Try doing it like this:

```javascript
function Square({ value }) {
  return <button className="square">value</button>;
}
```

Oops, this is not what you wanted:

![value-filled board](https://react.dev/images/tutorial/board-filled-with-value.png)

You wanted to render the JavaScript variable called `value` from your component, not the word “value”. To “escape into JavaScript” from JSX, you need curly braces. Add curly braces around `value` in JSX like so:

```javascript
function Square({ value }) {
  return <button className="square">{value}</button>;
}
```

For now, you should see an empty board:

![empty board](https://react.dev/images/tutorial/empty-board.png)

This is because the `Board` component hasn’t passed the `value` prop to each `Square` component it renders yet. To fix it you’ll add the `value` prop to each `Square` component rendered by the Board component:

```javascript
export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square value="1" />
        <Square value="2" />
        <Square value="3" />
      </div>
      <div className="board-row">
        <Square value="4" />
        <Square value="5" />
        <Square value="6" />
      </div>
      <div className="board-row">
        <Square value="7" />
        <Square value="8" />
        <Square value="9" />
      </div>
    </>
  );
}
```

Now you should see a grid of numbers again:

![tic-tac-toe board filled with numbers 1 through 9](https://react.dev/images/tutorial/number-filled-board.png)

### **Making an interactive component**

Let’s fill the `Square` component with an X when you click it. Declare a function called `handleClick` inside of the `Square`. Then, add `onClick` to the props of the button JSX element returned from the `Square`:

```javascript
function Square({ value }) {
  function handleClick() {
    console.log('clicked!');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}
```

If you click on a square now, you should see a log saying `"clicked!"` in the Console tab at the bottom of the Browser section in CodeSandbox. Clicking the square more than once will log `"clicked!"` again. Repeated console logs with the same message will not create more lines in the console. Instead, you will see an incrementing counter next to your first `"clicked!"` log.

### **Note**

If you are following this tutorial using your local development environment, you need to open your browser’s Console. For example, if you use the Chrome browser, you can view the Console with the keyboard shortcut `Shift + Ctrl + J`(on Windows/Linux) or `Option + ⌘ + J` (on macOS).

As a next step, you want the `Square` component to “remember” that it got clicked, and fill it with an “X” mark. To “remember” things, components use state.

React provides a special function called `useState` that you can call from your component to let it “remember” things. Let’s store the current value of the `Square` in state, and change it when the `Square` is clicked.

Import `useState` at the top of the file. Remove the `value` prop from the `Square` component. Instead, add a new line at the start of the `Square` that calls `useState`. Have it return a state variable called `value`:

```javascript
import { useState } from 'react';

function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    //...
```

`value` stores the value and `setValue` is a function that can be used to change the value. The `null` passed to `useState` is used as the initial value for this state variable, so `value` here starts off equal to `null`.

Since the `Square` component no longer accepts props anymore, you’ll remove the `value` prop from all nine of the `Square` components created by the `Board` component:

```javascript
// ...
export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
    </>
  );
}
```

Now you’ll change `Square` to display an “X” when clicked. Replace the console.log("clicked!"); event handler with `setValue('X');`. Now your `Square` component looks like this:

```javascript
function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    setValue('X');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}
```

By calling this `set` function from an `onClick` handler, you’re telling React to re-render that `Square` whenever its `<button>` is clicked. After the update, the `Square`’s `value` will be `'X'`, so you’ll see the “X” on the game board. Click on any Square, and “X” should show up:

![adding xes to board](https://react.dev/images/tutorial/tictac-adding-x-s.gif)

Each Square has its own state: the `value` stored in each Square is completely independent of the others. When you call a `set` function in a component, React automatically updates the child components inside too.

After you’ve made the above changes, your code will look like this:

### **React Developer Tools**

React DevTools let you check the props and the state of your React components. You can find the React DevTools tab at the bottom of the browser section in CodeSandbox:

![React DevTools in CodeSandbox](https://react.dev/images/tutorial/codesandbox-devtools.png)

To inspect a particular component on the screen, use the button in the top left corner of React DevTools:

![Selecting components on the page with React DevTools](https://react.dev/images/tutorial/devtools-select.gif)

### **Note**

For local development, React DevTools is available as a [Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en), [Firefox](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/), and [Edge](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil) browser extension. Install it, and the Components tab will appear in your browser Developer Tools for sites using React.

## **Completing the game(게임 완료)**

By this point, you have all the basic building blocks for your tic-tac-toe game. To have a complete game, you now need to alternate placing “X”s and “O”s on the board, and you need a way to determine a winner.

### **Lifting state up**

Currently, each `Square` component maintains a part of the game’s state. To check for a winner in a tic-tac-toe game, the `Board` would need to somehow know the state of each of the 9 `Square` components.

How would you approach that? At first, you might guess that the `Board` needs to “ask” each `Square` for that `Square`’s state. Although this approach is technically possible in React, we discourage it because the code becomes difficult to understand, susceptible to bugs, and hard to refactor. Instead, the best approach is to store the game’s state in the parent `Board` component instead of in each `Square`. The `Board` component can tell each `Square` what to display by passing a prop, like you did when you passed a number to each Square.

***To collect data from multiple children, or to have two child components communicate with each other, declare the shared state in their parent component instead. The parent component can pass that state back down to the children via props. This keeps the child components in sync with each other and with their parent.***

Lifting state into a parent component is common when React components are refactored.

Let’s take this opportunity to try it out. Edit the `Board` component so that it declares a state variable named `squares` that defaults to an array of 9 nulls corresponding to the 9 squares:

```javascript
// ...
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    // ...
  );
}
```

`Array(9).fill(null)` creates an array with nine elements and sets each of them to `null`. The `useState()`call around it declares a `squares` state variable that’s initially set to that array. Each entry in the array corresponds to the value of a square. When you fill the board in later, the `squares` array will look like this:

```javascript
['O', null, 'X', 'X', 'X', 'O', 'O', null, null]
```

Now your `Board` component needs to pass the `value` prop down to each `Square` that it renders:

```javascript
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} />
        <Square value={squares[1]} />
        <Square value={squares[2]} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} />
        <Square value={squares[4]} />
        <Square value={squares[5]} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} />
        <Square value={squares[7]} />
        <Square value={squares[8]} />
      </div>
    </>
  );
}
```

Next, you’ll edit the `Square` component to receive the `value` prop from the Board component. This will require removing the Square component’s own stateful tracking of `value` and the button’s `onClick` prop:

```javascript
function Square({value}) {
  return <button className="square">{value}</button>;
}
```

At this point you should see an empty tic-tac-toe board:

![empty board](https://react.dev/images/tutorial/empty-board.png)

Each Square will now receive a `value` prop that will either be `'X'`, `'O'`, or `null` for empty squares.

Next, you need to change what happens when a `Square` is clicked. The `Board` component now maintains which squares are filled. You’ll need to create a way for the `Square` to update the `Board`’s state. Since state is private to a component that defines it, you cannot update the `Board`’s state directly from `Square`.

Instead, you’ll pass down a function from the `Board` component to the `Square` component, and you’ll have `Square` call that function when a square is clicked. You’ll start with the function that the `Square` component will call when it is clicked. You’ll call that function `onSquareClick`:

```javascript
function Square({ value }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}
```

Next, you’ll add the onSquareClick function to the Square component’s props:

```javascript
function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}
```

Now you’ll connect the `onSquareClick` prop to a function in the `Board` component that you’ll name `handleClick`. To connect `onSquareClick` to `handleClick` you’ll pass a function to the `onSquareClick` prop of the first `Square` component:

```javascript
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={handleClick} />
        //...
  );
}
```

Lastly, you will define the `handleClick` function inside the Board component to update the `squares` array holding your board’s state:

```javascript
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick() {
    const nextSquares = squares.slice();
    nextSquares[0] = "X";
    setSquares(nextSquares);
  }

  return (
    // ...
  )
}
```

The `handleClick` function creates a copy of the `squares` array (`nextSquares`) with the JavaScript `slice()` Array method. Then, `handleClick` updates the `nextSquares` array to add `X` to the first (`[0]` index) square.

Calling the `setSquares` function lets React know the state of the component has changed. This will trigger a re-render of the components that use the `squares` state (`Board`) as well as its child components (the `Square` components that make up the board).

### **Note**

JavaScript supports [closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) which means an inner function (e.g. `handleClick`) has access to variables and functions defined in a outer function (e.g. `Board`). The `handleClick` function can read the `squares` state and call the `setSquares` method because they are both defined inside of the `Board` function.

Now you can add X’s to the board…  but only to the upper left square. Your `handleClick` function is hardcoded to update the index for the upper left square (`0`). Let’s update `handleClick` to be able to update any square. Add an argument `i` to the `handleClick` function that takes the index of the square to update:

```javascript
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    const nextSquares = squares.slice();
    nextSquares[i] = "X";
    setSquares(nextSquares);
  }

  return (
    // ...
  )
}
```

Next, you will need to pass that `i` to `handleClick`. You could try to set the `onSquareClick` prop of square to be `handleClick(0)` directly in the JSX like this, but it won’t work

```javascript
<Square value={squares[0]} onSquareClick={handleClick(0)} />
```

Here is why this doesn’t work. `The handleClick(0) `call will be a part of rendering the board component. Because `handleClick(0)` alters the state of the board component by calling `setSquares`, your entire board component will be re-rendered again. But this runs `handleClick(0)` again, leading to an infinite loop:

Why didn’t this problem happen earlier?

When you were passing `onSquareClick={handleClick}`, you were passing the `handleClick` function down as a prop. You were not calling it! But now you are calling that function right away—notice the parentheses in `handleClick(0)`—and that’s why it runs too early. You don’t want to call `handleClick` until the user clicks!

You could fix this by creating a function like `handleFirstSquareClick` that calls `handleClick(0)`, a function like `handleSecondSquareClick` that calls `handleClick(1)`, and so on. You would pass (rather than call) these functions down as props like `onSquareClick={handleFirstSquareClick}`. This would solve the infinite loop.

However, defining nine different functions and giving each of them a name is too verbose. Instead, let’s do this:

```javascript
export default function Board() {
  // ...
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        // ...
  );
}
```

Notice the new `() =>` syntax. Here, `() => handleClick(0)` is an arrow function, which is a shorter way to define functions. When the square is clicked, the code after the `=>` “arrow” will run, calling `handleClick(0)`.

Now you need to update the other eight squares to call `handleClick` from the arrow functions you pass. Make sure that the argument for each call of the `handleClick` corresponds to the index of the correct square:

```javascript
export default function Board() {
  // ...
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
};
```

Now you can again add X’s to any square on the board by clicking on them:

But this time all the state management is handled by the `Board` component!

This is what your code should look like:

Now that your state handling is in the `Board` component, the parent `Board` component passes props to the child `Square` components so that they can be displayed correctly. When clicking on a `Square`, the child `Square` component now asks the parent `Board` component to update the state of the board. When the `Board`’s state changes, both the `Board` component and every child `Square` re-renders automatically. Keeping the state of all squares in the `Board` component will allow it to determine the winner in the future.

Let’s recap what happens when a user clicks the top left square on your board to add an `X` to it:

1. Clicking on the upper left square runs the function that the `button` received as its `onClick` prop from the `Square`. The `Square` component received that function as its `onSquareClick` prop from the `Board`. The `Board` component defined that function directly in the JSX. It calls `handleClick` with an argument of 0.
2. `handleClick` uses the argument (0) to update the first element of the `squares` array from `null` to `X`.
3. The `squares` state of the `Board` component was updated, so the `Board` and all of its children re-render. This causes the `value` prop of the `Square` component with index `0` to change from `null` to `X`.

In the end the user sees that the upper left square has changed from empty to having a `X` after clicking it.

### **Note**

The DOM `<button>` element’s `onClick` attribute has a special meaning to React because it is a built-in component. For custom components like Square, the naming is up to you. You could give any name to the `Square`’s `onSquareClick` prop or `Board`’s `handleClick` function, and the code would work the same. In React, it’s conventional to use `onSomething` names for props which represent events and `handleSomething` for the function definitions which handle those events.


## **Adding time travel(시간 여행 추가)**
