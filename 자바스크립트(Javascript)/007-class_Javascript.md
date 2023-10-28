# Class

**클래스 정의**

객체지향 프로그래밍에서 특정 객체를 생성하기 위한 변수와 메소드를 정의하는 일종의 틀

## Class Keyword

### 기본적인 틀 클래스

```javascript
class IdolModel {
  name = '홍길동';
  year = 2003;
}

const person = new IdolModel();
```

### 파라매터를 받기 위한 클래스  

**constructor** - 생성자

자바스크립트에서는 `name, year` 프로퍼티는 옵셔너리라서 아래 코드와 같이 설정을 안해줘도 됩니다.
하지만 안해줄 경우 `this.year`처럼 정의를 해줘야 한다. 

```javascript
class IdolModel {
  name;
  year;
  constructor(name, year) {
    this.name = name;
    this.year = year;
  }
}
const person = new IdolModel('홍길동', 2003);
```

### 클래스 메소드

```javascript
class IdolModel {
  name;
  year;
  constructor(name, year) {
    this.name = name;
    this.year = year;
  }
  sayName() {
    return this.name;
  }
}
const person = new IdolModel('홍길동', 2003);
```

### 클래스의 type은 function이다

```javascript
console.log(typeof IdolModel);
// 결과값 : function
```
## Inheritance 상속

객체들간의 관계를 구축하는 방법입니다.

상속을 하는 클래스는 부모클래스 슈퍼클래스라고 합니다.  
상속을 받은 클래스는 자식클래스라고 합니다.  

부모클래스는 자식 클래스 메소드 변수를 사용할 수 없ㅅ브니다.  
자식은 `instanceof` 부모와 자신의 인스턴스입니다.


```javascript
class IdolModel {
  name;
  year;
  constructor(name, year) {
    this.name = name;
    this.year = year;
  }
}

class FIdolModel extends IdolModel {
  dance() {
    return '여자 모델이 춤을 춘다.';
  }
}
class MIdolModel extends IdolModel {
  sing() {
    return '남자 모델이 노래를 부른다.';
  }
}

const fModel = new FIdolModel('여자', 2003);
const mModel = new MIdolModel('남자', 2001);

//결과값 : fModel : {name: 여자, year: 2003} 
//결과값 : mModel : {name: 남자, year: 2001}

fModel.dance();
mModel.sing();
// 결과값:  '여자 모델이 춤을 춘다.'
// 결과값: '남자 모델이 노래를 부른다.'
```