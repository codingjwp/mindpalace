# **RecoilRoot**

***Atom***이 값을 갖는 컨텍스트를 제공.  
***Rocoil hook***을 사용하는 컴포넌트의 조상이어야 합니다.

**예시** : 아래와 코드와 같이 App 컴포넌트에서 useRecoilValue라는 Recoil hook을 사용하였을 시 RecoilRoot는 해당 App 컴포넌트 보다 상위에 선언되어야 합니다.

```typescript
const App = () => {
  const count = useRecoilValue(counterState);
  return (<div>Count: {count}</div>);
}

<RecoilRoot>
  <App />
</<RecoilRoot>>
```

## **Props** 

### **initializeState?: (MutableSnapshot => void)**

***Atom state***를 초기화 하기 위한 MutableSnapshot을 받는 선택적 함수, 초기 렌더링에 대한 상태를 설정하지만 후속 상태변경 및 비동기 초기화를 위한 것은 아님.  
비동기 상태 변경을 위해서는 ***`useSetRecoilState`*** , ***`useRecoilCallback`*** 훅을 사용

---

***Atom effects***를 사용하면 동적 아톰을 쉽게 초기화하고 초기화 로직을 atom definitions.와 함께 배치할 수 있습니다.~~  
***Atom effects*** 초기화는 초기화 상태보다 우선.  
**(Atom effects는 따로 정리)**

---

#### **Example initializeState**

아래의 코드와 같이 ***`initializeState`*** 함수는 ***snapshot 객체***를 이용해 ***atom myAtom***의 초기 값을 10으로 설정.  
***`<RecoilRoot>`*** 컴포넌트에 ***`initializeState prop`*** 으로 전달되어 초기 상태를 설정.  
이 함수는 한 번만 호출되기 때문에 이후 상태 변경이나 비동기 초기화 등에는 사용되지 않습니다. 

```typescript
import { RecoilRoot, atom, MutableSnapshot } from 'recoil';

const myAtom = atom({
  key: 'myAtom',
  default: 0,
});

function initializeMyState(snapshot: MutableSnapshot) {
  snapshot.set(myAtom, 10);
}

function App() {
  return (
    <RecoilRoot initializeState={initializeMyState}>
      {/* 나머지 컴포넌트들 */}
    </RecoilRoot>
  );
}
export default App;
```

#### **왜?? atom default로 쓰지 않고 initializeState를 사용할까??**

Recoil의 초기화 할때 더 ***유연***하게 사용하기위해 사용.

***`initializeState`*** 는 여러가지 atom의 상태를 ***한번에 초기화***가 가능.  
또한 초기화 로직이 계산이 필요하거나 여러가지 참조 해야 할 경우처럼 ***복잡한 경우 사용이 편함***.  
초기화시 한 파일에서 관리할 수 있어 ***관리가 용이함*** 

```typescript
const myAtom1 = atom({
  key: 'myAtom1',
  default: 0,
});
const myAtom2 = atom({
  key: 'myAtom2',
  default: '',
});

function initializeMyState(snapshot: MutableSnapshot) {
  snapshot.set(myAtom1, 10);
  snapshot.set(myAtom2, '안녕하세요');
}
```

### **override?: boolean**

기본값은 `true`.  
override 는 `<RecoilRoot>`가 다른 `<RecoilRoot>`안에 중첩된 경우에만 중요합니다.  
true 이면 ***새로운 Recoil scope(별도의 Recoil 상태 공간)*** 를 생성하고 false `<RecoilRoot>` 자손을 렌더링하는 역할만 수행

#### **예시**

아래의 코드와 같이 `override=true`를 적용 한 `<RecoilRoot>`의 자손 노드 경우의 count 수량은 독립적으로 수량이 올라갑니다.  

하지만 `override=false`를 적용한 `<RecoilRoot>`의 자손 경우 **Parent RecoilRoot** 밑에 있는 `<RecoilRoot>`와 상태를 공유하기 때문에 둘 중 하나라도 수량을 올리면 동일하게 수량이 올라갑니다.

```typescript
import { RecoilRoot, atom, useRecoilState } from 'recoil';

const counterAtom = atom({
  key: 'counter',
  default: 0,
});

function Counter() {
  const [count, setCount] = useRecoilState(counterAtom);
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>+</button>
      <span>{count}</span>
    </div>
  );
}

function App() {
  return (
    <div>
      <h2>Parent RecoilRoot</h2>
      <RecoilRoot>
        <Counter />
        <h2>Child RecoilRoot (override=true)</h2>
        <RecoilRoot override={true}>
          <Counter />
        </RecoilRoot>
        <h2>Child RecoilRoot (override=false)</h2>
        <RecoilRoot override={false}>
          <Counter />
        </RecoilRoot>
      </RecoilRoot>
    </div>
  );
}

export default App;
```

#### 왜?? 이렇게 상태를 분리 할까??

프로젝트의 복잡성을 줄이고, 독립적인 컴포넌트들이 서로간의 상태를 공유할 필요가 없을 때 사용.  
상태 분리의 이점들 : ***컴포넌트의 독립성 강화***, ***코드 가독성 향상***, ***재사용성 증가***, ***테스트 용이성 증가***, ***데이터 통신 감소***