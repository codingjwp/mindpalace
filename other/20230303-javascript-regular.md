# Javascript Regualr


## Refualr test
\- 문자열의 일부를 맞추는데 사용.   

```javascript
let myString = "Hello, World!";
let myRegex = /Hello/;
let result = myRegex.test(myString);
//결과값 true

// 여러가지 패턴을 검사할 경우 적용
let myRegex = /dog|cat|bird|fish/;
// {2, }  앞의 문자가 2번 이상 반복되는 경우

```
<br>

\- 대소문자 구별 안하고 검색 하는 플래그   
```javascript
// /i 플래그는 대소문자를 구분하지 않는 검색을 수행하도록 지정
let myRegex = /Hello/i;

```

<br>

\- match() 문자열 일치하는 부분을 찾아 배열로 반환   
\- 없을시 null 반환
```javascript
const myString = 'Hello, World!';
const myRegex = /World/;

console.log(myString.match(myRegex)); // [ 'World', index: 7, input: 'Hello, World!', groups: undefined ]
```

<br>

\- 전역검색 
```javascript
const myString = 'Hello Hello World`;
const myRegex = /Hello/g;

myString.match(myRegex);

```

<br>

\- 특정 문자들만 찾을 수 있는 방법
```javascript
let bigStr = "big";
let bagStr = "bag";
let bugStr = "bug";
let bogStr = "bog";
let bgRegex = /b[aiu]g/;
bigStr.match(bgRegex);
```

<br>

\- 소문자 알파맷, 숫자 만 찾을 수 있는 방법 
```javascript
let bgRegex = /[a-e0-9]at/;
string.match(bgRegex);
```

<br>

\-  정규식에서 문자열의 시작
```javascript
let bgRegex = /[^abc]/
```

<br>

\- 1번 이상 문자가 연속적으로 나와있는걸 찾기 위해 사용
```javascript
let bgRegex = /s+/;

/* 0번 이상 문자가 연속적으로 나올떄
* "Aaaaaaaaaaaaaaaarrrgh!"
* 결과값: Aaaaaaaaaaaaaaaa
*/

let bgRegex = /Aa*/
```

<br>

\- 비탐욕적 매칭 찾기   
```javascript
// `.` 모든 문자 `*` 0번이상 중복 `?`일치하는 가장 짧은 문자열
let bgRegx = /<.*?>/;
``` 

<br>

\- 문자열 끝을 알리는 `$`
```javascript
// asdasddddd 경우 찾아짐 dddddasdas 경우 안찾아짐
let bgRegx = /ddddd$/;
``` 

<br>

\- `\`사용한 플래그
```javascript
// 알파벳 대소문자(a-z, A-Z), 숫자(0-9) 및 밑줄(_)을 포함
let bgRegx = /\w/;
// 알파벳 대소문자(a-z, A-Z), 숫자(0-9) 및 밑줄(_) 이것과 반대를 검색
let bgRegx = /\W/;
// 모든 숫자 찾기
let bgRegx = /\d/;
// 모든 숫자가 아닌거 찾기
let bgRegx = /\D/;
// Whitespace
let bgRegx = /\s\r\t\f\n\v/;
// 역참조 첫번째 그룹을 역참조 합니다.
// `()`로 묶인 부분이 그룹
let bgRegx = /((test) \1 \1 )/;
``` 