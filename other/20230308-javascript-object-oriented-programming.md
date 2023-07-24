# OOP(Object-oriented Programming)

\- 객체 지향 프로그래밍   
\- 프로그램을 객체들의 집합으로 구성하여 객체들 간의 상호작용을 통해 프로그램을 작성하는 방법   

```javascript
// 산자는 객체가 특정 클래스의 인스턴스인지 또는 객체가 특정 클래스의 프로토타입 체인에 있는지를 확인하는 데 사용됩니다.
object instanceof constructor // object 검사할 객체, constructor 검사할 클래스 생성자 함수

// 모든 Bird를 쓰는 속성에 추가
Bird.prototype.numLegs = 2;
```

<br>

\- 여러개 속성 만들기   

```javascript
function Dog(name) {
  this.name = name;
}

Dog.prototype = {
  constructor: Dog,
  numLegs: 2,
  eat: function() {
    console.log("nom nom nom");
  },
  describe: function() {
    console.log("My name is " + this.name);
  }
};
```

<br>

\- 객체의 프로토타입이 어디서 왔는지   
```javascript
function Dog(name) {
  this.name = name;
}

let beagle = new Dog("Snoopy");
Bird.prototype.isPrototypeOf(duck);
```

<br>

### IIFE (Immediately Invoked Function Expression)
\- IIFE (Immediately Invoked Function Expression)는 JavaScript에서 함수를 정의하고 즉시 실행하는 패턴입니다.
\- 전역 스코프 오염 방지: IIFE를 사용하면 전역 스코프에 변수를 추가하지 않고 코드를 캡슐화할 수 있습니다. 이는 전역 스코프의 오염을 방지하고, 코드의 재사용성과 유지보수성을 높이는 데 도움이 됩니다.   
\- 블록 스코프 에뮬레이션: JavaScript의 var 키워드는 함수 스코프를 가지므로, 블록 스코프를 에뮬레이션하기 위해 IIFE를 사용할 수 있습니다. IIFE 내부에서 정의된 변수는 IIFE 외부에서 접근할 수 없으므로, 블록 스코프와 유사한 효과를 얻을 수 있습니다.   
\- 즉시 실행되는 코드 작성: IIFE는 함수를 정의하고 즉시 실행하는 패턴이므로, 즉시 실행되어야 하는 코드를 작성하는 데 유용하게 사용됩니다.   


<br>

### IIFE로 모듈 만들기

```javascript
let funModule = (function() {
  return {
    isCuteMixin: function(obj) {
      obj.isCute = function() {
        return true;
      };
    },
    singMixin: function(obj) {
      obj.sing = function() {
        console.log("Singing to an awesome tune");
      };
    }
  }
})();

function Dog() {

}

Dog.prototype = {
  constructor: Dog,
}

let begle = new Dog();

funModule.isCuteMixin(begle);
funModule.singMixin(begle);
```