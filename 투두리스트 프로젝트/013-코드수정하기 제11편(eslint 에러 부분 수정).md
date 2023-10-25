# eslint 부분 에러 수정 하기

## no-ignore waring이 발생하는 부분

- 검사가 필요하지 않는 부분을 .eslintignore 에 넣어도 waring이 뜨는 경우가 계속 생겨 없앨 수 있는 방법을 찾아 보았습니다.
- 물론 `--quiet`으로 eslint 하는 옵션으로 경고를 없애는 옵션이 있지만 해당 부분은 기능적인 경고가 있을 경우에도 안보이기 때문에 별로 좋지 않는 옵션으로 판단했습니다.
- 찾아보니 **eslint --cache --ext .ts,.tsx src/\*\*/*.{ts, tsx}** 으로 **src/\*\*/*.{ts, tsx}** 뒤에 ** 넣는 경우 경고를 생성 한다고 합니다.
- 그래서 `--quiet` 쓰는 방법 만 있으며 아니면 **eslint .** 방식으로 변경 해야 한다고 하여 검사 명령어를 변경 하였습니다.

```bash
# Before command 
eslint --cache --ext .ts,.tsx src/\*\*/*.{ts, tsx}
# After command
eslint --cache --ext .ts,.tsx src/
```

## test.tsx 파일에 error 나오는 문제

- 해당 파일은 에러 체크를 하지 말아야하는데 체크가 되어서 체크 안되도록 수정
- `.eslintignore`에 아래 문구 추가

```json
# Test Files
src/**/*.test.tsx
```

![test파일](https://github.com/codingjwp/mindpalace/assets/113403155/cc2a2ae7-7995-4c80-a353-ccb71eba3558)

## useValidation 에서 경고 발생

- 의존성 배열로 인한 `EMAIL_CHECK`가 경고 문구 생성
- 아래 코드 처럼 되어 있던 EMAIL_CHECK의 위치를 아래의 의존성 배열이 있는 곳에다 선언

```typescript
export const useValidation = () =>{
  const EMAIL_CHECK = /^[a-zA-Z0-9+-_.]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$/;
  // .... 생략
}
```

```typescript
const changeEmailData = useCallback((e: ChangeEvent<HTMLInputElement>) => {
  const EMAIL_CHECK = /^[a-zA-Z0-9+-_.]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$/;
  const check = EMAIL_CHECK.test(e.currentTarget.value);
  if (check) setIsEmail(true);
  else if (!check && isEmail) setIsEmail(false); 
}, [isEmail]);
```

![useValidation경고](https://github.com/codingjwp/mindpalace/assets/113403155/ccbb6a9d-f9dd-485d-b269-6716059360cc)

## Home 부분에 react-refresh 부분에서 경고 발생

- 컴포넌트 부분에 컴포넌트 외 것 이 선언되어 있을 경우 react-refresh가 되지 않아 경고 발생.
- 아래 코드를 style 부부을 따로 styles.tsx 파일로 만들고 import 하고 memo부분을 삭제합니다.

```typescript
// Home.styles.tsx
import styled from 'styled-components'

export const HomeTitle = styled.h1`
  text-align: center;
  margin-bottom: 40%;
  font-size: 3rem;
`;

export const HomeContainer = styled.div`
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;
`;
```

![home경고](https://github.com/codingjwp/mindpalace/assets/113403155/1eab55ef-bae7-4e96-9958-57065f4e90ed)

## btnicon.svg 에러 발생

- 읽을 수 있는 파일이 아닌데 체크가 되어 `.eslintignore`에 아래 문구 추가
![btniconSVG에러](https://github.com/codingjwp/mindpalace/assets/113403155/581e5dd7-f096-4cf1-8543-d1d25a42dd78)

## ModalContext hooks 부분 경고 발생

- 컴포넌트 부분에 컴포넌트 외 것 이 선언되어 있을 경우 react-refresh가 되지 않아 경고 발생.
![modalcontext경고](https://github.com/codingjwp/mindpalace/assets/113403155/9b718542-30a6-41f0-aeac-839e404f2aa0)

## Axios에 반환대는 값들을 전부 any 되어 있어서 에러 발생

- get, put, post, delete 전부 <type>에 맞는 타입을 지정해줌
- 처음에는 interface로 {state: number, data: TodoDataProps}로 사용 헀는데
- 받은 데이터를 setState로 넣을떄 타입이 맞지 않다고 하여 보니 <타입>으로 주는게 data에 할당하는 것이여서 거기에 맞춰서 다시 변경

```typescript
// 변경한 타입
todoAxios.put<TodoDataProps>
```

![todoAxion경고](https://github.com/codingjwp/mindpalace/assets/113403155/0798a23e-b4fa-4403-a80d-503da4f4a30f)

## error  Promise-returning function provided to attribute where a void return was expected  @typescript-eslint/no-misused-promises

- form 태그의 onSumbit에 `Promise<void>`를 반환하는 비동기 함수를 넣으니 발생 하는 에러입니다.
- 처음에는 `Promise<void>`를 명확히 지정안 해줘서 그런가 싶어서 아래 코드 처럼 했으나 그대로 였습니다.

```typescript
const postTodoCreateApi = (e: FormEvent): Promise<void> => { }
```

- 그래서 async와 await를 써서는 수정 하는 방법이 없어 보여 해당 부분을 함수에서 지웠습니다.
- 해당 부분을 지우고 then을 이용하여 처리하는 방법과 catch를 error을 지정하여 하였으나.
- `error  Invalid type "any" of template literal expression  @typescript-eslint/restrict-template-expressions` 가 발생하였습니다.
- 하지만 해당 에러에 정확히 들어가는 경우를 알 수 없어서 방법을 찾는중 `String(error)`을 사용하여 명시적으로 문자열로 바꿔주면 에러가 사라지는 걸 확인 하였습니다.
- 나머지 똑같은 에러가 나는 곳도 수정.

```typescript
  const postTodoCreateApi = (e: FormEvent) => {
    e.preventDefault();
    const form = (e.target as HTMLFormElement);
    const formData = new FormData(form);
    const todoDetail = formData.get('create');
    createTodoList(todoDetail as string).then((res) => {
      if (res.status >= 400) {
        setModalData({
          modalOpen: true,
          modalType: "error",
          modalMsg: res.data as string,
        })
      } else {
        setTodoData((prev) => [res.data as TodoDataProps, ...prev]);
      }
    }).catch((error) => {
      throw new Error(`에러 발생 ${String(error)}`);
    });
    form.reset();
  }
```
