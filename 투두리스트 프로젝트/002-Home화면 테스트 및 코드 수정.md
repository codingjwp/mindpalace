# 투두리스트 프로젝트

## Home test 코드

### Home.tsx 파일

```typescript
// import 부분 생략
const Home = () => {
  const navigate = useNavigate();
  const handleMoveClick = (e: MouseEvent<HTMLButtonElement>) => {
    navigate(e.currentTarget.name);
  };

  return (
    <>
      <HomeTitle>Todo</HomeTitle>
      <HomeContainer>
        <Button ariaLabel="signin" name='signin' type='submit' size='md' onClick={handleMoveClick}>
          SignIn
        </Button>
        <Button ariaLabel="signup" name='signup' type='submit' size='md' onClick={handleMoveClick}>
          SignUp
        </Button>
      </HomeContainer>
    </>
  );
};

// 스타일 부분 생략
export default Home;
```

### Home.test.tsx 테스트 확인

- SignUp 버튼 클릭 시 URL path /signup 이 맞는 지 확인
- SignIn 버튼 클릭 시 URL path /signIn 이 맞는 지 확인

```typescript
import { RouterProvider, createMemoryRouter} from 'react-router-dom';
import { render, screen} from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { it, describe } from 'vitest';
import { routerElement } from '../App'

describe('Home Page', () => {
  const user = userEvent.setup();
  it('Click SignIn button goes to /signin', async () => {
    const routers = createMemoryRouter(routerElement, {
      initialEntries: ['/']
    });
    render(<RouterProvider router={routers} />); 

    const signInBtn = screen.getByRole('button', {name: /signin/i});
    expect(signInBtn).toBeInTheDocument();
    await user.click(signInBtn);
    expect(routers.state.location.pathname).toEqual('/signin');
  });

  it('Click SignUp button goes to /signup', async () => {
    const routers = createMemoryRouter(routerElement, {
      initialEntries: ['/']
    });
    render(<RouterProvider router={routers} />);
     
    const signUpBtn = screen.getByRole('button', {name: /signup/i});
    expect(signUpBtn).toBeInTheDocument();
    await user.click(signUpBtn);
    expect(routers.state.location.pathname).toEqual('/signup');
  });
});
```

### Home.test.tsx 만들면서 힘들었던 부분 및 에러 부분

- 이동 후 path 이름을 어떻게 가져 오는 가
  - `routers.state.location.pathname` **createMemoryRouter** 의 값으로 `location.pathname` 가져와서 비교
- 중복 내용 하나로 합치는 부분에서 routers 부분에서 에러
  - 중복 되는 부분을 따로 위에 뺐으나 첫 번째 `it` 에서 `user.click`으로 인해 `/signup`으로 이동.
  - 두 번째 `it` 에서 **SignUp** 페이지 내용으로 테스트를 실행하여 filed 발생

  ```typescript
  const user = userEvent.setup();
  const routers = createMemoryRouter(routerElement, {
    initialEntries: ['/']
  });
  beforeEach(() => {
    render(<RouterProvider router={routers} />);
  })
  ```

  - 결국 **routers** 와 **render** 부분을 각각의 `it`에 넣어주는 수밖에 없었으며 `userEvent.setup()`만 밖에서 선언
