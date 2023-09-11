# Button 코드를 수정하자

## 문제점 찾기

- 코드가 너무 복잡해 보여서 코드 읽기가 쉽지 않다.
- react-icons사용으로 불필요한 라이브러리를 사용
- 이미지 색상이 Lighthouse로 체크 해보았을 때 문제 발생.
- Home 경우 버튼 크기가 너무 작아서 공간이 많이 남는 문제.

<div align="center">
  <img alt="button lighthouse" src="https://github.com/codingjwp/mindpalace/assets/113403155/f1d6d7bf-902b-412e-b378-6c5ccd209048" width=500 />
</div>

## 문제점 해결 방안

- 코드 정리
- 사용할 icon의 svg를 사용하는가 아님 스포라이트 이미지 사용을 생각
- 전체적인 밝은 계통과 비슷하게 버튼 배경색이 밝아 더 진하게 바꾸거나 글자를 검은색으로 변경필요.

## Button 만들기 시작

### Button 사용처

- Home화면 회원가입, 로그인 버튼
- 회원가입, 로그인화면 API 전달 버튼
- 입력창 표시해주는 버튼
- Todo 수정, 삭제, 제출, 취소 버튼

### Button props

  |Props(스타일)|내용 정리|
  |:---:|---|
  |$size|"basic", "large", "circle", "mini" <br> 사이즈에 따른 변경 옵션 `width`, `height`, `border-radius` |
  |$btnType|"primary","dismiss", "sub|
  |$direction|boolean|
  |$isIconOfText|"ok","no"|

  |Props(속성)|내용 정리|
  |:---:|---|
  |aria-label| vitest에 사용 및 어디에 쓰는지 판별용|
  |type| "submit", "button", "reset" |
  |disable| 회원가입 및 로그인에 Form 입력이 여부 |

### Button Icon

- react-icons 라이브러리를 삭제하고 svg를 활용 하려고 했지만.
- todoList 추가 부분에서 추가 생길때 마다 생성되어 추가 부분만 image sprite로 변경

## Button 수정 코드

### 이전 Button interface

```typescript
interface ButtonProps {
  testname?: string;
  type: 'submit' | 'edit' | 'delete' | 'plus';
  open?: boolean;
  name?: string;
  ariaLabel?: string;
  size?: 'sm' | 'md' | 'lr';
  disabled?: boolean;
  onClick?: (e: MouseEvent<HTMLButtonElement>) => void;
  children?: ReactNode;
}
```

### 변경후 Button interface

- 앞에 `$` 붙인 이유는 styled-components에서 `$`가 붙지 않은 **props** 경우 Dom 요소로 판단을 하여 아닌 경우 style로 넘어 온다고 하여 구분을 위해 적용
- **$size**에 `mini` 추가, **$btnType**에 `sub` 추가, **$direction**에 `left`, `right` 추가

```typescript
interface ButtonStyleProps {
  $size: "basic" | "large" | "circle" | "mini";
  $btnType: "primary" | "dismiss" | "sub";
  $direction?: boolean | "left" | "right";
  $isIconOfText?: "ok" | "no";
}
```

### Button 태그 자체적인 속성 적용

- styled-components로 스타일을 적용하여 컴포넌트로 만들었지만 **button** 이 자체적으로 가지고 있는 속성을 사용 못함.
- 하지만 전 interface 처럼 넣어주는 것은 필요할 때마다 수정해야 하여 별로 좋아 보이지 않아서 새로운 방법을 찾아봄.
- **`extends ButtonHTMLAttributes<HTMLButtonElement>`** React가 가지고 있는 button 속성 interfcae 적용
- 스타일적으로 사용할 props는 따로 선언하고 button 자체 속성 props는 spread 연산자를 사용하여 적용

  ```typescript
  interface ButtonStyleProps extends ButtonHTMLAttributes<HTMLButtonElement>

  export const Button: FC<ButtonStyleProps> = ({$size, $btnType, $direction, ...props}) => {
    // .. 반환 JSX
    <ButtonStyle $isIcon={"no"} $size={$size} $btnType={$btnType} $direction={$direction} {...props}>
  }
  ```

### Button 조건에 따른 스타일 적용

- **Before**

  ```CSS
  const ButtonStyle = styled.button<ButtonProps>`
    /* ... 생략 */
    background-color: ${props => {
      if (props.disabled) return theme.disabledSubmit;
      else {
        if (props.type === 'submit' || props.type === 'edit') {
          return theme.submit;
        } else return theme.delete;
      }
    }};

    &:hover {
      background-color: ${props => {
        if (!props.disabled) {
          if (props.type === 'submit' || props.type === 'edit') {
            return theme.hoverSubmit;
          } else return theme.hoverDelete;
        }
      }};
    }

    cursor: pointer;
  `;
  ```

- **After**
  - 특정 조건에 따른 style 경우 별도의 객체로 만들어서 관리하여 스타일 부분에 코드가 난잡하지 않게 수정
  - disabled 상태 일떄는 hover 작동하지 않도록 수정
  - filter 사용시 GPU를 사용하여 CPU와 에모리에 영향을 줄 수 있어 변경

  ```typescript
  const btnTheme = {
    "primary": css`
      background-color: #2929FF;
      &:not(:disabled):hover {
        background-color: #0000C6;
      }
    `,
    "dismiss": css`
      background-color: #B31010;
      &:not(:disabled):hover {
        background-color: #840C0C;
      }
    `
  };

  const btnSize = {
    "basic": css`
      width: 3.5rem;
      height: 2rem;
      border-radius: .325rem;
      font-size: .8rem;
    `,
    "large": css`
      width: 100%;
      height: 3rem;
      border-radius: .625rem;
      font-size: 1.2rem;
    `,
    "circle": css`
      position: fixed;
      left: 50%;
      bottom: -2rem;
      transition: .5s;
      width: 4rem;
      height: 4rem;
      border-radius: 50%;
      transform: translateX(-50%);
      z-index: 2;
    `,
  }
  ```

  - disabled에 따른 opacity 적용

  ```CSS
  const ButtonStyle = styled.button<ButtonStyleProps>`
    cursor: pointer;
    color: #ffffff;
    &:disabled {
      opacity: 0.7;
    }
    ${({$size}) => btnSize[$size]};
    ${({$btnType}) => btnTheme[$btnType]};
    ${({$isIconOfText}) => $isIconOfText === "ok" && css`
      display: flex;
      flex-direction: row;
      justify-content: center;
      align-items: center;
      & > svg {
        margin-right: .2rem;
      }
    `}
    left: ${({$direction}) => $direction === 'left' && '-1.5rem'};
    right: ${({$direction}) => $direction === 'right' && '-1.5rem'};
  `
  ```

## 아이콘 사용한 버튼 IconButton 컴포넌트 추가

### IconButton에 대한 interface

- extends로 **ButtonStyleProps**을 가져오기
- [SvgIcon 컴포넌트 생성](./04-코드수정하기%20제2편(SVG%20Sprite).md)
  |props|내용|
  |:---:|---|
  |iconName|SVG sprite에 쓰이는 id|
  |iconFill|색상|
  |iconWidth|넓이|
  |iconHeight|높이|

### IconButton 코드

- **ButtonStyle** 재사용
- 글자 있는 형태와 없는 형태의 스타일 다르게 하기 위해 **$isIconOfText** 사용

```typescript
export const IconButton: FC<IconStyleProps> = ({$size, $btnType, $isIconOfText="ok", $direction, iconName, iconFill, iconWidth, iconHeight, ...props}) => {
  return (
    <ButtonStyle $isIconOfText={$isIconOfText} $direction={$direction} $size={$size} $btnType={$btnType} {...props}>
      <SvgIcon aria-label={props['aria-label']} iconName={iconName} $direction={$direction || 'down'} width={iconWidth} height={iconHeight}  fill={iconFill} />
      {props.children}
    </ButtonStyle>
  )
}
```
