# 투두리스트 프로젝트

## 선택한 테스트 라이브러리

![Vitest](https://img.shields.io/badge/Vitest-0.33.0-20232A)
![jsdom](https://img.shields.io/badge/jsdom-22.1.0-28A7245)
![@testing-library/react](https://img.shields.io/badge/@testing--library/react-14.0.0-B73BFE?)
![@testing-library/user-event](https://img.shields.io/badge/@testing--library/jest--dom-5.17.0-CA4245)
![@testing-library/jest-dom](https://img.shields.io/badge/@testing--library/user--event-14.4.3-007ACC)

### 해당 라이브러리로 선택한 이유

1. **`Vitest`**
    - Jest가 테스트의 공식으로 자리 잡고 있으나 Jest 공식 문서 상 Jest는 vite에서 완전히 지원되지 않는다고 한다  [Jest using-vite Link](https://jestjs.io/docs/getting-started#using-vite)
    - Vite의 테스트 러너로 자리 잡고있는 Vitest를 선택
      - 하지만 마찬 가지로 아직 version 자체가 `0.33.0` 으로 완전하지는 않음
2. **`jsdom`**
    - `happy-dom` 둘 중 하나를 생각하고 있었으나 `happy-dom`에는 아직 일부 지원하지 않는 Web API가 존재하는 것으로 인해 `jsdom`을 선택
3. **`@testing-library`** (React Testing Library)
    - react : React 컴포넌트의 사용자 관점에서 동작과 접근성 테스트
    - user-event: 브라우저 이벤트 시뮬레이션
    - jest-dom: Dom요소에 대한 단언문

<br>

### 설치 및 설정 방법

1. 설치

    ```bash
    npm install -D vitest jsdom @testing-library/jest-dom @testing-library/react @testing-library/user-event
    ```

2. 설정
    - package.json
        - bail: 테스트 오류 횟수로 2번의 오류 발생 후 종료한다.

      ```json
      "scripts": {
        // ... 생략
        "test": "vitest --bail 2",
        "testui": "vitest --ui",
      },
      ```

    - vite.config.ts (triple slash를 사용하였습니다.)

      ```typescript
      import { defineConfig } from 'vite'
      import react from '@vitejs/plugin-react'

      export default defineConfig({
        plugins: [react()],
        test: {}
      });
      ```

    - `vite.config.ts`에 **`test`**를 작성하니 일어나는 에러.
      > No overload matches this call.  
      > Overload 1 of 3, '(config: UserConfig): UserConfig', gave the following error.
      > Argument of type '{ plugins: PluginOption[][]; test: {}; }' is not assignable to parameter of type 'UserConfig'.
      > Object literal may only specify known properties, and 'test' does not exist in type UserConfig'.
      > ... 생략
    - 왜?? [참조 링크](https://stackoverflow.com/questions/72146352/vitest-defineconfig-test-does-not-exist-in-type-userconfigexport)
      > Vite 구성 인터페이스는 Vitest에 대해 아무것도 알지 못하며 TS는 과도한 속성(유형/인터페이스에 정의되지 않은 속성)을 허용하지 않음
      > Vite 자체는 Vitest와 그 구성에 대해 아무것도 모르기 때문입니다. 따라서 Vitest는 Vite 구성(TS 인터페이스로 정의됨)을 확장이 필요.
    - 수정 방법

      ```typescript
      // 1. Vitest에서 확장한 Vite 구성 가져오기
      import { defineConfig } from 'vitest/config';
      // or
      // 2. triple slash 명령 사용
      /// <reference types="vitest" />
      import { defineConfig } from 'vite'
      ```

    - Options
      - `globals`: jest와 같은 전역 테스트 API 사용 [자세한 참조 내용](https://www.js2uix.com/frontend/jest-study-step2/)

        ```json
        //typescript는 tsconfig.json에 추가필요.
        "types": ["vitest/globals"],
        ```

      - `css`: Css 파일 처리 여부, `false`시 무시하고 `true` 같이 포함해서 처리
      - `setupFiles`: 설치 파일의 경로입니다. 각 테스트 파일 전에 실행됩니다.

        ```typescript
        // 전역 처리할 부분을 미리 설정하는 파일이라고 보면 된다.
        import '@testing-library/jest-dom';
        ```

      - `include`: 테스트 실행에 포함될 파일
      - `exclude`: 테스트 실행에 제외할 파일
      - `environment`: 테스트할 환경 설정 'jsdom' 사용
      - `typecheck`: 유형 검사 테스트 환경 구성
        - `checker`: `tsc` : typescript 구문 분석
        - `inclued`: 테스트 포함 파일
        - `exclude`: 테스트 제외 파일

      ```typescript
      /// <reference types="vitest" />
      import { defineConfig } from 'vite'
      import react from '@vitejs/plugin-react'

      export default defineConfig({
        plugins: [react()],
        test: {
          globals: true,
          css: true,
          setupFiles: './src/tests/setUpFiles.ts',
          include: ['./src/**/*.{test,spec}.{ts,tsx}'],
          exclude: [
            '**/node_modules/**',
            '**/public/**',
            '**/readmeImg/**',
            '**/.**',
            '**/*.{json,md,html}'
          ],
          environment: 'jsdom',
          typecheck: {
            checker: 'tsc',
            include: ['./src/**/*.{test,spec}.{ts,tsx}'],
            exclude: [
              '**/node_modules/**',
              '**/public/**',
              '**/readmeImg/**',
              '**/.**',
              '**/*.{json,md,html}'
            ],  
          }
        }
      });
      ```  

<br>

## 테스트를 위한 코드 작성

### Home Page 테스트용 코드 연습

- App 코드.

```typescript
//App.tsx 화면 Router.tsx를 테스트 하기 쉽게 App.tsx로 이동
// import 생략 
export const routerElement = [
  {
    path: '/',
    element: (
      <Layout>
        <Outlet />
      </Layout>),
    children: [
      { index: true, element: <Home /> },
      { path: 'signup', element: <SignUp /> },
      { path: 'signin', element: <SignIn /> },
  ]}
];

function App() {
  const routers = createBrowserRouter(routerElement);

  return (
    <>
      <GlobalStyles />
      <RouterProvider router={routers} />
    </>
  );
}

export default App;
```

- Home 코드

```typescript
//import .. 생략

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

const HomeTitle = styled.h1`
  text-align: center;
  margin-bottom: 100px;
`;
const HomeContainer = styled.div`
  display: flex;
  align-items: center;
  justify-content: center;
  & button:first-child {
    margin-right: 20px;
  }
`;

export default Home;
```

- 테스트 코드 App.test.tsx

```typescript
import { RouterProvider, createMemoryRouter} from 'react-router-dom';
import { render, screen} from '@testing-library/react';
import { it, describe } from 'vitest';
import { routerElement } from '../App'

describe('App Router', () => {
  it('render path "/" Home page', async () => {
    const routers = createMemoryRouter(routerElement, {
      initialEntries: ['/']
    });
    render(<RouterProvider router={routers} />);
    const heading = screen.getByRole('heading', {level:1});
    const signInBtn = screen.getByRole('button', {name: 'signin'});
    const signUpBtn = screen.getByRole('button', {name: 'signup'});

    expect(heading).toBeInTheDocument();
    expect(signInBtn).toBeInTheDocument();
    expect(signUpBtn).toBeInTheDocument();
  });
});
```

- 왜 **createBrowserRouter** 을 안쓰고 **createMemoryRouter** 을 쓰는 걸까??
  - **createMemoryRouter** 를 사용하여 테스트하는 이유는 메모리 기반 라우터가 브라우저 환경이 아닌 테스트 환경에서 더 적합하기 때문입니다
  - 브라우저 history를 직접 조작할 필요가 없어 테스트 환경에서 더 쉽게 관리할 수 있습니다.
  - 테스트 간에 공유되지 않는 독립적인 라우팅 상태를 가질 수 있습니다.
  - 초기 경로 및 경로 전환을 쉽게 설정할 수 있습니다.
  - 사용자의 실제 URL을 건드리지 않기 때문에 브라우저 환경과 무관하게 실행됩니다.

<br>

### 테스트 코드 작성하며 에러 났던 부분

- 처음 **heading** 변수에서 에러가 났는데 `screen.getByRole('h1')` 넣어서 에라가 났던것
  - `screen.getByRole('heading', {level: 1})`로 변환;
- `signInBtn` 변수에서 찾지 못하는 에러 발생. 옵션 `name`을 찾지 못해서 여러 개의 `button`을 발견해서 에러가 발생
  - `screen.getByRole('button', {name: 'signin'})`이 **button**의 속성인 `name`이 아니라 **`aria-label`** 속성이여야 찾을 수 있다는 걸 깨닫고 **Button 컴포넌트** 에 `aria-label` 추가
- **`ReferenceError: expect is not defined`** 에러 발생
  - **expect**뒤에 **toBeInTheDocument()** 인 **jest-dom**을 찾지 못해서 발생.
  - **jset-dom** 기능이 아닌 **vitest**의 자체적 기능이 있으나 **jest-dom** 기능이 많아서 같이 사용할 수 있게 설정을 할 수 있음.
  - 하지만 `setupFiles`로 설정을 해줘도 `globals`가 `true`가 아니면 찾지를 못해 에러 발생

    ```json
    // vite.config.ts에서 설정
    "globals": true,
    "setupFiles": "./src/tests/setUpFiles.ts",
    ```

    ```typescript
    //setUpFiles.ts 에 선언
    import '@testing-library/jest-dom';
    ```  
