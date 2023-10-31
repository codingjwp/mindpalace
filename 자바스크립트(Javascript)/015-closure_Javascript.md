# Closure

클로저는 어떤 함수와 해당 함수가 선언된 렉시컬 환경의 조합입니다.  
**상위 함수보다 하위 함수가 더 오래 살아있는 경우 클로저라고 합니다.**  

기존 함수 내용입니다.  
아래코드 경우 `innerGetNumber` 함수가 콜 스택에서 사라진 후 `getNumber` 함수가 콜 스택에서 사라집니다.  

```javascript
function getNumber() {
  var number = 5;
  function innnerGetNumber() {
    return number;
  }
  return innerGetNumber();
}

console.log(getNumber());
```

아래코드는 클로저 경우입니다.  
`innerGetNumber` 함수자체를 리턴으로 받는 경우 `getNumber` 함수가 콜 스택에서 사라지지만 `console.log(runner())`에서 콜 스택에 `innerGetNumber` 함수가 실행되는 동시에 들어갑니다..

```javascript
function getNumber() {
  var number = 5;
  function innnerGetNumber() {
    return number;
  }
  return innerGetNumber;
}

const runner = getNumber();
console.log(runner());
```

## 어디에 쓰는 걸까??


- 데이터를 캐싱 할 경우에 사용합니다.

```javascript
function cachFunction(newNumber) {
  // 아래 계산이 매우 오래걸린다고 가정
  var number = 10 * 10;
  function innerCacheFunction(newNumber) {
    return number * newNumber;
  }
  return innerCacheFunction;
}
// 오래 걸리는 계산을 미리 작성하고 추가적으로 계산이 필요할 경우 효과적으로 적용이 가능합니다.
const runner = cachFunction();
console.log(runner(10));
console.log(runner(20));
```

- 반복적으로 사용을 할 경우 

아래의 코드와 같이 외부에서 `number`값을 가져올 방법이 업심나 increment를 반환한 곳에서는 `number` 값이 보존되면서 증가합니다.

```javascript
function cachFunction(newNumber) {
  // 아래 계산이 매우 오래걸린다고 가정
  var number = 99;
  function increment() {
    number++;
    return number;
  }
  return increment;
}

const runner = cachFunction();
console.log(runner()); // 100
console.log(runner()); // 101
console.log(runner()); // 102
console.log(runner()); // 103
```

- 정보 은닉

자바스크립트에는 `private`가 생긴지 오래되지 않았습니다.  
아래 `newIdol._year`와 같이 접근이 불가능 하게되는 것 처럼 정보 은닉이 가능합니다.

```javascript
function Idol(name, year) {
  this.name = name;
  var _year = year;
  this.sayNameAndYear = function () {
    return `안녕하세요 저는 ${this.name}이고 ${this._year}에 태어났습니다.`
  }
}

const newIdol = new Idol('안유진', 2003);
console.log(newIdol.name);
console.log(newIdol._year);

```