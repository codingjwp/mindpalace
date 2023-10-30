# Scope

상위 스코프에 내용을 사용할 수 있습니다.  
모든 선언은 가장 가까운 스코프를 가져옵니다.  

```javascript
var numberOne = 20;
function levelOne() {
  console.log(numberOne);
}
levelOne();
```

JS에서의 스코프는 **Lexical Scope** 입니다.  
**Lexical Scope** 란 선언된 위치가 상위 스코프를 정합니다.
**Dynamic Scope** 란 실행한 위치가 상위 스코프를 정합니다.

```javascript
var numberThree = 3;
function functionOne() {
  var numberThree = 100;
  funtionTwo();
}
function functionTwo() {
  console.log(numberThree);
}
// 결괏값 3
```

## var, const, let

아래의 코드에서는 왜?? `var i = 999;`의 값이 출력되지 않고 i의 값이 10이 되었을까??
`var`는 새로운 스코프가 생성될 때는 `function`를 선언했을 때만 가능합니다.

```javascript
var i = 999;
for(var i = 0; i < 10; i++) {
  console.log(i);
}
console.log(`i in global scope: ${i}`);
// 결괏값 i in global scope: 10
```

`let, const`는 **for loop**나 **if문** **whlie loop**과 함수와 같이 블록 레벨 스코프도 만들 수 있습니다.

```javascript
var i = 999;
//block level scope
for(let) i = 0; i < 10; i++) {
  console.log(i);
}
console.log(`i in global scope: ${i}`);
// 결괏값 i in global scope: 10
```