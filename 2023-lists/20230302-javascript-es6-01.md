# Javascript Es6

## 기본 매개 변수 설정
\- 값이 없을시 기본 매개 변수로 들어가게 된다.   

```javascript
const greeting = (name = "Anonymous") => "Hello " + name;

console.log(greeting("John"));
console.log(greeting());

// 출력값 : Hello John Hello Anonymous
```

## Rest Parameter
\- 나머지 인자, 여분의 인자 들이 배열로 만들어 진다.   
\- isArray()함수를 사용하면 true가 출력됩니다.   

```javascript
const sum = (...args) => {
  let total = 0;
  for (let i = 0; i < args.length; i++) {
    total += args[i];
  }
  return total;
}

console.log(sun(0, 1, 2));
console.log(sun(1, 2, 3, 4));
console.log(sun(5));
console.log(sun(0));

//결과값 3, 10, 5, 0;
```

## Spread Operator
\- 그대로 배열이나 객체의 항목들을 펼친다는 것이며. 쉽게 말해 "[]"를 없애고 값 만 보이는 것 입니다..   

```javascript
const arr1 = ['JAN', 'FEB', 'MAR', 'APR', 'MAY'];
let arr2;
//arr1에 펼쳐지는 것에 
arr2 = [...arr1];
console.log(arr2);
// arr2 결과값  [ 'JAN', 'FEB', 'MAR', 'APR', 'MAY' ];
```

## Destructuring Assignment (구조 분해 할당)
\- 구조 분해 할당은 객체나 배열의 속성을 해체하여 그 값을 개별 변수에 담을 수 있게 하는 JavaScript 표현식   

```javascript
const HIGH_TEMPERATURES = {
  yesterday: 75,
  today: 77,
  tomorrow: 80
};
// 구조 분해 할당
const { today, tomorrow } = HIGH_TEMPERATURES;

console.log(today, tomorrow);
// 결과값 77 80;
```

## Destructuring Assignment (구조 분해 할당) 새 변수 이름 지정
\- 값을 추출할 때 새 변수 이름 지정 가능   
\- 
```javascript
const HIGH_TEMPERATURES = {
  yesterday: 75,
  today: 77,
  tomorrow: 80
};
// 새 변수명에 지정.
const {today: highToday, tomorrow: highTomorrow} = HIGH_TEMPERATURES;

console.log(highToday, highTomorrow);
// 결과값 : 77, 80
```

## Destructuring Assignment (구조 분해 할당) 중첩된 객체에서 변수 할당.
\- 중첩된 객체에서 변수를 설정합니다.   

```javascript
const LOCAL_FORECAST = {
  yesterday: { low: 61, high: 75 },
  today: { low: 64, high: 77 },
  tomorrow: { low: 68, high: 80 }
};

const {today : { low: lowToday , high: highToday}} = LOCAL_FORECAST;  

console.log(lowToday, highToday);
//결과 값 64 77
```

## Destructuring Assignment (구조 분해 할당) 배열 할당.
```javascript
const [a, b] = [1, 2, 3, 4, 5, 6];
console.log(a, b)
// 결과 값 : 1, 2
// 원하는 index에 도달하기 위해 ',' 사용
const [a, b,,, c] = [1, 2, 3, 4, 5, 6];
console.log(a, b, c)
// 결과 값 : 1, 2 5 

let a = 8, b = 6;
[a, b] = [b, a];

console.log(a, b);
//결과값 6, 8;

const [a, b, ...arr] = [1, 2, 3, 4, 5, 7];
console.log(a, b);
console.log(arr);
//결과값, 1, 2, [3, 4, 5, 7];
```


## Object Literal(객체 리터럴)
\- 객체 리터럴 속기 구문을 사용하면 객체의 속성 이름과 값이 같은 경우에 축약하여 작성할 수 있습니다.   

```javascript
const getObject = (x, y) => {
  return( {
    x: x,
    y: y
  });
}
\\ 속기 부분
const getObject = (x, y) => {
  return ({x, y});
}
```

## ES6 간결한 함수 선언

```javascript
const bicycle = {
  gear: 2,
  /*
    기존 함수 
    setGear function(newGear): {
      this.gear = newGear;
    }
  */
  setGear(newGear) {
    this.gear = newGear;
  }
};

bicycle.setGear(3);
console.log(bicycle.gear);
// 결과 값 3;
```

# JavaScript Promise
\-  비동기 처리를 위해 사용되는 객체   
\- 대기 (pending): 이행하지도, 거부하지도 않은 초기 상태.
\- 이행 (fulfilled): 연산이 성공적으로 완료됨.
\- 거부 (rejected): 연산이 실패함.

```javascript
  const makeServerRequest = new Promise((resolve, reject) => {
    let responseFromServer = false;
      
    if(responseFromServer) {
      resolve("We got the data");
    } else {  
      reject("Data not received");
    }
  });

  makeServerRequest.then(result => {
    console.log(result);
  }).catch(error => {
    console.log(error);
  });
```