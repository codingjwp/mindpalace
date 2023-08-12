# 회원가입과 로그인 페이지를 수정하자!
## 문제점 찾기
- **SignIn**, **SignUp** 페이지 모양이 같은 형태지만 다른 페이지를 사용
  - 하지만 안에 **API** **Title** **Buuton** 내용이 틀림
- inputField에 label을 썻으나 htmlFor이나 label로 input을 감싸지 않아 웹 접근성 위배
- **유효성 검사 경고** 부분 색상 **Ligthouse** 검사
- button 색상 **Lighthouse**에서 검사
- **onChage**에서 사용한 **setState**로 인한 많은 **re-rendering**
<div align="center">
  <img src="https://github.com/codingjwp/mindpalace/assets/113403155/6c7dfcb4-7029-4b51-bf72-eed2dc4098ce" width="600px" alt="SignIn 페이지 Lighthouse 검사" />
</div>

## 문제점 수정방법
- **Signin**, **SiginUp** 페이지 공통 컴포넌트 사용 및 props로 타이틀, 버튼, API쓸 수있게 전달
  - **customHook** 생성
- **InputFiled**, **label** 감싸 놓은 상태로 수정
- **유효성 검사 경고** 부분 색상 변경
- **Button**도 색상 수정

## SignInOfUp 만들기 시작
### SignInOfUp props
|props|내용 정리|
|:---:|---|
|title|`button`, `API`, `Title`에 사용|

### SignInOfUp 수정 코드
- [customHook 생성 및 input의 onChange 부분](./05.sub-유효성%20검사를%20위한%20CustomHook%20수정.md)
- `SignIn`, `SignUp`이 하나의 페이지로 합쳐저서 **API URL**를 구분 할 방법을 변경
  - **SignInOfUp**이 전달 받는 `props`의 **titles**를 수정하여 `path`로 전달
```typescript
  const PostSignInofUpApi = (e: FormEvent) => {
    e.preventDefault();
    const path = titles.replace(/ /g, "") .toLowerCase();
    // API 기능
  }
``` 

### validation 부분에서 오류 발생
- `Link`를 통해 **`signup, signin`** 경로로 페이지를 넘길때 **`form`**에 있는 **`input`** 및 **useValidation Hook** 에 써놓은 **input 글자가 남아 있는 것**과 **경고문을 표시**해주는 기능이 작동 하지 않는 문제가 있어서 수정하였습니다.
- input value 값 남아 있는 부분 수정 하기 (`titles` 값이 변경하는 것에 따라 `reset`처리)
```typescript
// SigInOfUp uesEffect 사용
  useEffect(() => {
    if (titles) {
      const form = document.querySelector('form');
      form?.reset();
    }
  }, [titles])
```
- `useValidation` 경고문 초기화 (매개변수 `titles` 넣어 `titles` 변경시 `false`값으로 변경)
```typescript

export const useValidation = (titles: string) => {
  useEffect(() => {
    if (titles) {
      setIsEmail(false);
      setIsPassword(false);
    }
  }, [titles])
```
