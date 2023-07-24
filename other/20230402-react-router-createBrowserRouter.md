# React Roter v6 createBrowserRouter

## ***createBrowserRouter***
react router V6에 생성된 Data Api가능한 기능

`createBrowserRouter`과 `RouterProvider`를 이용하여 만들수 있는 router 기능   

```bash
#설치 방법
yarn add react-router-dom @types/react-router-dom
```
### 사용방법 예제
```typescript
// Router.tsx
import { createBrowserRouter } from 'react-router-dom'
```
```typescript
// 타입 지정
interface RouterItems {
  index?: boolean;
  path?: string;
  element?: React.ReactNode;
  errorElement?: React.ReactNode;
  linkLabel?: string;
  children?: {
    index?: boolean;
    path?: string;
    element: JSX.Element;
    errorElement: JSX.Element;
    linkLabel?: string;
  }[];
}
```
```typescript
// router 위치및 element 정보
const routerInfo: RouterItems[] = [
  {
    path: "/",
    element: <PageLayout />,
    errorElement: <NotFound />,
    children: [
      {
        index: true,
        element: <Main />,
        errorElement: <NotFound />,
        linkLabel: "홈",
      },
      {
        path: "musiclist",
        element: <Musiclist />,
        errorElement: <NotFound />,
        linkLabel: "둘러보기",
      },
      {
        path: "mylist",
        element: <Mylist />,
        errorElement: <NotFound />,
        linkLabel: "내리스트",
      },
    ],
  },
  {
    path: "/myinfo",
    element: <Myinfo />,
    errorElement: <NotFound />, 
  }
];
```
```typescript
// 위에 정보를 바로 요기에 작성해도 상관없음
// children?: RouterITems[]로 설정했을때 children에서 error 발생
const routerElement = createBrowserRouter(
  routerInfo.map((info) => {
    return {
      path: info.path,
      element: info.element,
      errorElement: info.errorElement,
      children: info.children
    }
  }))
```
### 설정한 것을 적용하는 방법
```typescript
// App.tsx
import { RouterProvider } from "react-router-dom"
// 위에 등록한 tsx 파일
import { routerElement } from './Router'

const App = () => {
  return (
    <div className="App">
      <RouterProvider router={routerElement} />
    </div>
  )
}

export default App
```