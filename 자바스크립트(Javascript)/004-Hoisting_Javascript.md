# Hoisting(호이스팅)

## Hoisting(호이스팅) 이란?

모든 함수, 클래스, 변수 또는 import 선언을 코드의 최상단으로 이동시키는 프로세스를 말합니다.

### 간단한 변수 Hoisting

```javascript
var name;
console.log(name);
name="테스트";
console.log(name);
// 결과 값
// undefined
// 테스트;
```

### `let`, `const` 둘다 호이스팅이 됩니다.

`let`, `const`으로 **Hoisting**이 되었는지 테스트 코드로  
첫 번째 코드 에러 문구 : `Cannot access 'test' before initializtion`  
두 번째 코드 에러 문구 : `test is not defined`  
첫 번째는 값이 초기화 되기 전에 막아주는 에러고 두 번째는 존재하지 않는 에러를 나타냅니다.  
즉 첫 번째 코드는 존재는 하지만 **Hoisting**을 막아준 것으로 보시면 되겠습니다.

```javascript
console.log(test);
let test = '테스트'
```

```javascript
console.log(test);
// let test = '테스트'
```

### 함수 Hoisting

첫 번째 코드는 함수가 선언되기 전에 호출되었는데 오류 없이 실행됩니다.

```javascript
console.log(square(5)); // 25

function square(n) {
  return n * n;
}
```

이는 자바스크립트 인터프리터가 전체 함수 선언을 현재 범위 맨 위로 올려 두 번쨰 코드와 같이 실행되기 때문입니다.

```javascript
function square(n) {
  return n * n;
}
console.log(square(5)); // 25
```

함수 Hoisting은 세 번째 코드와 같이 함수 표현식에서는 작동을 하지 않습니다
```javascript
console.log(square(5)); // ReferenceError: Cannot access 'square' before initialization
const square = function (n) {
  return n * n;
};
```

### import Hoisting

import는 아래의 코드와 같이 선언되기 전 코드가 작동하기 때문에 Hoisting이 됩니다. 

```javascript
// …
const myCanvas = new Canvas("myCanvas", document.body, 480, 320);
myCanvas.create();
import { Canvas } from "./modules/canvas.js";
myCanvas.createReportList();
// …
```

`Class` 같은 경우 `let`, `const`와 같이 선언을 먼저 하지 않으면 사용을 못합니다.

## 궁금한 점

그럼 여러가지 Hoisting이 있는데 순서대로 최상단으로 가는 걸까??  
**변수 선언은 함수의 선언보다 처리됩니다.**  

### 추가적 공부
- 컴파일 시간에는 할당이 아닌 선언만 메모리에 끌어 올려진다는 점을 기억하는 것이 중요하다.  
- 선언만 호이스팅 됩니다.  
- 변수가 선언되지 않은 상태에서 초기화가 이루어질 경우, 그 변수는 그것이 초기화된 함수의 범위(scope)가 아닌 전역 범위(global scope)로 호이스팅된다는 점입니다

  ```javascript
  function doSomething() {
  doing = "something";
  }

  console.log(doing); // ReferenceError: doing is not defined
  doSomething();
  console.log(doing); // something
  ```

> 출처 : [What is hoisting and how it works in Javascript](https://thejs.dev/jmitchell/what-is-hoisting-and-how-it-works-in-javascript-e1e)
