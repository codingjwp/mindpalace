# **Selector 제 2편**

## **쓰기가 가능한 selector**

***양방향 selector***은 들어오는 값을 매개변수로 수신하고 이를 사용하여 데이터흐름 그래프에 따라 변경사항을 [업스트림](#업스트림upstream)으로 전파할 수 있습니다.

사용자는 selector를 새 값으로 설정하거나 selecotr을 재설정 할 수 있으므로 수신되는 값은 selector가 나타내는 것과 **동일한 유형**이거나 재설정 작업을 나태내는 **DefaultValue 객체**입니다.

- 예시 : [출처 Recoil 공식문서](https://recoiljs.org/docs/api-reference/core/selector#writeable-selectors)

  ```typescript
  const proxySelector = selector({
    key: 'ProxySelector',
    get: ({get}) => ({...get(myAtom), extraField: 'hi'}),
    set: ({set}, newValue) => set(myAtom, newValue),
  });
  ```

### 궁금한 단어

#### 업스트림(upstream)

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
