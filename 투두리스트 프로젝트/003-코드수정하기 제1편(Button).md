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

### Button 사용처

- Home화면 회원가입, 로그인 버튼
- 회원가입, 로그인화면 API 전달 버튼
- 로그아웃 버튼
- 입력창 표시해주는 버튼
- Todo 수정, 삭제, 제출, 취소 버튼

### Button Icon

- react-icons 라이브러리를 삭제하고 svg를 활용 하려고 했지만.
- todoList 추가 부분에서 추가 생길때 마다 생성되어 추가 부분만 SVG sprite로 변경

### Button 종류 및 Props

일반적인 Button과 icon이 포함된 IconButton으로 종류를 나누었습니다.

|Props 종류|내용|
|:---:|:---:|
|$size|`'basic'\|'large'\|'circle'\|'mini'\|'logout'`|
|$btnType|`'primary'\|'dismiss'\|'sub'`|
|$direction|`'up'\|'down'\|'left'\|'right'`|
|$isIconOfText|`'ok'\|'no'`|

|Icon Props 종류|내용|
|:---:|:---:|
|iconFill|string|
|iconName|`'btn-edit'\|'btn-delete'\|'btn-send'\|'btn-send-cancel'\|'btn-uparrow'\|'btn-close'\|'checkbox'\|'uncheckbox'`|
|iconWidth|string|
|iconHeight|string|

### Button 태그 자체적인 속성 적용

- styled-components로 스타일을 적용하여 컴포넌트로 만들었지만 **button** 이 자체적으로 가지고 있는 속성을 사용 못함.
- 하지만 전 interface 처럼 넣어주는 것은 필요할 때마다 수정해야 하여 별로 좋아 보이지 않아서 새로운 방법을 찾아봄.
- `extends ButtonHTMLAttributes<HTMLButtonElement>` React가 가지고 있는 button 속성 interfcae 적용
- 스타일적으로 사용할 props는 따로 선언하고 button 자체 속성 props는 spread 연산자를 사용하여 적용

  ```typescript
  interface ButtonStyleProps extends ButtonHTMLAttributes<HTMLButtonElement>

  export const Button: FC<ButtonStyleProps> = ({$size, $btnType, $direction, ...props}) => {
    // .. 반환 JSX
    <ButtonStyle $isIcon={"no"} $size={$size} $btnType={$btnType} $direction={$direction} {...props}>
  }
  ```

### Button 조건에 따른 스타일 적용

- 역활에 따른 색상 지정
- 위치에 따른 크기 지정

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
    `,
    sub: css`
      background-color: #bfbfff;
    `,
  };

  const btnSize = {
    basic: css`
      /* 생략 */
    `,
    large: css`
      /* 생략 */
    `,
    circle: css`
      /* 생략 */
    `,
    mini: css`
      /* 생략 */
    `,
    logout: css`
      /* 생략 */
    `,
  }
  ```

## Button 및 IconButton 코드 일부분

**기본 버튼 코드**

```typescript
export const Button: FC<ButtonStyleProps> = ({
  $size,
  $isIconOfText = 'no',
  $btnType,
  ...props
}) => {
  return (
    <ButtonStyle $isIconOfText={$isIconOfText} $size={$size} $btnType={$btnType} {...props}>
      {props.children}
    </ButtonStyle>
  );
};
```

**아이콘 버튼 코드**

```typescript
export const IconButton: FC<IconStyleProps> = ({
  $size,
  $btnType,
  $isIconOfText = 'ok',
  $direction,
  iconName,
  iconFill,
  iconWidth,
  iconHeight,
  ...props
}) => {
  return (
    <ButtonStyle
      $isIconOfText={$isIconOfText}
      $direction={$direction}
      $size={$size}
      $btnType={$btnType}
      {...props}>
      <SvgIcon
        aria-label={props['aria-label']}
        iconName={iconName}
        $direction={$direction || 'down'}
        width={iconWidth}
        height={iconHeight}
        fill={iconFill}
      />
      {props.children}
    </ButtonStyle>
  );
};
```
