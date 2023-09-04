# **JavaScript 기본**

## **DOM**

***Document Object Model***
스크립트 언어로 html를 제어할 수 있도록 웹 문서를 객체화 한것.  

하나의 부모태그와 n개의 자식태그를 가지며 이러한 구조를 표시하면 tree 구조를 가집니다.  
이러한 것을 ***Dom Tree***라고 하며 각각의 요소를 ***Node***라고 합니다.  

```html
<div>
    <h1></h1>
    <div>
        <p></p>
    </div>
</div>
```

## **BOM**

***Browser Object Model***(브라우저 객체 모델)  
브라우저를 스크립터로 제어하기 위한 모델  
`window` : 모든 객체가 소속된 객체, 브라우저 창  
`document` : 현재 문서의 정보를 가지는 객체.  
`history` : 현재 브라우저가 접근한 Url history 제어  
`location` : 문서의 주소와 관련된 객체, window와 document의 프로퍼티  
`screen` : 사용자 디스플레이 정보를 가지는 객체  
`navigator` : 실행중이 애플리케이션에 대한 정보를 알수 있다.

## **자바스크립트 효과적 가져오기**

### **과거 script 태그를 실행하는 방법**

body 태그 제일 하단에 선언하기

```html
<body>
    <button>클릭</button>
    <script src='click.js'></script>
</body>
```

`window.onload` 사용하기, onload는 HTML파싱 Dom생성 외부컨텐츠(image, script, css)등 로드된 후 발생하기에 비효율적  

```html
<head>
    <script>
        window.onload = function () {
            const btn = document.querySelector('btton');
            btn.addEventListener('click', function () {
                alert('hello');
            })
        }
    </script>
</head>
<body>
    <button>클릭</button>
</body>
```

`DomContentLoaded` HTML파싱 DOM생성 후 발생하는 이벤트  

```html
<head>
    <script>
        document.addEventListener('DomContentLoaded', function () {
            const btn = document.querySelector('btton');
            btn.addEventListener('click', function () {
                alert('hello');
            })
        })
    </script>
</head>
<body>
    <button>클릭</button>
</body>
```

### **HTML5 defer async**

`defer` 속성 HTML파싱과 함께 비동기로 JavaScript 파일을 불러온다.  
파싱이 끝난 후에 실행된다.

```html
<script src="click.js" defer></script>
```

`async` 속성 HTML파싱과 함께 비동기로 JavaScript 파일을 불러온다.  
HTML 파싱이 끝나지 않아도 완료된 JavaScript를 실행한다.

```html
<script src="click.js" async></script>
```

## **this**

객체를 가르키는 키워드?  
`this`는 호출한 객체, 호출한 객체가 없으면 `window`  
`this`는 호출한 방법에 의해 결정  
`person.printThis()는` person에 의해서 호출되었기 때문에 this는 person.  
`print()`는 앞에 호출한게 없기때문에 this는 window.  

```javascript
let person = {
    fullname: '테스터',
    age : 20,
    printThis: function() {
        console.log(this);
        console.log("this===person : ", this === person);
        console.log("this===window : ", this === window);
    }
}
person.printThis();
// 결과값
// {full ....}
// true 
// false
let print = person.printThis();
print();
// 결과값
// {window ....}
// false 
// true
```

### **ES5 bind**

bind는 원래 함수에서 한번만 가능합니다.

```javascript
function printThis() {
    console.log(this); // default window
}

let person = {
    name: '홍길동'
}
let printThis1 = printThis.bind(person1);
printThis1();
// 결괏값
// {name: '홍길동'}
```

아래와 같이 작성한 코드에서 `person.hello()`에 this는 setTimeout에서 선언된 것으로 인해 window가 this가 됩니다.  
수정된 코드와 같이 수정시 person이 this가 됩니다.

```javascript
let person = {
    name: '테스트',
    age: 20,
    // hello: function() {
    //     setTimeout(function (){
    //         console.log(this.name);
    //         console.log(this.age);
    //     }, 1000)
    // }
    // 수정 코드
    hello : function () {
        function print() {
            console.log(this.name);
            console.log(this.age);
        }
        setTimeout(print.bind(this), 1000);
    }
}
person.hello();
//결괏값
// undefinde
```

### **예외**

전역 스코프에서는 `this`는 `window`.  
`화살표 함수`와 `Strict Mode`에서의 `this`는 달라집니다.  
화살표 함수는 외부 스코프에 따라 this를 계승 받습니다.  
아래 코드와 같이 `setTimeout에서` `화살표 함수`를 사용 시 화살표 함수를 포함하고 있는 상위 스코프인 `hello`의 `this`를 계승.

```javascript
let person = {
    name: '테스트',
    age: 20,
    hello: function() {
        setTimeout(() => {
            console.log(this.name);
            console.log(this.age);
        }, 1000)
    }
}
// 결괏값
//'테스트'
// 20
```

`Strict Mode` 엄격 모드인 경우 호출한 부분이 없는 경우 `window`가 아닌 `undefinde` 이다

```javascript
'use strict'
function printThis() {
    console.log(this);
}
printThis();
//결괏값 undefinde
```
