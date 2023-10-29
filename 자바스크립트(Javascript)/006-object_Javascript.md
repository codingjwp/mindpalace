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

## 프로퍼티 Attribute