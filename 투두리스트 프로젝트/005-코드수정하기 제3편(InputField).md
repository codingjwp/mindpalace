# InputField 코드를 수정하자

## 문제점 찾기

- react-icons으로 불필요한 용량 차지
- 코드가 너무 복잡해 보여 코드가 읽기 힘듬
- 불필요한 inteface 요소 삭제

## 문제점 수정방법

- 코드 정리
- checkbox 기본을 사용 또는 SVG 스프라이트 사용
- interface 정리
- checkbox용 컴포넌트 따로 작성

## InputField 만들기 시작

### InputField 사용처

- `회원가입`에서 email, password 사용
- `로그인`에서 email, password 사용
- `Todo 수정` checkbox, text 할일 목록 마다 사용
- `Todo 추가` text 추가 부분에서 사용

### InputField Props

- 변경할 interface InputField에서 사용할 input type은 2개 `text`, `password`
- checkbox용으로 사용할 $ishidden

|Props|내용 정리|
|:---:|---|
|type|`text`, `password`|
|$ishidden|true,false|

- 이전 interface

  ```typescript
  interface InputProps {
    testname?: string;
    size?: 'md' | 'lr' | 'full';
    id?: string;
    type: HTMLInputTypeAttribute;
    name?: string;
    defaultChecked?: boolean;
    placeholder?: string;
    defaultValue?: string | number;
    onChange?: (e: ChangeEvent<HTMLInputElement>) => void;
  }
  ```

### InputField 수정 코드

- width, height는 고정적으로 사용으로 size를 정 할 필요가 없어서 삭제
- 나머지 경우 기본적으로 input에 있는 속성들로 **`InputHTMLAttributes<HTMLInputElement>>`** 로 타입을 가져와 **`props`** 로 속성 값을 가져오게 수정
- **type** 부분만 `text`, `password`로 두개만 사용
- **label**을 감싸 웹 접근성을 높힘
- 수정 전

  ```typescript
  export const InputField = forwardRef<HTMLInputElement, InputProps>((props, ref) => {
    if (props.type !== 'checkbox')
      return (
        <InputStyle
          data-testid={props.testname}
          size={props.size}
          id={props.id}
          type={props.type}
          name={props.name}
          defaultChecked={props.defaultChecked}
          placeholder={props.placeholder}
          defaultValue={props.defaultValue}
          onChange={props.onChange}
          ref={ref}
        />
      );
    else
      return (
        <label>
          <InputCheckBox
            data-testid={props.testname}
            size={props.size}
            id={props.id}
            type={props.type}
            name={props.name}
            defaultChecked={props.defaultChecked}
            placeholder={props.placeholder}
            defaultValue={props.defaultValue}
            onChange={props.onChange}
          />
          {props.defaultChecked ? (
            <Checked defaultChecked={props.defaultChecked}>
              <MdDone style={checkColor} />
            </Checked>
          ) : (
            <Checked />
          )}
        </label>
      );
  });
  ```

- 수정 후
- 따로 컴포넌트를 만들어서 `checkbox`만 사용 하도록 수정

  ```typescript
  interface InputFieldProps extends InputHTMLAttributes<HTMLInputElement>{
    type?: "text" | "password";
    $ishidden?: boolean;
  }

  export const InputField = forwardRef<HTMLInputElement, InputFieldProps>(({type, ...props}, ref) => {
      return (
        <InputLabel>
          <InputFieldBase ref={ref} type={type} {...props} />
        </InputLabel>
      );
  });

  export const CheckBoxField: FC<InputFieldProps> = ({type, isCompleted, ...props}) => {
    return (
      <InputLabel $check='check'>
        <InputFieldBase type='checkbox' $ishidden={true} defaultChecked={isCompleted} {...props}/>
        <SvgIcon iconName={isCompleted ? 'checkbox' : 'uncheckbox'} fill={isCompleted ? '#2929FF' : '#000000'} />
      </InputLabel>
    )
  } 
  ```
