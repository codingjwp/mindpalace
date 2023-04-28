# Array
\- 배열   
```javascript
let arr = [1, 2, 3, 4, 5, 6 ];
```
<br>

\-  추가 `//` 추가된 후 arr 값
```javascript
arr.push(2); // [1, 2, 3, 4, 5, 6, 2 ]
arr.unshift(3) // [3 , 1, 2, 3, 4, 5, 6 ]
```

<br>

\-  삭제 `//` 삭제된 후 arr 값
```javascript
arr.pop(); //  [1, 2, 3, 4, 5 ]
arr.shift(3) // [ 2, 3, 4, 5, 6 ]
arr.splice(2, 2); //[1, 2, 5, 6]
arr.splice(2, 2, 7, 8); //[1, 2, 7, 8, 5, 6] => 뒤에 넣은 숫자는 잘라낸 부분 순서대로 넣어집니다. 구분자 ','
```

<br>

\-  추출
```javascript
arr.slice(1, 3) // [2, 3] 추출할 시작 인덱스, 추출 중지할 인덱스
```

<br>

\-  추출
```javascript
arr.slice(1, 3) // [2, 3] 추출할 시작 인덱스, 추출 중지할 인덱스
```

<br>

\-  요소 확인: 존재하는 첫번쨰 index 값 반환
```javascript
arr.indexOf(1) // true 0
arr.indexOf(0) // -1
```