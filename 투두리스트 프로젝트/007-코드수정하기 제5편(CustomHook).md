# CustomHook 수정 및 생성하기

## useValidation 생성

- 이메일과 비밀번소 유효성 검사를 하기위한 Hook

## useValidation 매개변수 및 리턴 값

### 이전 Hook

- 매개변수
  - email: email 입력 값
  - pawword: 패스워등 입력 값
- 리턴 값
  - emailStatus
    - message: 경고 메세지
    - status: 유효청 체크
  - passwordStatus
    - message: 경고 메세지
    - status: 유효청 체크

### 변겅 Hook

- 매개 변수(빈 값)
- 리턴 값
  - isEmail: email 유효성 검사 여부
  - isPassword: password 유효성 검사 여부
  - isDisable : 버튼 활성화 여부
  - changeEmailData : email onChage 함수
  - changePasswordData : password onChage 함수

## useValidation 변경 코드

- **EMAIL_CHECK**로 이메일 정규식 적용
- **chnage** 함수들의 **useCallback**을 이용하여 `isEmail`, `isPassword`가 변경 될때만 콜백 함수를 생성하고 그외에는 참조를 유지
- `isDisable`는 **useMemo**를 사용하여 `isEmail`, `isPassword`를 사용하여 두 값이 변경될 떄만 적용하도록 수정
- 일반적인 **Message**를 전달하는게 아닌 `isEmail`, `isPassword`로 true 값만 보내서 경고 메시지만 스타일을 변경 하도록 수정

### 변경 전 onChage 부분

```typescript
const onChangeEmailHandler = (e: React.ChangeEvent<HTMLInputElement>) => {
  setFormData({
    ...formData,
    email: e.target.value,
  });
};
```

### 변경 후 코드

```typescript
export const useValidation = () =>{
  const EMAIL_CHECK = /^[a-zA-Z0-9+-_.]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$/;
  const [isEmail, setIsEmail] = useState(false);
  const [isPassword, setIsPassword] = useState(false);
  const isDisable = useMemo(() => {
    return !(isEmail && isPassword);
  }, [isEmail, isPassword]) 

  const changeEmailData = useCallback((e: ChangeEvent<HTMLInputElement>) => {
    const check = EMAIL_CHECK.test(e.currentTarget.value);
    if (check) setIsEmail(true);
    else if (!check && isEmail) setIsEmail(false); 
  }, [isEmail]);
  
  const changePasswordData = useCallback((e: ChangeEvent<HTMLInputElement>) => {
    const check = e.currentTarget.value.length > 7;
    if (check) setIsPassword(true);
    else if (!check && isPassword) setIsPassword(false);
  }, [isPassword]);

  return [isEmail, isPassword, isDisable, changeEmailData, changePasswordData] as const
}
```

- 이메일 목록이 존재 함에 따라 UI가 위치가 변경되어 **opacity**를 **0**로 줘서 UI공간을 차지하도록 수정

```CSS
const SignInOfUpWarring = styled.p<{$visible?: boolean, $position?: string}>`
  font-weight: 540;
  opacity: ${({$visible}) => $visible === true && 0 };
  /* ... 생략*/
`
```

## useTodoEvent 생성

- todo 목록과 페이지에 따른 다른 todo 표시를 위한 훅

### useTodoEvent 리턴 값

- 리턴 값
  - **`todoData`** : Todo 목록
  - **`length`** : 총 Todo 목록
  - **`setTodoData`** : Todo setState
  - **`setPages`** : 현재 페이지 setState

### useTodoEvent 코드

- 처음 조회되는 Todo 목록 가져오기 `getTodoListAPI` 호출
- 페이지마다 할일 9개 씩 출력 `todoData: todoData.slice(pages * 9, (pages + 1) * 9)`
- todo 내용 수정과, 페이지 수정을 위한 `setState`

```typescript
// import 생략

export interface TodoDataProps {
  id: string;
  isCompleted: boolean;
  todo: string;
  userId: string;
}

export interface TodoStateProps {
  setTodoData: Dispatch<SetStateAction<TodoDataProps[]>>;
}

export const useTodoEvent = () => {
  const [todoData, setTodoData] = useState<TodoDataProps[]>([]);
  const length = todoData.length;
  const [pages, setPages] = useState(0);
  const { setModalData } = useModalState();

  const getTodoListApi = async () => {
    const res = await getTodoList();
    if (res.status >= 400)
      setModalData({ modalOpen: true, modalType: "error", modalMsg: res.data});
    else 
      setTodoData(res.data);
  }

  useEffect(() => {
    getTodoListApi();
  }, [])

  return { todoData: todoData.slice(pages * 9, (pages + 1) * 9), length, setTodoData, setPages };
}
```
