# Immutable

아래의 코드를 기본으로 사용합니다.

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
```

## Extensible

확장이 가능 여부를 판단합니다.  
아래의 코드와 같이 `Object.preventExtensions(person)`를 사용하기 전에 `group`은 person에 생성이 되었으나 `job`은 생성이 되지 않았습니다.
삭제는 가능하나 추가는 불가능합니다.
아래의 코드와 같이 `delete person['group']`을 하여 `person` 객체값에서 `group`이 사라진걸 확인 할 수있습니다.

```javascript
person['group'] = '활빈당'
Object.preventExtensions(person)
console.log(Object.isExtensible(person));
// 결괏값 false
// person 데이터값 : {name: '홍길동', year: 2002, group: '활빈당'}

person['job'] = '의적'
console.log(person);
// 결괏값 {name: '홍길동', year: 2002, group: '활빈당'}

delete person['group'];
// 결괏값 true
console.log(person);
// person 데이터값 : {name: '홍길동', year: 2002}
```

## Seal

객체를 봉인하는 기능을 말합니다.  
`Object.seal(person)`과 같이 사용하였을 경우 추가, 삭제 부분이 적용이 안됩니다.  
또한 에러발생을 하지 않기 때문에 이 부분에 대해서는 주의하시기 바랍니다.  
`defineProperty`에서 **value**, **writable** 등 바꾸는게 가능하면 **Seal**은 **configurable**를 `false`하는 것과 동일합니다.

```javascript
Object.seal(person);
console.log(Object.isSealed(person));
// 결괏값: true
person['group'] ='활빈당';
console.log(person);
// 결괏값: {name: '홍길동', year: 2002}
delete person['name'];
console.log(person);
// 결괏값: {name: '홍길동', year: 2002}
```

## Freeze

**defineProperty**에서 **writable**, **configurable**를 `false`하는 것과 동일합니다.

```javscript
Object.freeze(person);
console.log(Object.isFrozen(person));

console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// 결괏값 {value: '홍길동', writable: false, enumerable: true, configurable: false}
```


### 주의사항

**위에 3가지 내용들은 하위 객체까지 적용되는 것이 아니고 적용한 객체만 적용됩니다.**