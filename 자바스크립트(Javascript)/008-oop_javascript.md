# OOP

## getter setter

기본적인 `get, set` 설정입니다.  
get을 지정한 부분은 함수(메소드)가 아니기 때문에 뒤에 `()`를 붙이지 말아야 합니다.

```javascript
class IdolModel {
  name;
  year;
  constructor(name, year) {
    this.name = name;
    this.year = yaer;
  }
  get nameAndYear() {
    return `${this.name}-${this.year}`;
  }
  set setName(name) {
    this.name = name;
  }
}

const classTest = newIdolModel('홍길동', 2003);
// console.log(classTest.nameAndYear);
// classTesxt.setName = '아이돌';
```

## 프라이빗 프로퍼티

프라이빗 설정을 하면 결과 값에 `#name` 부분이 출력되지 않습니다.  
그렇기 때문에 `get`나 `set`을 사용하여 데이터를 가져오거나 입력할 수 있습니다.  
`#`부분은 ES6부터 사용할 수 있습니다.

```javascript
class IdolModel {
  #name;
  year;
  constructor(name, year) {
    this.#name = name;
    this.year = yaer;
  }
  get nameAndYear() {
    return `${this.name}-${this.year}`;
  }
  set setName(name) {
    this.name = name;
  }
}

const classTest = newIdolModel('홍길동', 2003);
// console.log(classTest);
// 결과값 classTest { year: 2003}
```
## Static keyword

`static`을 사용하면 클래스 자체에 귀속됩니다.  
그렇기 때문에 `new`를 사용하여 객체를 생성할 필요 없습니다.

```javascript
class IdolModel {
  name;
  year;
  static groups = '활빈당';

  constructor(name, year) {
    this.#name = name;
    this.year = yaer;
  }
  
}

// console.log(IdolModel.groups);
// 결과값 : 활빈당
```
### factory construtor

아래와 같이 만드는 이유는 어떤 형태로 데이터를 받아서 인스턴스를 만들지 탬플릿하여 정의할 수 있습니다.

```javascript
class IdolModel {
  name;
  year;
  static groups = '활빈당';

  constructor(name, year) {
    this.#name = name;
    this.year = yaer;
  }
  static fromObject(object) {
    return new IdolModel(
      object.name,
      object.year,
    )
  }
}

// const test = IdolModel.fromObject({name: '홍길동', year: 2003})
// console.log(test); 결과 값 test : {name: '홍길동', year: 2003}
```

### super

부모 클래스를 나타냅니다. 즉 `super() => IdolModel()`과 같습니다.


```javascript
class IdolModel {
  name;
  year;

  constructor(name, year) {
    this.#name = name;
    this.year = yaer;
  }
}

class FIdolModel extends IdolModel {
  part;
  constructor(name, yaer, part) {
    super(name, year);
    this.part = part;
  }
  dance() {
    return '여자 모델이 춤을 춘다.';
  }
}

```

### Override

보통 OOP라면 `안녕하세요 ${super.name}입니다 ${this.part}를 맡고있습니다.`가 맞지만  
자바스크립트는 제대로 만들어진 OOP가 아니여서 `${super.name} => ${this.name}`를 해야 적용됩니다.

```javascript
class IdolModel {
  name;
  year;

  constructor(name, year) {
    this.#name = name;
    this.year = yaer;
  }
  sayHello() {
    return `안녕하세요 ${this.name}`;
  }
}

class FIdolModel extends IdolModel {
  part;
  constructor(name, yaer, part) {
    super(name, year);
    this.part = part;
  }
  sayHello() {
    return `안녕하세요 ${this.name}입니다 ${this.part}를 맡고있습니다.`;
  }
}