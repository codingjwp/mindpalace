# **Javascript 기본 3편**

## **Array APIs**

### **map**

주어진 함수를 호출한 결과를 새로운 배열로 반환  
결괏값: [2,4,6,8,10]

```javascript
const numbers = [1,2,3,4,5];

const result = numbers.map(function (number) {
  return number * 2
})
console.log(result)
```

### **some**

배열안에 요소가 주어진 조건에 하나라도 `true`면은 `true`를 반환 전부 `false`면 `false`로 반환  
결괏값: `true`

```javascript
const fluits = ['사과', '배', '바나나'];
console.log(fluits.som((fluits) => fluits === '배'));
```

### **every**

배열안의 요소가 모두 `true`일 경우에만 `true` 하나라도 `false`면 `false`  
결괏값: `false`

```javascript
const fluits = ['사과', '배', '바나나'];
console.log(fluits.som((fluits) => fluits === '배'));
```

### **fillter**

배열안의 요소가 조건에 통과하는 것 만 모아서 새로운 배열을 반환  
결괏값: [4,5]

```javascript
const numbers = [1,2,3,4,5];

const result = numbers.fillter(function (number) {
  return number > 3
})
console.log(result)
```

### **reduce**

배열 안의 요소에 대해 주어진 리듀서 함수를 실행하고 하나의 결과값을 반환  
리듀서 함수  

1. 누적 값(acc)
2. 현재 값(cur)
3. 현재 인덱스(idx)
4. 원본 배열(src)

뒤에 초깃값을 설정 할 수 있다.  
결괏값: 15

```javascript
const numbers = [1,2,3,4,5];

numbers.reduce((acc, cur, idx, src) => {
  return acc + cur
}, 0)

```

## **javascript 모듈 시스템**

분리된 하나의 파일

### **장점**

전역 스코프에 존재하는 변수명이 겹치는 경우 모듈을 분리하여 묘듈만의 네임스페이스를 가진다.  
유지보수가 용이함.

### **종류**

`CommonJS` : NodeJS 환경을 위해 만들어진 모듈  
`ES Module`: ES6에 도입된 모듈  
```html
<script type="module" src='index.js'></script>
```

### **내보내기**

`export`: 외부에서 사용가능하게 내보내는 기능  
`export default`: 모듈에서 하나만 선언이 가능 (import 시 이름을 마음대로 작성가능)  
`export.sum` : CommonJS에서 내보내기
`export.module` : CommonJs에서 한번에 내보내기

### **가져오기**

`import from` : ES Module에서 가져오는 방법

