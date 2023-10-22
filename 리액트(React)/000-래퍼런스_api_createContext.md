# createContext

`createContext`를 사용하면 컴포넌트가 제공하거나 읽을 수 있는 컨텍스트를 만들 수 있습니다.

```typescript
const SomeContext = createContext(defaultValue)
```

## 참조

### **`createContext(defaultValue)`**

컴포넌트 외부에서 `createContext`를 호출하여 컨텍스트를 생성하세요.

```typescript
import { createContext } from 'react';
const ThemeContext = createContext('light');
```

### 매개변수

- `defaultValue` : 상위 트리에 일치하는 `Provider`가 없을 경우 이 컨테스트가 갖도록 할 값입니다.
의미 있는 기본값이 없으면 `null`로 지정하세요. 기본값은 **가장 마지막 수단**으로 사용됩니다.

### 반환값

- `createContext` 는 컨텍스트 객체를 반환합니다.
- **컨텍스트 객체는 어떤한 정보도 보유하고 있지 않습니다.** 다른 컴포넌트가 읽거나 제공할 수 있는 컨텍스트를 나타냅니다.
- 상위 컴포넌트에서 `SomeContext.Provider`를 사용해 컨텍스트 값을 지정하고, 하위 컴포넌트에서 `useContext(SomeContext)`를 호출해 컨텍스트 값을 읽습니다.
    - `SomeContext.Provider`를 사용하면 컴포넌트에 컨텍스트 값을 제공할 수 있습니다.
    - `SomeContext.Consumer` 는 컨텍스트 값을 읽는 또다른 방법이며 거의 사용되지 않습니다.

### **`SomeContext.Provider`**

컴포넌트를 컨텍스트 provider로 감싸 내부의 모든 컴포넌트에 대한 이 컨텍스트의 값을 지정하세요

```typescript
function App() {
  const [theme, setTheme] = useState('light');
  return (
    <ThemeContext.Provider value={theme}>
      <Page />
    </ThemeContext.Provider>
  );
}
```

### **Props**

- `value` : 이 값은 해당 provider 내에서 이 컨텍스트를 읽는 모든 컴포넌트에 전달하려는 값으로, 깊이에 상관없이 전달할 수 있습니다

### **`SomeContext.Consumer`**

`useContext` 이전에는 컨텍스트를 읽는 오래된 방법이 있었습니다

```typescript
function Button() {
  // 🟡 오래된 방법
  return (
    <ThemeContext.Consumer>
      {theme => (
        <button className={theme} />
      )}
    </ThemeContext.Consumer>
  );
}
```

아래의 코드와 같이 `useContext()`를 사용하여 수정해주세요.

```typescript
function Button() {
  const theme = useContext(ThemeContext);
  return <button className={theme} />;
}
```

### Props

- `children` : 함수입니다. React는 `useContext()`와 동일한 알고리즘에 의해 결정된 현재 컨텍스트 값으로 전달한 함수를 호출하고, 이 함수에서 반환한 결과를 렌더링합니다. 또한 **React는 부모 컴포넌트의 컨텍스트가 변경될 때마다 이 함수를 다시 실행하고 UI를 업데이트합니다**.

## 사용법

### **context 생성하기**

```typescript
import { createContext } from 'react';

const ThemeContext = createContext('light');
const AuthContext = createContext(null);
```

`useContext()`를 사용하여 컨텍스트 읽습니다.

```typescript
function Button() {
  const theme = useContext(ThemeContext);
}

function Profile() {
  const currentUser = useContext(AuthContext);
}
```

컴포넌트에 다른 동적 값을 제공하는 방법입니다.

```typescript
function App() {
  const [theme, setTheme] = useState('dark');
  const [currentUser, setCurrentUser] = useState({ name: 'Taylor' });

  return (
    <ThemeContext.Provider value={theme}>
      <AuthContext.Provider value={currentUser}>
        <Page />
      </AuthContext.Provider>
    </ThemeContext.Provider>
  );
}
```

컨텍스트가 유용한 이유는 다음과 같이 **컴포넌트에 다른 동적 값을 제공할 수 있기 때문**입니다

## 문제 해결

### **컨텍스트 값을 변경하는 방법을 찾을 수 없습니다**

**이 값은 절대 변하지 않습니다**.(예 `‘light’`) React는 위에서 일치하는 `provider`를 찾을 수 없는 경우에만 이 값을 대체하여 사용합니다.

```typescript
const ThemeContext = createContext('light');
```