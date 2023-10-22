# **JavaScript 기본 2편**

## **API란 무엇일까?**

응용프로그램에서 사용할 수 있도록 운영체제나, 프로그램 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스  
에플리케이션에서 데이터를 읽거나 쓰기 위해 사용하는 인터페이스  

### **인터페이스(interface)란?**

상호간에 소통을 하기위한 접점

## **동기와 비동기**

동기 : 결과를 기다리는 것  
비동기 : 결과를 기다리지 않는 것

|동기|비동기|
|:---:|:---:|
|자원 비효율적|자원 효율적|
|업무가 단순|업무가 복잡|


## **버블링 & 캡처링**

### **캡처링(Capturing)**

이벤트가 최상위로부터 타겟까지 타고 내려가는 형식  
`addEventListener` 옵션으로 true를 주면 캡처링  
아래 코드 결괏값 

1. capturing body 태그
2. capturing main 태그
3. capturing div 태그
4. capturing p 태그
5. capturing span 태그

```html
<body>
  <main>
    <div>
      <p>
        <span></span>
      </p>
    </div>
  </main>
  <script>
    const $body = document.querySelector('body');
    const $main = document.querySelector('main');
    const $div = document.querySelector('div');
    const $p = document.querySelector('p');
    const $span = document.querySelector('span');
    $span.addEventListener('click', function(){ console.log('capturing span 태그')}, true);
    $p.addEventListener('click', function(){ console.log('capturing p 태그')}, true);
    $div.addEventListener('click', function(){ console.log('capturing div 태그')}, true);
    $main.addEventListener('click', function(){ console.log('capturing main 태그')}, true);
    $body.addEventListener('click', function(){ console.log('capturing body 태그')}, true);
  </script>
</body>
```


### **버블링(Bubbling)**

이벤트가 타겟부터 최상위까지 올라가는 형식  
`addEventListener`는 기본적으로 버블링으로 동작  
아래 코드 결괏값  

1. span 태그
2. p 태그
3. div 태그
4. main 태그
5. body 태그

```html
<body>
  <main>
    <div>
      <p>
        <span></span>
      </p>
    </div>
  </main>
  <script>
    const $body = document.querySelector('body');
    const $main = document.querySelector('main');
    const $div = document.querySelector('div');
    const $p = document.querySelector('p');
    const $span = document.querySelector('span');
    $span.addEventListener('click', function(){ console.log('span 태그')});
    $p.addEventListener('click', function(){ console.log('p 태그')});
    $div.addEventListener('click', function(){ console.log('div 태그')});
    $main.addEventListener('click', function(){ console.log('main 태그')});
    $body.addEventListener('click', function(){ console.log('body 태그')});
  </script>
</body>
```

### **event.eventPhase 이벤트 흐름의 단게**

해당 코드는 이벤트 흐름을 나타낸다.  
캡처링으로 내려갈때는 1, 타겟에 왔을때는 2, 버블링할때는 3

```javascript
const $span = document.querySelector('span');
$span.addEventListener('click', function(event) { 
  
  console.log('[' + event.evntPhase + '] span 태그');
});
```

### **이벤트 흐름 멈추기 evet.stopPropagation()**

작성된 위치에서 이벤트 흐름을 멈춘다.  
결괏값

1. span 태그

```html
<body>
  <main>
    <div>
      <p>
        <span></span>
      </p>
    </div>
  </main>
  <script>
    const $body = document.querySelector('body');
    const $main = document.querySelector('main');
    const $div = document.querySelector('div');
    const $p = document.querySelector('p');
    const $span = document.querySelector('span');
    $span.addEventListener('click', function(event){ 
      event.stopPropagation();
      console.log('span 태그')
    });
    $p.addEventListener('click', function(){ console.log('p 태그')});
    $div.addEventListener('click', function(){ console.log('div 태그')});
    $main.addEventListener('click', function(){ console.log('main 태그')});
    $body.addEventListener('click', function(){ console.log('body 태그')});
  </script>
</body>
```

### **event.preventDeFault()**

이벤트의 기본동작 방지 
submit이나 anchor태그를 막을때 자주 사용