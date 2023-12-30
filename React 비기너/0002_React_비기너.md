# React 비기너 Part 2

## setInterval은 자바스크립트가 아니다?

`setInetrval()`은 **WindowOrWorkerGlobalScope**에 존재하며
**WindowOrWorkerGlobalScope**는 **Window 오브젝트**에 존재합니다.

자바스크립트라면 ECMAScript 스펙에 작성되어 있어야 하나  
Window 오브젝트는 HTML 스펙에 작성되어 있습니다.

자바스크립트 파일에 존재한다고 해서 전부 자바스크립트가 아닙니다.

## JavaScript 개념

### 문장

자바스크립트 코드의 실행 단계  
세미콜론(;)까지 하나의 문장

세미콜론을 작성하지 않으면 **자동으로** 삽입됩니다.

아래 작성한 코드와 `return`경우 세미콜론을 제대로 안할 경우
결괏값이 원하는 값과 다른 경우가 발생합니다.

```javascript
/**
 * 결괏값
 * 1. undefinde
 * 2. 6
*/

function sports() {
    return
        1 + 5;
}

console.log("1. ", sports());

function point() {
    return (
        1 + 5
    );
}

console.log("2. ", point());
```

### 변수

`const`변수는

- 변수에 값을 할당하여 전체를 바꿀수는 없지만
- 오브젝트(Object Array)이면 값을 바꿀 수 있는 시멘틱
- 상수 변수가 아니라 const 변수입니다.


### 값 변경 불가 설정

`Object.freeze`는 변수에 할당된 프로퍼티 값을 바꿀 수 없도록 설정합니다.

```javascript
/**
 * 결괏값
 * 1. {item:200}
 * 2. {item:200}
*/
const point = {item: 100}
point.item = 200;
console.log("1. ", point);

Object.freeze(point)
point.item = 300;
console.log("2. ", point);

```