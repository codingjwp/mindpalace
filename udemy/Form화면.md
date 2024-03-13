# Form 화면

사용자 제출을 위한 from element 적용 방식

## Form 입력 양식이 힘든 이유

form은 사용자가 입력한 값을 추출하는 작업과 제공한 데이터를 검증하는 작업 두가지로 되어있습니다.

데이터 추출 부분은 어렵지 않습니다. 하지만 검증 부분은 여러 방법이 있으면 사용자 경험 부분에서 절충안을 찾기가 힘듭니다.

- 입력때 마다 에러 확인
- 양식을 제출때 마다 에러 확인

### 기본적인 양식 예제 코드

```javascript
<form>
  <h2>Login</h2>

  <div className="control-row">
    <div className="control no-margin">
      <label htmlFor="email">Email</label>
      <input id="email" type="email" name="email" />
    </div>

    <div className="control no-margin">
      <label htmlFor="password">Password</label>
      <input id="password" type="password" name="password" />
    </div>
  </div>

  <p className="form-actions">
    <button className="button button-flat">Reset</button>
    <button className="button">Login</button>
  </p>
</form>
```

### `useState`를 사용한 양식 값 추출

```javascript
import { useState } from "react";

export default function Login() {
  const [enteredValue, setEnteredValue] = useState({
    email: '', password: ''
  })

  // type="submit" 버튼 클릭 시 
  function handleSubmit(event) {
    event.preventDefault();  
  }

  function handleEnterChange(identifier, value) {
    setEnteredValue(prev => ({
      ...prev,
      [identifier]: value
    }))
  }

  return (
    <form onSubmit={handleSubmit}>
      {/* .. 생략 */}
      <div className="control-row">
        <div className="control no-margin">
          <label htmlFor="email">Email</label>
          <input id="email" type="email" name="email" value={enteredValue.email} onChange={(event) => handleEnterChange('email', event.target.value)} />
        </div>

        <div className="control no-margin">
          <label htmlFor="password">Password</label>
          <input id="password" type="password" name="password" value={enteredValue.password} onChange={(event) => handleEnterChange('password', event.target.value)} />
        </div>
      </div>
      {/* .. 생략 */}
    </form>
  );
}
```

### `useRef`를 이용한 양식 값 추출

```javascript
import { useRef } from 'react';

export default function Login() {
  const emailRef = useRef();
  const passwordRef = useRef();
  function handleSubmit(event) {
    event.preventDefault();
    const enteredEmail = emailRef.current.value;
    const enteredPassword = passwordRef.current.value;
  }

  return (
    <form onSubmit={handleSubmit}>
      {/* .. 생략 */}
      <input id="email" type="email" name="email" ref={emailRef} />
      <input id="password" type="password" name="password" ref={passwordRef} />
      {/* .. 생략 */}
    </form>
  );
}

```

### FormData를 이용한 제출 방식

복잡한 양식 경우 사용하기 좋습니다.

다수의 양식을 가져오기 위해 작성한 예시 코드입니다.  
`Object.fromEntries(fd.entries());` 를 통해 배열을 object 로 변경합니다

```javascript
function handleSubmit(event) {
  event.preventDefault();
  // 다중 입력시 entries 메서드로는 가져올 수 없기 때문에 따로 작성
  const acquisitionChannel = fd.getAll('acquisition');
  const fd = new FormData(event.target);
  const data = Object.fromEntries(fd.entries());
  data.acquisition = acquisitionChannel;
}
```

### Form 초기화

버튼을 클릭하여 `form` 리셋하는 방법

```javascript
<button type="reset">reset</button>
```

onSubmit 이벤트를 사용하여 리셋하는 방법

```javascript
function handleSubmit(event) {
  event.target.reset();
}
```

## 유효성 검사

입력 기반 유효성 검증과 폼 제출형 유효성 검증은 같이 사용하는게 좋습니다.

입력 기반 유효성 검증

```javascript
  const [enteredValue, setEnteredValue] = useState({
    email: '',
    password: ''
  })
  const [didEdit, setDidEdit] = useState({
    email: false,
    password: false
  })
  const emailIsInvalid = didEdit.email && !enteredValue.email.includes('@');

  function handleInputBlur(identifier) {
    setDidEdit(prev => ({
      ...prev,
      [identifier]: true
    }))
  }
  function handleEnterChange(identifier, value) {
    setEnteredValue(prev => ({
      ...prev,
      [identifier]: value
    }))
    setDidEdit(prev => ({
      ...prev,
      [identifier]: false
    }))
  }

  reutnr (
    <input id="email" type="email" name="email" value={enteredValue.email} 
      onBlur={() => handleInputBlur('email')}
      onChange={(event) => handleEnterChange('email', event.target.value)} />
  )
```

내장된 기능을 이용한 검증은 `<input required/>` 를 이용해 타입에 맞는 기능을 사용하며 아래 코드는 비밀번호 비교 시 추가적으로 검증하는 코드 입니다.

```javascript
  const [passwordsAreNotEqual, setPasswrodAreNotEqual] = useState(false);

  function handleSubmit(event) {
    event.preventDefault();
    // 다중 입력시 entries 메서드로는 가져올 수 없기 때문에 따로 작성
    const acquisitionChannel = fd.getAll('acquisition');
    const fd = new FormData(event.target);
    const data = Object.fromEntries(fd.entries());
    data.acquisition = acquisitionChannel;
    if (data.password !== data['confirm-password']) {
      setPasswrodAreNotEqual(true);
      return
    }
  }
```