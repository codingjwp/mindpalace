# Tanstack Query

프론트엔드 사용자 인터페이스를 백엔드 데이터와 동기화된 상태로 유지하는데 이용하는 라이브러리 입니다.

Tanstack Query에는 HTTP 요청을 전송하는 로직이 내장되어 있지 않습니다.  
대신 요청을 관리하는 로직을 제공합니다. 요청과 관련된 데이터와 발생 가능한 오류를 추적하는 역학을 합니다.


## 기본적인 리액트 쿼리 실행 방법

```javascript
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <RouterProvider router={router} />;
    </QueryClientProvider>
  )
}
```

```javascript
import { fetchEvents } from '../../util/http.js'
import { useQuery } from '@tanstack/react-query';

const { data, isPending, isError, error } = useQuery({
  queryKey: ['events'],
  queryFn: fetchEvents
})
```

## 쿼리 동작 이해 및 구성

자체적으로 다른 요청이 전송되지만 이 요청이 데이터를 즉시 표시하기 위한 것은 아닙니다.  
리액트 쿼리는 요청을 통해 얻은 응답 데이터를 캐시 처리하고 나중에 동일한 쿼리 키를 가진 다른   `useQuery`가 실행되면 이 데이터를 재사용합니다.

`staleTime` 자체적으로 전송하기 전에 기다릴 시간을 설정하는 방법이며 불필요한 요청을 막기위해 사용합니다.  
`gcTiem` 데이터와 캐시를 얼마나 오랫 동안 보관할 지를 제어합니다. 기본값은 5분 입니다.


```javascript
const { data, isPending, isError, error } = useQuery({
  queryKey: ['events'],
  queryFn: fetchEvents,
  staleTime: 5000,
  gcTime: 30000,
})
```


## 쿼리 동작시 문제 되는 경우

**처음 로딩 할때 불러오는 쿼리**

```javascript
const { data, isPending, isError, error } = useQuery({
  queryKey: ['events'],
  queryFn: fetchEvents,
  staleTime: 5000,
  gcTime: 30000,
})
```

**내용을 검색할 때 사용하는 리액트 쿼리**

```javascript
  const searchElement = useRef();
  const [searchTerm, setSearchTerm] = useState('');
  const {data, isPending, isError, error} = useQuery({
    querykey: ['events', { search: searchTerm }],
    queryFn: ({signal}) => fetchEvents({signal, searchTerm})
  });
```

위에 코드가 작성되어있는 컴포넌트가 같은 위치에 있을 경우 처음 로드 했을 때, 리로드 했을때
동일한 값이 출력됩니다.

하지만 `queryKey`가 다르기 때문에 동일한 값이지만 캐쉬가 되지 않습니다.

이러한 현상을 막기위해 `enabled`를 사용합니다.  
`enabled`는 기본 값이 `true`이며 `false` 경우 쿼리를 비활성화 합니다.

```javascript
  const searchElement = useRef();
  const [searchTerm, setSearchTerm] = useState();
  const {data, isPending, isError, error} = useQuery({
    querykey: ['events', { search: searchTerm }],
    queryFn: ({signal}) => fetchEvents({signal, searchTerm}),
    enabled: searchTerm !== undefined
  });
```

위의 코드가 작성되면 Loading 관련 처리를 하였다면 처음 로드나 리로드를 했을 때 로딩관련 내용이 표시됩니다.  
이 것을 해결하기 위해 `isPending`을 `isLoading`으로 바꿔줍니다. 그래야지 쿼리가 비활상화되었다고 `true`가 되지 않습니다.

```javascript
const {data, isLoading, isError, error} = useQuery({
    querykey: ['events', { search: searchTerm }],
    queryFn: ({signal}) => fetchEvents({signal, searchTerm}),
    enabled: searchTerm !== undefined
  });
```

## 데이터 수정 방법

`useQuery`는 데이터를 가져오는 것에 대해서만 사용하기 때문에 데이터를 보내기 위해서는 `useMutation`을 사용해야 합니다.

```javascript
import { useMutation } from '@tanstack/react-query';
```

`useMutation`은 `useQuery` 처럼 `queryKey`같이 `mutationKey`가 존재하지만 해당 키는 데이터를 캐쉬해주지는 않습니다. 그렇기 꼭 작성할 필요는 없습니다.

```javascript
const { mutate } = useMutation({
  mutationFn: createNewEvent
})
```

또한 `useQuery`에서는 객체를 넘길려면 함수안에 데이터 전송 함수를 작성했지만 `useMutation` 경우는 `mutate` 함수를 호출해 요청을 전송할 수 있습니다.

```javascript
// useQuery 경우
const {data, isLoading, isError, error} = useQuery({
    querykey: ['events', { search: searchTerm }],
    queryFn: ({signal}) => fetchEvents({signal, searchTerm}),
  });

// useMutation 경우
const { mutate, isPending, isError, error } = useMutation({
  mutationFn: createNewEvent
})
function handleSubmit(formData) {
  mutate({ event: formData });
}
```

## mutation 성공 시 동작 및 쿼리 무효화

mutation이 완료될 때 까지 기다리고 이동하는 방법입니다.  
onSuccess 메소드 안에 완료 후 적용 할 내용을 작성합니다.

```javascript
const { mutate, isPending, isError, error } = useMutation({
  mutationFn: createNewEvent,
  onSuccess: () => {
    navigate('/events')
  }
})
```

하지만 위 코드와 같이 사용했을때 캐시기능을 사용한 쿼리 경우 새롭게 UI를 업데이트 하지 않습니다.  
그걸 방지하기위해 `queryClient.invalidateQueries();` 를 사용하여 해당 캐시가 만료되었으니 새로 UI를 업데이트 해야된다는 걸 알려줘어야 합니다.

아 물론 `querClient`는 `<QueryClientProvider>` 에서 사용한 client랑 동일해야 합니다.  
> `exact: true`는 앞에 작성한 queryKey가 정확히 동일한 부분만 캐쉬를 만료시킵니다.

```javascript
import { createNewEvent, queryClient } from '../../util/http.js';

const { mutate, isPending, isError, error } = useMutation({
  mutationFn: createNewEvent,
  onSuccess: () => {
    queryClient.invalidateQueries({queryKey: ['events'], exact: true });
    navigate('/events')
  }
})
```

## 자동 가져오기 무효화

만약 `useMutation`과 같은 컴포넌트에 `useQuery`를 사용했다면 아래 `useMutation`이 실행 되면 `invalidateQeries`를 사용하여 무효화를 해버리면 같은 컴포넌트에 있는 `useQuery`도 동일하게 실행이 되기 때문에 문제가 발생합니다.

그러한 문제를 방지하기위해 `refetchType`을 적용합니다.

`refetchType: 'none'`을 사용하여 쿼리를 무효화 하고 즉시 다시 실행되도록 하지 않고  
다음 번에 요청을 될때 다시 실행됩니다. 

즉 동일한 컴포넌트에 있는 `useQuery`는 실행이 안되고 `navigate('/events)` 이동하여 해당 경로에 있는 `useQuery`는 재실행 되게됩니다.

```javascript
const { mutate, isPending, isError: isMutationError } = useMutation({
  mutationFn: deleteEvent,
  onSuccess: () => {
    queryClient.invalidateQueries({
      queryKey: ['events'],
      refetchType: 'none'
    });
    navigate('/events');
  }
})
```

## 낙관적 업데이트

아래의 코드는 낙관적 업데이트를 하는 코드 입니다.

```javascript
  const { mutate } = useMutation({
    mutationFn: updateEvent,
    onMutate: async (data) => {
      const newData = data.event;
      // 수정을 하기전에 모든 쿼리를 취소하는 부분
      await queryClient.cancelQueries({queryKey: ['events', {id : params.id}]});

      // 에러가 발생 시 이전 데이터로 변경하기 위해 이전 데이터 가져오는 부분
      const prevData = queryClient.getQueryData(['events', {id: params.id}]);

      // 새로운 데이터로 쿼리를 변경하는 부분
      queryClient.setQueryData(['events', {id: params.id}], newData);
      return { prevData };
    },
    onError: (error, data, context) => {
      // 에러 발생 시 이전 데이터로 변경하여 적용하는 부분
      queryClient.setQueryData(['events', {id: params.id}], context.prevData);
    },
    onSettled: () => {
      // 해당 부분을 다시 무효화해서 실행하는 부분
      queryClient.invalidateQueries(['events', {id: params.id}])
    }
  })
```

## 쿼리 키로 입력하기

아래 코드와 같이 querykey searchTerm와 queryFn에사용하는 searchTerm 이랑 같은 경우가 있습니다.

```javascript
const {data, isLoading, isError, error} = useQuery({
    queryKey: ['events', { search: searchTerm }],
    queryFn: ({signal}) => fetchEvents({signal, searchTerm}),
  });
```

그러면 그냥 복사 붙여 넣기가 아니라 querykey를 가지고 와서 재사용이 가능합니다.

```javascript
const {data, isLoading, isError, error} = useQuery({
    queryKey: ['events', { searchTerm: searchTerm }],
    queryFn: ({signal, queryKey}) => fetchEvents({signal, ...queryKey[1]}),
  });
```

## 리액트 라우터와 결합하여 사용하는 법

`loader`을 이용하여 초기 데이터를 가져오는 방법입니다.  
`lodaer`을 사용한다고 해서 `useQuery`를 삭제할 필요 없습니다.  
왜냐하면 `useQuery`에 캐시된 값을 다시 사용하기 떄문에 `useQuery`를 같이 사용해주세요.  
그리고 `staleTime: 10000` 5초에서 10초정도를 작성해서 두 번 불러오늘 걸 방지해 주세요.

```javascript
// 라우터 부분
{
  path: '/events/:id',
  element: <EventDetails />,
  children: [
    {
      path: '/events/:id/edit',
      element: <EditEvent />,
      loader: editLoader
    },
  ],
}

// EditEvent.jsx
export function loader({params}) {
  return queryClient.fetchQuery({
    queryKey: ['events', { id: params.id}],
    queryFn: ({signal}) => fetchEvent({signal, id: params.id}),
    staleTime: 10000
  })
}
```

action을 사용할 경우 낙관적 업데이트 더 이상 실행되지 않습니다.
그렇기 때문에 useMutation 사용된 부분을 지우고 submit으로 처리를 해야합니다.

```javascript
export async function action({request, params}) {
  const formData = await request.formData();
  const updateEventData = Object.fromEntries(formData);
  await updateEvent({id: params.id, event: updateEventData});
  await queryClient.invalidateQueries(['events']);
  return redirect('../');
}
```

```javascript
import { useSubmit } from 'react-router-dom';

const submit = useSubmit();

function handleSubmit(formData) {
  submit(formData, {method: 'PUT', });
}
```


## 데이터를 가져오는지 확인하기 위한 리액트 쿼리 훅

```javascript
import { useIsFetching } from '@tanstack/react-query';

function Example() {
  const fetching = useIsFetching();
  return (
    <div>
      {fetching > 0 && <progress />}
    </div>
  )
}
```