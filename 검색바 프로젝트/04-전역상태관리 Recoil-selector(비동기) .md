# **Selector 제 3편**

## **비동기식 selector**

selector get callback에서 값자체 대신 값에대한 Promise를 반환하기만 하면 인터페이스는 완전히 동일하게 유지됩니다. 다른 selector에서 이 selector의존하여 데이터를 추가로 변환할 수 있습니다.  

selector는 ***`idempotent`*** 함수를 나타낸다는 점을 명심하세요: 주어진 입력 세트에 대해 항상 동일한 결과를 생성해야 합니다. 

### 비동기 selector 예제

쿼리해야 하는 사용자 이름이 일부 데이터베이스에 저장되어 있는 경우, 프로미스를 반환하거나 비동기 함수를 사용하기만 하면 됩니다.  
종속성이 변경되면 선택기가 다시 평가되고 새 쿼리가 실행됩니다.  
결과는 캐시되므로 쿼리는 고유 입력당 한 번만 실행됩니다.  
```typescript
const currentUserNameQuery = selector({
  key: 'CurrentUserName',
  get: async ({get}) => {
    const response = await myDBQuery({
      userID: get(currentUserIDState),
    });
    return response.name;
  },
});

function CurrentUserInfo() {
  const userName = useRecoilValue(currentUserNameQuery);
  return <div>{userName}</div>;
}
```

### 비동기식을 위한

React render 함수는 동기식이기 때문에 recoil은 보류중인 데이터를 처리하기 위해 React Suspense와 함께 작동하도록 설계 되었습니다.

```typescript
function MyApp() {
  return (
    <RecoilRoot>
      <React.Suspense fallback={<div>Loading...</div>}>
        <CurrentUserInfo />
      </React.Suspense>
    </RecoilRoot>
  );
}
```

### 비동기에서 에러가 발생하면 어떻게 해야할까??

Recoil selector는 컴포넌트가 해당 값을 사용할려고 할떄 에러를 던질 수 있기 때문에 이 오류를 잡기위해 React [`<ErrorBoundary>`](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary)를 이용합니다.

```typescript
const currentUserNameQuery = selector({
  key: 'CurrentUserName',
  get: async ({get}) => {
    const response = await myDBQuery({
      userID: get(currentUserIDState),
    });
    if (response.error) throw response.error;
    return response.name;
  },
});

function CurrentUserInfo() {
  const userName = useRecoilValue(currentUserNameQuery);
  return <div>{userName}</div>;
}

function MyApp() {
  return (
    <RecoilRoot>
      <ErrorBoundary>
        <React.Suspense fallback={<div>Loading...</div>}>
          <CurrentUserInfo />
        </React.Suspense>
      </ErrorBoundary>
    </RecoilRoot>
  );
}
```

### ErrorBoundary는 무엇인가요???

기본적으로 렌더링 도중 에러를 발생시키면 React는 화면에서 해당 UI를 제거하는 것을 방지 하기위해 UI일부를 Error Boundary로 감쌉니다.  

Error Boundary는 에러가 발생한 부분 대신 오류 메세지를 같은 fullback UI를 표시할 수 있는 특수 컴포넌트 입니다.  

Error Boundary 컴포넌트 구현을 위해 static ***getDerivedStateFromError***을 이용해 에러에 대한 응답을 상태를 업데이트하고 에러 메세지를 표시해야합니다.  

또한 선택적으로 ***componentDidCatch***를 구현하여 분석 서비스에 오류를 기록하는등 추가 로직을 추가할 수 있습니다.  