# Javascript Basic algorithm

### 섭씨온도 화씨온도로 변경
```javascript
function convertCtoF(celsius) {
  let fahrenheit = celsius / 5 * 9 + 32;
  return fahrenheit;
}
convertCtoF(30);
```
<br>

### 문자열 앞뒤 바꾸기
```javascript
function reverseString(str) {
  return str.split("").reverse().join('');
}

reverseString("hello");
```

<br>

### 팩토리얼

```javascript

function factorialize(num) {
  if (num === 0) return 1;
  return num * factorialize(num - 1);
}

factorialize(5);

```

<br>


### 문자열 중 가장 긴 단어 찾기

```javascript

function findLongestWordLength(str) {

  // return Math.max(...str.split(" ").map(word => word.length));
  return  str.split(' ').reduce((longest, word) => Math.max(longest, word.length), 0);
}

findLongestWordLength("The quick brown fox jumped over the lazy dog");

```


<br>


### 배열에서 가장 큰 값들 찾아 리턴
```javascript
function largestOfFour(arr) {
  let arry = [];
  arr.map(item => arry.push(item.reduce((before, after) => Math.max(before, after))));
  return arry;
}

largestOfFour([[4, 5, 1, 3], [13, 27, 18, 26], [32, 35, 37, 39], [1000, 1001, 857, 1]]);

```

<br>

### 문자열 끝에 있는 문자열 맞추기.

```javascript
function confirmEnding(str, target) {
  return str.slice(str.length - target.length, str.length).indexOf(target) > -1 ? true : false;
}
confirmEnding("He has to give me a new name", "name");
```
```javascript
function confirmEnding(str, target) {
  //  ECMAScript 6 규격에 포함
  return str.endsWith(target);
}
confirmEnding("He has to give me a new name", "name");
```

<br>

### 문자열 반복

```javascript
function repeatStringNumTimes(str, num) {
  return str.repeat(num);
}

repeatStringNumTimes("abc", 3);
```
```javascript
function repeatStringNumTimes(str, num) {
  let test = "";
  while (num > 0) {
    test += str;
    num--;
  }
  return test;
}
repeatStringNumTimes("abc", 3);
```

<br>

### 문자열 자르기

```javascript
function truncateString(str, num) {
  return str.length > num ? str.slice(0, num) + '...' : str;
}
truncateString("A-tisket a-tasket A green and yellow basket", 8);
```

<br>