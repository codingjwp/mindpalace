# ProtoType

아래의 코드가 기본적으로 사용 됩니다.

```javascript
function person(name, year) {
  this.name = name;
  this.year = year;
  this.steel = function() {
    return `${this.name}이 도둑질을 하였습니다.`;
  }
}
```

__proto__ 모든 객체에 존재하는 프로퍼티입니다.  
아래의 코드는 숨겨진 내용을 전부 보여달라고 요청하는 코드입니다.  

```javascript
console.dir(person.prototype, {
  showHidden: true,
})
```

## Prototype chain

모든 `prototype`은 최상위 `Object.prototype`을 가지게 됩니다.  
