# React Custom Component 만들기 1

포트폴리오를 만들면서 계속 같은 걸 일관성 없게 만드는 것 같아서 <br/>
개인적으로 재사용성이 있는 형태로 만들어 써보기위해서 커스텀 컴포넌트를 만들어 보기로 했다.
사용한 기술
- react
- vite
- typescript
- styled-compnents

## Button Component(컴포넌트)

1. [버튼 크기](#버튼-크기-정하기)
    - `small`: width: 80px, height: 30px ,font-size: 12px
    - `medium`: width: 120px, height: 40px, font-size: 16px
    - `large`: width: 160px, height: 50px, font-size: 20px
2. [버튼 스타일](#버튼-스타일-칼라-만들기)
    - `filled` : 배경 background-color 전체에 색상이 되어 있는 스타일 
    - `outline`: 테두리 만 boorder 색상 되어 있는 스타일
3. [현재까지 최종 본](#기록-최종본)

<br/>

```typescript
// 위치 /src/composes/buttons/Button.tsx
import styled from 'styled-compnents'

const BaseButton = styled.button.attrs<ButtonAttrsProps>((props) => ({
  type: props.type || 'submit',
}))<ButtonProps>`
`

const Button = (props: ButtonProps) => {
  return (
    <BaseButton type={props.type}>
      {props.children}
    </BaseButton>
  )
}

export default Button;
```
### 버튼 크기 정하기
\- 처음에는 그냥 디자인에 따라 크기가 달라 질텐데 지 정할 필요 없이 그냥 하면 되지 않을 까??<br/>
라는 생각을 했지만 버튼은 재사용되는 용도가 많고 크기가 거의 지정 되어 있는 것과 같다는 생각과<br/>
라이브러리들 보았을떄 크기가 지정되어 있는 것을 보아서 크기를 지정해야 겠다고 생각 했습니다.

<br/>

1. 첫번째 방법 size에 따른 크기 지정 `styled-components`에서 `css` 기능 사용

```typescript
import styled, { css } from 'styled-components'

const BaseButton = styled.button.attrs<ButtonAttrsProps>((props) => ({
  type: props.type || 'submit',
  size: props.size || 'medium',
}))<ButtonProps>`
  ${(props) => props.size === 'small' && css`
    width: 80px;
    height: 30px;
    font-size: 12px;
  `}
  ${(props) => props.size === 'mideum' && css`
    width: 120px;
    height: 40px;
    font-size: 16px;
  `}
  ${(props) => props.size === 'large' && css`
    width: 160px;
    height: 50px;
    font-size: 20px;
  `}
`
```

<br/>

2. 칼라와 같이 객채로 만들고 적용했습니다.

```typescript
/* buttonData.tsx */

export const buttonSize = {
  'small': {
    'font-size': '12px',
    'width': '80px',
    'height': '30px',
  },
  'medium': {
    'font-size': '16px',
    'width': '120px',
    'height': '40px',
  },
  'large': {
    'font-size': '20px',
    'width': '160px',
    'height': '50px',
  },
}

/* Button.tsx */
const BaseButton = styled.button.attrs<ButtonAttrsProps>((props) => ({
    type: props.type || 'submit',
    size: props.size || 'medium',
    disabled: props.disabled || false,
}))<ButtonProps>`
  font-size: ${props => buttonSize[props.size || 'medium']['font-size']};
  width: ${buttonSize[props.size || 'medium']['width']};
  height: ${buttonSize[props.size || 'medium']['height']};
`
```

<br/>

### 버튼 스타일, 칼라 만들기
\- 스타일로는 배경과 테두리에 색상을 넣는 두가지 형태를 사용하기로 생각했습니다.   
\- 또한 색상을 5가지정도 정해서 정해진 색상을 나오도록 하였습니다.   
버튼 종류 색상 5가지   
  - `blue: #3B76CB`
  - `green: #487B3B` 
  - `orange: #FD974F`
  - `red: #C0423B`
  - `purple: #8E35AA`

1. 첫번째 색상에 맞게 `background-color` 지정하기 
```typescript
const BaseButton = styled.button.attrs<ButtonAttrsProps>((props) => ({
  type: props.type || 'submit',
  size: props.size || 'medium',
}))<ButtonProps>`
  ${(props) => (props.color === 'blue' || !props.color) && css`
    background-color: #3B76CB;
  `}
    ${(props) => (props.color === 'green') && css`
    background-color: #487B3B;
  `}
  /* 나머지 부분은 중복이여서 생략 */
  {....}
`
```

<br/><br/>

2. 두번쨰 스타일에 맞게 `filled`와 `outline`을 만들려고하는데 써야 하는 칼라 부분이 `background-color`랑 `border`로 나뉘는 것으로 인해 문제 발생.   
\- 위에 방식으로는 너무 많은 분할이 생겨날꺼 같아서 다른 방법이 없나 생각을 해보다가  
\- 객채을 만들어 보자 해서 객체를 따로 빼서 만들어 봤습니다.
```typescript
// buttonData.tsx
export const buttonColor = {
  'blue': '#3B76CB',
  'green': '#487B3B',
  'orange': '#FD974F',
  'red': '#C0423B',
  'purple': '#8E35AA',
}

import styled, { css } from 'styled-components'
import { buttonColor } from './buttonData';

const BaseButton = styled.button.attrs<ButtonAttrsProps>((props) => ({
    type: props.type || 'submit',
    size: props.size || 'medium',
}))<ButtonProps>`
  ${(props) => (props.variant === 'filled' || !props.variant) && css`
      border-radius: 5px;
      background-color: ${buttonColor[props.bgcolor || 'blue']};
      color: #ffffff;
  `}
  ${(props) => props.variant === 'outline' && css`
      border-radius: 5px;
      border: 3px solid ${buttonColor[props.bgcolor || 'blue']};
      /* 테두리에 색상을 넣기에 기본적인 배경은 흰색으로 지정 */
      background-color: #ffffff;
      /* 글자는 해당 글자와 같도록 지정 */
      color: ${buttonColor[props.bgcolor || 'blue']};
  `}
`
```


### 기록 최종본

1. buttonData.tsx : 버튼에 쓰이는 데이터 모음
``` typescript
export const buttonSize = {
  'small': {
    'font-size': '12px',
    'width': '80px',
    'height': '30px',
  },
  'medium': {
    'font-size': '16px',
    'width': '120px',
    'height': '40px',
  },
  'large': {
    'font-size': '20px',
    'width': '160px',
    'height': '50px',
  },
}

export const buttonColor = {
  'blue': '#3B76CB',
  'green': '#487B3B',
  'orange': '#FD974F',
  'red': '#C0423B',
  'purple': '#8E35AA',
}

```
<br/>

```typescript
// 버튼 타입
export interface ButtonAttrsProps {
  size?: 'small' | 'medium' | 'large';
  type?: 'submit' | 'reset' | 'button';
  disabled?: boolean;
}

export interface ButtonProps extends ButtonAttrsProps {
  children?: ReactNode;
  bgcolor?: 'blue' | 'green' | 'orange' | 'red' | 'purple';
  variant?: 'outline' | 'filled';
}
```

<br/>

3. 버튼 컴포넌트
```typescript

import styled, { css } from 'styled-components'
import { buttonColor } from './buttonData';
import { ButtonProps, ButtonAttrsProps } from '../types/buttonTypes'

const BaseButton = styled.button.attrs<ButtonAttrsProps>((props) => ({
    type: props.type || 'submit',
    size: props.size || 'medium',
    disabled: props.disabled || false,
}))<ButtonProps>`
  /* 공통부분 */
  text-align: center;
  font-weight: 580;
  padding: 8px;

  /* 사이즈에 따른 부분 */
  font-size: ${props => buttonSize[props.size || 'medium']['font-size']};
  width: ${buttonSize[props.size || 'medium']['width']};
  height: ${buttonSize[props.size || 'medium']['height']};
  /* 스타일에 따른 색상 부분 */
  ${(props) => (props.variant === 'filled' || !props.variant) && css`
      border-radius: 5px;
      background-color: ${buttonColor[props.bgcolor || 'blue']};
      color: #ffffff;
  `}
  ${(props) => props.variant === 'outline' && css`
      border-radius: 5px;
      border: 3px solid ${buttonColor[props.bgcolor || 'blue']};
      /* 테두리에 색상을 넣기에 기본적인 배경은 흰색으로 지정 */
      background-color: #ffffff;
      /* 글자는 해당 글자와 같도록 지정 */
      color: ${buttonColor[props.bgcolor || 'blue']};
  `}
`

const Button = (props: ButtonProps) => {
  return (
    <BaseButton type={props.type} size={props.size} disabled={props.disabled} variant={props.variant} bgcolor={props.bgcolor}>
      {props.children}
    </BaseButton>
  )
}

export default Button;
```

[@Icon Button React 추가하기](./20230410-react-custom-components-iconbutton.md)