# **Selector 제 2편**

## **쓰기가 가능한 selector**

***양방향 selector***은 들어오는 값을 매개변수로 수신하고 이를 사용하여 데이터흐름 그래프에 따라 변경사항을 [업스트림](#업스트림upstream)으로 전파할 수 있습니다.

사용자는 selector를 새 값으로 설정하거나 selecotr을 재설정 할 수 있으므로 수신되는 값은 selector가 나타내는 것과 **동일한 유형**이거나 재설정 작업을 나태내는 **DefaultValue 객체**입니다.

- 예시 : [출처 Recoil 공식문서](https://recoiljs.org/docs/api-reference/core/selector#writeable-selectors)
- 기본적으로 atom을 래핑하여 추가 필드를 추가합니다.

  ```typescript
  const proxySelector = selector({
    key: 'ProxySelector',
    get: ({get}) => ({...get(myAtom), extraField: 'hi'}),
    set: ({set}, newValue) => set(myAtom, newValue),
  });
  ```

- 아래와 같은 경우 데이터가 변환하므로 들어오는 값이 [DefaultValue](#defaultvalue)인지 확인이 필요합니다.

  ```typescript
  const transformSelector = selector({
    key: 'TransformSelector',
    get: ({get}) => get(myAtom) * 100,
    set: ({set}, newValue) =>
      set(myAtom, newValue instanceof DefaultValue ? newValue : newValue / 100),
  });
  ```

### **궁금한 단어**

#### **업스트림(upstream)**

- 특정 상태(atom 또는 selector)가 다른 상태들에 의존할 때, 의존되는 상태들을 지칭합니다

```typescript
// 상태 stateA, stateB (upstream 상태)
const stateA = atom({ key: "stateA", default: 10 });
const stateB = atom({ key: "stateB", default: 20 });

// 상태 stateC를 정의. (downstream 상태)
const stateC = selector({
  key: "stateC",
  get: ({ get }) => {
    const aValue = get(stateA);
    const bValue = get(stateB);
    // 상태 stateC는 상태 stateA와 stateB에 의존하여 값을 계산합니다.
    return aValue * bValue;
  },
});
```

#### **DefaultValue**

- ***DefaultValue***는 Recoil에서 제공하는 ***특별한 타입의 객체***입니다. 이것은 ***상태를 기본값***으로 되돌리고 싶을때 사용하는 특별한 신호 같은 것 입니다.

```typescript
import { DefalutValue } from 'recoil';

const myAtom = atom({
  key: "myAtom",
  defalut: 10,
});

const mySelector = selector({
  key: "mySelector",
  get: ({get}) => get(myAtom),
  set: ({set}, newValue) => set(myAtom, newValue instanceof DefaultValue ? newValue: newValue * 2),
});

const MyComponent = () => {
  const [value, setValue] = useRecoilState(mySelector);
  const initValue = () => {
    // DefaultValue로 상태를 기본값으로 초기화
    setValue(new DefaultValue())
  }
  // ... 생략
}

```

---
[**이전페이지 Recoil selector(기본편)**](./03-전역상태관리%20Recoil-selector(기본편).md)  
[**다음페이지 Recoil selecotr(비동기)**](./05-전역상태관리%20Recoil-selector(비동기)%20.md)