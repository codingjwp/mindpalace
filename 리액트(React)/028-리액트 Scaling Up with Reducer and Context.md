# Scaling Up with Reducer and Context

> reducer와 context로 확장하기 

**Reducer**를 사용하면 컴포넌트의 state 업데이트 로직을 통합할 수 있음.

**Context**를 사용하면 다른 컴포넌트들에 정보를 전달할 수 있음.

Reducer와 context를 함께 사용하여 복잡한 화면의 state를 관리할 수 있음.

## 배울 내용들

1. reducer와 context를 결합하는 방법
2. state와 dispatch 함수를 prop으로 전달하지 않는 방법
3. context와 state 로직을 별도의 파일에서 관리하는 방법

## reducer와 context를 결합

Reducer를 사용하면 이벤트 핸들러를 간결하고 명확하게 만들 수 있으나 앱이 커질수록 다른 어려움이 부딪힐 수 있음.

**현재 `tasks` state와 `dispatch` 함수는 최상위 컴포넌트인 `TaskBoard`에서만 사용할 수 있음.**

아래 코드와 같이 다른 컴포넌트들에서 tasks의 리스트를 읽고 변경할려면 prop을 통해 현재 state와 state를 변경할 수 있는 이벤트 핸들러를 명시적으로 전달해야 함.

```javascript
<TaskList
  tasks={tasks}
  onChangeTask={handleChangeTask}
  onDeleteTask={handleDeleteTask}
/>
```

간단한 예시 경우 잘 동작하지만 수십, 수백개의 컴포넌트를 거쳐 전달하기는 쉽지 않기 때문에 props대신 context에 넣어서 사용하고 싶음.

**그러면 반복적인 prop drilling 없이 아래의 모든 컴포넌트 트리에서 tasks를 읽고 dispatch 함수를 실행할 수 있습니다.**

### Reducer와 context 결합 방법

1. Context를 **생성**
2. State와 dispatch 함수를 context에 **넣음**.
3. 트리 안에서 context를 **사용**

### 스탭: 1  Context 생성

`useReducer`훅은 현재 `tasks`와 업데이트할 수 있는 `dispatch` 함수를 반환합니다.

```javascript
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
```

트리를 통해 전달하려면, 두 개의 별개의 context를 생성

- `TasksContext`는 현재 tasks 리스트를 제공합니다.
- `TasksDispatchContext`는 컴포넌트에서 action을 dispatch 하는 함수를 제공합니다.

```javascript
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

### 스탭: 2  State와 dispatch 함수를 context에 넣기

이제 `TaskBoard` 컴포넌트에서 두 context를 모두 불러올 수 있음. `useReducer()`를 통해 반환된 `task`, `dispatch`를 받고 아래 트리 전체에 전달

```javascript
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
  // ...
  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        ...
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}
```

### 스탭: 3  트리 안에서 context 사용하기

tasks 리스트나 이벤트 헨들러를 트리 아래로 전달할 필요 없음.

```javascript
<TasksContext.Provider value={tasks}>
  <TasksDispatchContext.Provider value={dispatch}>
    <AddTask />
    <TaskList />
  </TasksDispatchContext.Provider>
</TasksContext.Provider>
```

대신 필요한 컴포넌트에서 task리스트를 읽거나 disatch 함수를 읽고 호출할 수 있음

```javascript
const tasks = useContext(TasksContext);
const dispatch = useContext(TasksDispatchContext)
```

**`TaskBoard` 컴포넌트는 자식 컴포넌트에, `TaskList`는 `Task` 컴포넌트에 이벤트 핸들러를 전달하지 않음.**

```javascript
import { useState, useContext } from 'react';
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskList() {
  const tasks = useContext(TasksContext);
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task task={task} />
        </li>
      ))}
    </ul>
  );
}

function Task({ task }) {
  const [isEditing, setIsEditing] = useState(false);
  const dispatch = useContext(TasksDispatchContext);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            dispatch({
              type: 'changed',
              task: {
                ...task,
                text: e.target.value
              }
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={e => {
          dispatch({
            type: 'changed',
            task: {
              ...task,
              done: e.target.checked
            }
          });
        }}
      />
      {taskContent}
      <button onClick={() => {
        dispatch({
          type: 'deleted',
          id: task.id
        });
      }}>
        Delete
      </button>
    </label>
  );
}
```

**State와 state를 관리하는 `useReducer`는 여전히 최상위 컴포넌트인 `TaskBoard`에 있으나** `tasks`와 `dispatch`는 하위 트리 컴포넌트 어디서나 context를 불러와서 사용할 수 있습니다.

## 하나의 파일로 합치기

reducer과 context를 모두 하나의 파일에 작성하면 컴포넌트를 조금 더 정리할 수 있음.

1. Reducer로 state를 **관리**.
2. 두 context를 모두 자식 컴포넌트에 **제공**.
3. `children`을 prop으로 받기 때문에 JSX를 **전달**할 수 있음

```javascript
export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}
```

**이렇게 하면 `TaskBoard` 컴포넌트에서 복잡하게 얽혀있던 부분을 깔끔하게 정리할 수 있음.**

```javascript
export function useTasks() {
  return useContext(TasksContext);
}

export function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}
const tasks = useTasks();
const dispatch = useTasksDispatch();
```

이렇게 하면 동작이 바뀌는 건 아니지만, 다음에 context를 더 분리하거나 함수들에 로직을 추가하기 쉬어짐. 

**이제 모든 context와 reducer는 `TasksContext.js`에 존재. 이렇게 컴포넌트들이 데이터를 어디서 가져오는지가 아닌 무엇을 보여줄 것인지에 집중할 수 있도록 깨끗하게 정리할 수 있음.**

### 노트

`useTasks`와 `useTasksDispatch` 같은 함수들을 *커스텀 훅* 이라고 함.

이름이 `use`로 시작되는 함수들은 커스텀 훅 이며 커스텀 훅 안에서도 `useContext` 등 다른 Hook을 사용할 수 있음

⏮️ Previous : [Passing Data Deeply with Context](./027-리액트%20Passing%20Data%20Deeply%20with%20Context.md)

⏭️ Next : [Escape Hatches]()