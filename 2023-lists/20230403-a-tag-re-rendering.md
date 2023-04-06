# `<a>` 태그의 리랜더링 문제.

## 문제 발생

SPA로 제작할려는 코드 내용 중 일부분 사이드바에서 다른 페이지로 이동할때 경우를 생각해 a를 사용하여 이동을 하였는데 Network를 확인해보니 계속 전체적으로 리 랜더링 문제가 발생하였다.

```html
<ul>
  <li><a href="/">홈</a></li>
  <li><a href="/musiclist">뮤직리스트</a></li>
  <li><a href="mylist">마이리스트</a></li>
</ul>
```

## 수정 방안 첫번째 (해결 X)

처음에는 처음써보는 `createBrowserRouter`을 이용한 문제인가 싶어서 `BrowserRouter`을 써보았으나 똑같은 문제가 발생.

## 수정 방안 두번째 (해결 O)

여러가지 검색을 하다 `react-router-dom`에서 사용하는 `<Link>`태그를 발견하여 해당 코드 중 `<a>`를 `<Link>`로 전부 변경하니 리랜더링 없이 작동.

```typescript
import { Link } from 'react-router-dom'

<ul>
  <li><Link href="/">홈</ㅣ></li>
  <li><Link href="/musiclist">뮤직리스트</Link></li>
  <li><Link href="mylist">마이리스트</Link></li>
</ul>
```

## 이유??

`<Link>`를 공부할 때 `<a>`와 유사한 역할이라고 하여 `<a>`를 써도 상관없는 것 같아서 사용하였으나. 자세히 찾아보니 `<a>`경우 기본 동작으로 페이지 새로고침이 있었다.. `<a>`태그를 사용할려면 아래 코드 처럼 수정이 필요.

```typescript
import React from 'react';
import { useNavigate } from 'react-router-dom';
const MoveCompose = () => {
  const navigate = useNavigate();

  const handleClick = (e: React.MouseEvent<HTMLAnchorElement>) => {
    // 새로고침 방지
    e.preventDefault();
    navigate('/mylist');
  };

  return <a onClick={handleClick}>뮤직리스트</a>;
};
export default MoveCompse;
```
