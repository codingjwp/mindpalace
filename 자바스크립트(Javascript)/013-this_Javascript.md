# this 키워드

JS는 **Lexical Scope**를 사용하기 때문에 함수의 상위 스코프가 정의 시점에 평가된다.  
하지만 **this**키워드 경우 바인딩이 객체가 생성되는 시점에 결정됩니다.  

## this 키워드 경우 어떤걸 가르키냐는 세가지만 기억하면 됩니다.

- 일반 함수 호출할때 this가 최상위 (global 또는 window)를 가리킨다.
- 메서도로 호출할때 호출된 객체를 가르킨다.
- new 키워드를 사용해서 객체를 생성했을땐 객체를 가리킨다.

```javascript
// 첫번 째
function thisTest() {
  return this.
}
console.log(thisTest() === global);

// 두번 째
const person = {
  name: '홍길동',
  year: 2002,
  steel: function() {
    return `${this.name}이 도둑질을 하였습니다.`
  },
  set age(age) {
    this.year = new Date().getFullYear() - age; 
  }
}

// 세번 째
function Person(name, year) {
  this.name = name,
  this.year = year,
  this.steel = function() {
    return `${this.name}이 도둑질을 하였습니다.`
  }
}

const personTest = new Person('홍길동', 2003);
cossole.log(personTest.steel())
```

### 원하는 값으로 this 매핑 하기

`call()` :  컴마를 기준으로 아규먼트를 순서대로 넘겨줍니다.  
`apply()` : 아규먼트를 리스트로 입력해야합니다.  

```javascript
function returnName() {
  return this.name;
}

const person = {
  name: '홍길동',
}
console.log(returnName.call(person));
// 홍길동
console.log(returnName.apply(person));
// 홍길동
```

아규먼트 적용한 테스트 코드 입니다.

```javascript
function multiply(x, y, z) {
  return `${this.name} 결괏값: ${x * y * z}`;
}
console.log(multiply.call(person, 3, 4, 5));
// 홍길동 결괏값: 60
console.log(multiply.apply(person, [3, 4, 5]));
// 홍길동 결괏값: 60
```

`bind()` : 원하는 변수에다가 원하는 객체를 삽입할 수 있습니다. this를 지정해줄 수 있습니다. bind 경우는 바로 값이 반환되지 않고 실행시켜야 반환합니다.  

```javascript
const laterFuc = multiply.bind(person, 3, 4, 5)
console.log(lateFuc);
// bound multiply
console.log(lateFuc());
// 홍길동 결괏값: 60
```
