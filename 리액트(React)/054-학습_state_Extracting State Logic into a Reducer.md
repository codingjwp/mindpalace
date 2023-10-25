# Extracting State Logic into a Reducer

> state로직을 reducer로 추출하기

여러 개의 state 업데이트가 여러 이벤트 핸들러에 분산되어 있는 컴포넌트는 과부하가 걸릴 수 있음.

이러한 경우 reducer 라고 하는 단일 함수를 통해 컴포넌트 외부의 모든 state 업데이트 로직을 통합할 수 있음.

## 배울 내용들

1. `reducer` 함수란 무엇인가.
2. `useState` 를 `useReducer`로 리팩토링 하는 방법
3. `reducer`를 사용해야 하는 경우
4. `reducer`를 잘 작성하는 방법

## reducer로 state 로직 통합

컴포넌트가 복잡해지면 state가 업데이트 되는 다양한 경우를 한눈에 파악하기 어려워 질 수 있음.

예를 들며 `TaskApp` 컴포넌트는 state에 `tasks` 배열을 보유 중에 있으며, 세 가지의 이벤트 핸들러를 사용하여 task를 추가, 제거, 수정을 함.

```javascript
import { useState } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

export default function TaskApp() {
  const [tasks, setTasks] = useState(initialTasks);

  function handleAddTask(text) {
    setTasks([
      ...tasks,
      {
        id: nextId++,
        text: text,
        done: false,
      },
    ]);
  }

  function handleChangeTask(task) {
    setTasks(
      tasks.map((t) => {
        if (t.id === task.id) {
          return task;
        } else {
          return t;
        }
      })
    );
  }

  function handleDeleteTask(taskId) {
    setTasks(tasks.filter((t) => t.id !== taskId));
  }

  return (
    <>
      <h1>Prague itinerary</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask} />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: 'Visit Kafka Museum', done: true},
  {id: 1, text: 'Watch a puppet show', done: false},
  {id: 2, text: 'Lennon Wall pic', done: false},
];
```

각 이벤트 핸들러는 state를 업데이트하기 위해 `setTasks`를 호출.

컴포넌트가 커질수록 흩어져 있는 state 로직의 양도 늘어나며 복잡성을 줄이고 모든 로직을 접근하기 쉽게 한 곳에 모을려면 state 로직을 컴포넌트 외부의 **reducer**라고 하는 단일 함수로 옮길 수 있음.

**Reducer**은 state를 관리하는 다른 방법이며 `useState`에서 `useReducer`로 마이그레이션 하는 방법은 세 단계로 진행

1. state를 설정하는 것에서 action들을 전달하는 것으로 변경
2. reducer 함수 작성
3. 컴포넌트에서 reducer 사용

### 스탭 1: state 설정을 action들의 전달로 바꾸기

현재 이벤트 핸들러는 state를 설정하여 수행할 작업을 지정

```javascript
function handleAddTask(text) {
  setTasks([
    ...tasks,
    { id: nextId++, text: text, done: false, },
  ]);
}

function handleChangeTask(task) {
  setTasks(
    tasks.map((t) => {
      if (t.id === task.id) { return task; } 
			else { return t;}
    })
  );
}

function handleDeleteTask(taskId) {
  setTasks(tasks.filter((t) => t.id !== taskId));
}
```

Add 누르면 `handleAddTask(text)` 호출

task 토글하거나 Save를 누르면 `handleChangeTask(task)` 호출

Delete 누르면 `handleDeleteTask(taskId)` 호출

reducer를 사용한 state 관리는 state를 직접 설정하는 것과 다르며 state를 설정하여 React에게 무엇을 할 지를 지시하는 대신 이벤트 핸들러에서 **action**을 전달하여 사용자가 방금 한 일을 지정. **(state 업데이트 로직은 다른 곳에 있음)**

이벤트 핸들러를 통해 `tasks` 를 설정 하는 대신 task를 **추가/변경/삭제** 하는 **action**을 전달하는 것.

```javascript
function handleAddTask(text) {
  dispatch({
    type: 'added',
    id: nextId++,
    text: text,
  });
}

function handleChangeTask(task) {
  dispatch({
    type: 'changed',
    task: task,
  });
}

function handleDeleteTask(taskId) {
  dispatch({
    type: 'deleted',
    id: taskId,
  });
}
```

`dispatch` 함수에 넣은 객체 `{ type: 'deleted', id: taskId,}`를 **action** 이라고 함. 이 객체는 일반적인 Javascript 객체 이며 여기에 무슨 일이 일어났는지에 대한 최소한의 정보를 포함해야 함.

### 노트

action 객체는 어떤 형태든 될 수 있으나 무슨일 이 일어나는지 설명하는 문자열 타입의 `type` 을 지정하고 추가적인 정보는 다른 필드를 통해 전달하도록 작성하는게 일반적.

### 스탭 2: reducer 함수 작성하기

reducer 함수에 state 로직을 둘 수 있음. 이 함수는 두개의 매개변수를 가지는데 하나는 현재의 state이고 다른 하나는 action 객체 임

다음 state를 반환하는 함수 예

```javascript
function yourReducer(state, action) {
  // return next state for React to set
}
```

React는 reducer로부터 반환된 것을 state로 설정할 것.

state를 설정하는 로직을 이벤트 핸들러에서 reducer 함수로 옮기기 위해서 다음과 같이 진행해보자.

1. 현재 state(tasks)를 첫 번째 매개변수로 선언.
2. action 객체를 두 번째 매개변수로 선언.
3. 다음 state를 reducer 함수에서 반환(React가 state로 설정함)

```javascript
function tasksReducer(tasks, action) {
  if (action.type === 'added') {
    return [
      ...tasks,
      { id: action.id, text: action.text, done: false, },
    ];
  } else if (action.type === 'changed') {
    return tasks.map((t) => {
      if (t.id === action.task.id) {
        return action.task;
      } else {
        return t;
      }
    });
  } else if (action.type === 'deleted') {
    return tasks.filter((t) => t.id !== action.id);
  } else {
    throw Error('Unknown action: ' + action.type);
  }
}
```

reducer 함수는 state(`tasks`)를 매개변수로 갖기 때문에 **컴포넌트 밖에서 reducer 함수**를 선언할 수 있음. 이렇게 하면 들여쓰기 단계도 줄이고 코드를 읽기 쉽게 만들 수 있음.

### 노트

위에 있던 코드는 `if/else` 구문을 사용. 그러나 reducer 안에서는 `switch` 구문을 사용하는게 한눈에 봐도 읽기 더 편하기 때문에 일반적으로 `switch` 사용. 

```javascript
function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        { id: action.id, text: action.text, done: false,},
      ];
    }
    case 'changed': {
      return tasks.map((t) => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

case 블럭을 모두 중괄호 `{` `}` 로 감싸는 걸 추천. 이렇게 하면 다양한 `case`들 안에 선언된 변수들이 서로 충돌하지 않음.

또한 하나의 `case`는 보통 `return` 으로 끝나야 함

### 스탭 3 : 컴포넌트에서 reducer 사용하기

마지막으로 컴포넌트에 `tasksReducer`를 연결해야 함. React에서 `useReducer` 훅을 `import` 

```javascript
import { useReducer } from 'react';
```

그런 다음 useState 대신 useReducer로 변경

```javascript
const [tasks, setTasks] = useState(initialTasks);
// change
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
```

`useReducer` Hook은 `useState`와 비슷함. 초기 state 값을 전달해야 하며, 그 결과로 state 값과 state 설정자 함수(useReducer의 경우 dispatch 함수)를 반환. 하지만 조금 다른 점이 존재.

`useReducer` Hook은 두 개의 인자를 받음.

1. reducer 함수
2. 초기 state

그리고 아래의 값을 반환.

1. state 값
2. dispatch 함수(사용자의 action을 reducer에 전달해서 주는 함수)

관심사 분리도 가능

```javascript
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { tasksReducer } from 'tasksReducer.js'

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>Prague itinerary</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: 'Visit Kafka Museum', done: true},
  {id: 1, text: 'Watch a puppet show', done: false},
  {id: 2, text: 'Lennon Wall pic', done: false},
];
```

```javascript
export function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case 'changed': {
      return tasks.map((t) => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

## `useState`와 `useReducer` 비교

Reducer도 좋은 점만 있는 것은 아님. 

다음은 `useState`와 `useReducer`를 비교할 수 있는 몇가지 방법.

- **코드 크기** : 일반적으로 `useState`를 사용하면 미리 작성해야 하는 코드가 줄어듬. `useReducer`를 사용하면 **reducer** 함수와 **action**을 전달하는 부분 모두 작성해야 하지만 많은 이벤트 헨들러가 비슷한 방식으로 state를 업데이트하는 경우 `useReducer`를 사용하면 코드를 줄이는 데 도움이 될 수 있음.
- **가독성** : `useState`로 간단한 state를 업데이트 하는 경우 가독성이 좋음. 그렇지만 state의 구조가 더욱 복잡해지면 컴포넌트의 코드의 양이 부풀어 오르고 한눈에 읽기 어려워질 수 있으며 이런 경우 `useReducer`를 사용하면 **업데이트 로직이 어떻게 동작하는지와 이벤트 핸들러를 통해 무엇이 일어났는지**를 깔끔하게 분리 가능.
- **디버깅** : `useState`에 버그가 있는 경우 state가 어디서 잘못 설정되었는지 그리고 왜 그런지 알기 어려울 수 있음. `useReducer`를 사용하면 reducer에 콘솔 로그를 추가하여 **모든 state 업데이트와 왜(어떤 action으로) 버그가 발생했는지 확인 가능**. 각 action이 정확하면 버그가 reducer로직 자체에 있다는 것 을 알 수 있음. 하지만 `useState`를 사용할 때 보다 더 많은 코드를 살펴봐야함.
- **테스팅** : reducer는 컴포넌트에 의존하지 않는 순수 함수. 즉 별도로 분리해서 내보내거나 테스트할 수 있음. 일반적으로 보다 현실적인 환경에서 컴포넌트를 테스트하는 것이 좋지만 복잡한 state 업데이트 로직의 경우 reducer가 특정 초기 state와 action에 대해 특정 state를 반환한다고 단언하는 것이 유용할 수 있음.

일부에서 state 업데이트로 인해 버그가 자주 발생하고 코드에 더 많은 구조를 도입 하는 경우 reducer를 사용하는 것이 좋음. 하지만 모든 컴포넌트에 reducer을 사용할 필요는 없기에 `useState`와 `useReducer`을 함꼐 사용.

## reducer 잘 작성하기

reducer을 작성할 때 두개의 팁을 기억하세요.

- **reducer는 반드시 순수해야 함** : state 설정 함수와 비슷하게 reducer는 렌더링 중에 실행됩 (action은 다음 렌더링 까지 대기) 즉 **reducer는 반드시 순수** 해야하며 입력 값이 같다면 결과 값도 항상 같아야 함. **요청을 보내거나 timeout을 스케쥴링하거나 사이드 이펙트을 수행해서는 안됨**. reducer는 객체 및 배열을 변이 없이 업데이트 해야함.
- **각 action은 여러 데이터가 변경되더라도 하나의 사용자 상호작용을 설명해야 함** : reducer가 관리하는 5개의 필드가 있는 양식에서 ‘재설정’을 누른 경우, 5개의 개별 `set_field action`보다는 하나의 `reset_form action`을 전송하는 것이 더 합리적. 모든 action을 reducer에 기록하면 어떤 상호작용이나 응답이 어떤 순서로 일어났는지 재구성할 수 있을 만큼 로그가 명확해야 함. 이는 디버깅에 도움이 됩니다!

## Immer를 사용하여 간결한 reducer 작성

Immer 라이브러리를 사용해 reducer를 더 간결하게 만들 수 있음. 여기서 `useImmerReducer`를 사용하면 `push` 또는 `arr[i] =` 할당으로 state를 변이할 수 있음.

```javascript
import { useImmerReducer } from 'use-immer';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

function tasksReducer(draft, action) {
  switch (action.type) {
    case 'added': {
      draft.push({
        id: action.id,
        text: action.text,
        done: false,
      });
      break;
    }
    case 'changed': {
      const index = draft.findIndex((t) => t.id === action.task.id);
      draft[index] = action.task;
      break;
    }
    case 'deleted': {
      return draft.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

export default function TaskApp() {
  const [tasks, dispatch] = useImmerReducer(tasksReducer, initialTasks);

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>Prague itinerary</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: 'Visit Kafka Museum', done: true},
  {id: 1, text: 'Watch a puppet show', done: false},
  {id: 2, text: 'Lennon Wall pic', done: false},
];
```

reducer은 순수해야 하지만 Immer은 안전하게 변이 할수 있는 객체를 제공.