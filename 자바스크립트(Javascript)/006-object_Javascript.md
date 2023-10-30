# Object

**특징**  
- const를 선언한 객체는 객체 자체를 변경할 수 없다.
- 객체의 프로퍼티나 메서드는 수정이 가능하다.

## method

Object 안에 넣는 함수를 method라고 한다.

```javascript
const yuJin = {
  name: '안유진';
  group: '아이브';
  dance: function () {
    return `${this.name}이 춤을 춥니다`;
  }
}
```

## 도움 되는 기본기

```javascript
// 키값 다가져오기
Object.keys(yuJin);
// 벨류값 다 가져오기
Object.value(yuJin);
// 아래와 같이 사용시 키값이 동일하게 name이 적용
const name = '안유진';
const yuJin = {
  name
}
```

## Copy by Value(값에 의한 전달)

기본적으로 primitive 값은 **copy by value**입니다.

```javascript
let origin = 'origin'
let clone = origin;

clone += ' copy';
```

새로운 메모리에 복사되어 생성됩니다.


## Copy by Reference(참조에 의한 전달)

Object가 정장된 주소를 `orgin`, `clone`이 동일한 주소를 가지기 때문에 프로퍼티를 수정하여도 동일하게 변경됩니다.

```javascript
const origin = {
  name: 'origin',
  group: 'group1'
}
const clone = origin;
```

## 함수 객체

아래 코드와 같은 함수를 생성자 함수라고 합니다.

```javascript
function IdolFunc(name, year) {
  this.name = name;
  this.year = name;
}
const getIdol = IdolFunc('가을', 2002);
// 결괏값: IdolFunc : { name: '가을', year : 2002 }

```

## 프로퍼티 Attribute

데이터 프로퍼티 : 키와 값으로 형성된 실질적 값을 갖고 있는 프로퍼티  
엑세서 프로퍼티 : 자체적으로 값을 가지고 있지 않고 다른 값을 가져오거나 설정할 때 호출되는 함수로 구성된 프로퍼티  
- value : 실제 프로퍼티 값
- writable : 값을 수정할 수 있는지 여부 
- eumerable : 열거가 가능한지 여부
- configurable :  프로퍼티 어트리뷰트를 재정의가 가능한지 여부 `flase`일 경우 프로퍼티 삭제나 어트리뷰트 변경이 금지됩니다. 하지만 writeable이 true인 경우 값 변경과 writable을 변경가능 합니다.

```javascript
const person = {
  name: '홍길동',
  year: 2002
}

console.log(Object.getOwnPropertyDescriptor(person, 'name'));

// 결괏값 
// {value: '홍길동', writable: true, enumerable: true, configurable: true}
```

### `getOwnPropertyDescriptor`

```javascript
const person = {
  name: '홍길동',
  year: 2002,
  get  age() {
    return new Date().getFullYear() - this.year; 
  },
  set age(age) {
    this.year = new Date().getFullYear() - age; 
  }
}

console.log(Object.getOwnPropertyDescriptor(person, 'age'));
// 결괏값 : {enumerable: true, configurable: true, get: ƒ, set: ƒ};
```

### `defineProperty`

value값만 입력 시 나머지 전부 false  

```javascript
Object.defineProperty(person, 'height', {
  value : 172,
})
// {name: '홍길동', year: 2002, height: 172}
console.log(Object.getOwnPropertyDescriptor(person, 'height'));
// 결괏값 : {value: 172, writable: false, enumerable: false, configurable: false}
```
**writable**을 할 경우 value 값을 변경할 수 없습니다.

```javascript
person.height = 180;
Object.defineProperty(person, 'height', {
  writable : false,
})
console.log(Object.getOwnPropertyDescriptor(person, 'height'));
// 결괏값: {value: 172, writable: false, enumerable: true, configurable: true}
person.height = 172;
console.log(person);
```

아래의 첫번 째 for로 키를 열거하면 `name, year, age, height`가 표시됩니다. 하지만 **enumerable**를 `false`
하면 입력한 키 값에 표시가 안됩니다.

```javascript
for(let key in person) {
  console.log(key)
}

Object.defineProperty(person, 'name', {
  enumerable : false,
})

for(let key in person) {
  console.log(key)
}

// 결괏값 : yaer name height
```
**configurable**를 `false`하면 다른 프로퍼티를 수정하지 못하게 됩니다.
하지만 **writable**를 `true`로 같이 수정시 **value** 값을 수정 할 수 있습니다.

```javascript
Object.defineProperty(person, 'height', {
  configurable :false,
})

Object.defineProperty(person, 'height', {
  enumerable: true,
})

```

