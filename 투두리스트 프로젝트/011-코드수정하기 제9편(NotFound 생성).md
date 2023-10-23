# NotFound 페이지 생성 하기

## 문제점 찾기

- 경로가 잘못 됬을때 **Not Fuound** 페이지를 띄어줘야 하는데 이전 코드에는 없어서 특정 페이지를 생성

## 코드

```typescript
import Layout from "../styles/Layout";
import styled from 'styled-components';

const NotFound = () => {
 return (<Layout>
  <NotFoundTitle>404</NotFoundTitle>
  <NotFoundText>Page Not Found</NotFoundText>
 </Layout>);
}

const NotFoundTitle = styled.h1`
  margin-top: 3rem;
  text-align: center;
  font-size: 10rem;
`
const NotFoundText = styled.p`
  margin-top: 3rem;
  text-align: center;
  font-size: 2.5rem;
`

export default NotFound;
```
