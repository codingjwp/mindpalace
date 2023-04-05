# Styled-compnents 

## 선택자 

```javascript
import styled from 'styled-compnents'

const Wrapper = styled.div`
/* 전체 설정됨 */
p {
  color: blue;
}
/* Wrapper 한 단계 아래 p 태그 */
> p {
  color: red;
}
/* 한 단계 아래 첫 번째 p 태그 */
> p:first-child {
  color: green;
}
`

const Example = () => (
  <Wrapper>
    <p>First child</p>
    <p>Second child</p>
    <div>
      <p>Descendant</p>
    </div>
  </Wrapper>
);

```