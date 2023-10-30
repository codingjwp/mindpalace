# Function create Object

아래의 코드를 기본으로 사용합니다.

```javascript
function person(name, year) {
  this.name = name;
  this.year = year;
  this.steel = function() {
    return `${this.name}이 도둑질을 하였습니다.`;
  }
}
```

## 생성자 함수

생성자 함수인 경우 꼭 **new**를 작성해 줘야 합니다.
만약 **new** 키워드를 사용하지 않고 생성자 함수를 실행했을 경우 `global(globalThis)`에 값을 넣게 됩니다.  
**new**를 사용했는지 아닌지 확인이 가능합니다. `console.log(new.target)`  


### 주의사항

**arrow 함수**경우에는 생성자 함수를 사용할 수 없습니다.
**크롬 개발자도구 콘솔에서 할때는 global이 없고 globalThis가 존재합니다.**

```javascript
 const shif = new person('도둑', 2001);
 console.log(shif);
 // person {name: '도둑', year: 2001, steel: ƒ}
 
 const shif2 = person('도둑2', 2002);
 console.log(shif2);
 // undefined
 console.log(global.steel());
 // '도둑2이 도둑질을 하였습니다.'
```