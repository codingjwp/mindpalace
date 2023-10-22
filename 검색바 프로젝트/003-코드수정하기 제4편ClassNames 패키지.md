# ClassNames 패키지

조건부로 className을 결합하는 간단한 Javascript 유틸리티  
[npm classnames](https://www.npmjs.com/package/classnames), [github classnames](https://github.com/JedWatson/classnames#readme)  

sass를 사용하여 검색바 프로젝트를 만들었더니 CSS in JS와 다른게 js 변화로 className을 지정하는 부분이 어지럽게 되어 편하게 하는 내용을 찾는중 `classnames` 라는 패키지를 찾아 적용을 해보려고 합니다.

## 설치 방법

```bash
npm install classnames
```

## 사용방법

React에 사용하기위해 선언.

```typescript
import classNames from 'classnames';
```

문자열이나 객체로 선언하여 사용 용법에 종류가 표시되어 있어서 코드를 갖고왔습니다.  
`null`, `false`, `undefined`, `0`, `''`는 무시  

```typescript
classNames('foo', 'bar'); // => 'foo bar'
classNames('foo', { bar: true }); // => 'foo bar'
classNames({ 'foo-bar': true }); // => 'foo-bar'
classNames({ 'foo-bar': false }); // => ''
classNames({ foo: true }, { bar: true }); // => 'foo bar'
classNames({ foo: true, bar: true }); // => 'foo bar'
classNames('foo', { bar: true, duck: false }, 'baz', { quux: true }); // => 'foo bar baz quux'
classNames(null, false, 'bar', undefined, 0, 1, { baz: null }, ''); // => 'bar 1'
```
