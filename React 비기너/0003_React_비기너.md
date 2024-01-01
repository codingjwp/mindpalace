# React 비기너 Part 3

Javascript 개념 부분 

## 함수

- 함수 선언문
- 함수 표현식
- 화살표 함수

### 함수 이름

함수 이름은 **함수 코드를 읽지 않아도** **함수 이름**과 **파라미터**로 함수 기능을 알 수 있도록 시맨틱을 부여하여 작명합니다

함수는 동사 형태의 이름으로 부여하며 두 개 이상의 단어를 사용할 경우 두 번째 부터 명사를 사용합니다.

### prototype

- 함수 선어문과 함수 표현식 경우 prototype이 있습니다.
- 화살표 함수 경우 prototype이 없습니다.
- prototype은 객체 지향 프로그래밍의 바탕이 됩니다.

### 대문자 작성 관례

- `function Point() {};`
    - 첫 문자가 대문자이면서 명사인 경우
    - 이것은 이어서 prototype이 있다는 것을 암시합니다.
- 아래와 같은 코드 경우 ES5까지 사용했던 형태 ES6부터는 class를 사용합니다.

```javascript
function Point() {};
Point.prototype.getPoint = function(param) {
    return param + 100;
}

const one = new Point();
console.log(one.getPoint(100));
```

