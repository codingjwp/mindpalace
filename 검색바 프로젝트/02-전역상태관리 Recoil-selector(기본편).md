# **Selector 제 1편**

selector은 Recoil에서 함수 또는 파생된 상태를 나타낸다.  
get 함수만 제공된 경우 읽기 전용이며 RecoilValueReadOnly 반환  
set 함수가 제공되면 쓰기 가능한 RecoilState 객체를 반환  
Recoil은 atom과 selector 상태변경을 관리하여 해당 selector를 구독하는 컴포넌트에 언제 re-rendering하도록 알릴지 파악합니다.  
selector의 객체 값이 직접 변경되면 이를 우회하여 구독하는 컴포넌트에 알리지 않을 수 있습니다.  
버그를 감지하는 데 도움이 되도록 리코일은 개발 모드에서 선택기 값 오브젝트를 동결합니다.

## **Selecotr options**

- ***`key`*** : selector을 식별하는 고유한 문자열, 전체적으로 atom, selecotr와 관려하여 고유, 지속성을 위해 사용되는 경우 실행 전반에 걸쳐 안정적이어야 합니다.
  - ***지속성(persistence)*** : 여러 렌더링 사이에서 일관된 결과를 반환하고, 동일한 입력에 대해서는 동일한 출력을 생성해야 함을 의미합니
- ***`get`*** : 파생된 상태값을 평가하는 함수, 값을 직접 반환하거나 비동기 ***Promise***, ***Loadable***, 동일한 유형 ***atom***, **selector**를 반환할 수 있습니다.
  - selector 값을 Promise나 Loadable 같은 걸로 설정할려면 `selector.value()`로 감싸면 됩니다.
  - ***get()*** : 다른 atom/selector에서 값을 가져오는 데 사용되는 함수입니다. 이 함수에 전달된 모든 atom/selector는 selector에 대한 종속성 목록에 암시적으로 추가됩니다. selector의 종속성 중 하나라도 변경되면 selector가 다시 평가됩니다.
  - ***getCallback()*** : callback 인터페이스를 사용하여 Recoil-aware callback을 생성하는 함수 ***(??)***
- ***`set`*** : 해당 property 설정되어 있으면 selector은 쓰기가 가능한 상태로 반환. 첫 번째 매개변수로 콜백 객체와 새 값을 전달받는 함수. 전달 받는 값은 T 유형 값이거나 DefaultValue 유형 객체 일 수 도 있습니다.
  - ***get()*** : atom/selector에서 값을 가져오는 함수.
  - ***set()*** : upstream Recoil state를 설정 하는데 사용하는 함수. 첫번째 매개변수는 Recoil state, 두번째는 새 값. 새 값은 DefaultValue 객체이거나 업데이트 함수.
  - ***reset()*** :upstream Recoil state를 기본 값으로 리셋하는데 사용하는 함수.
- ***`dangerouslyAllowMutability`*** : 경우에 따라 상태 변경을 나타내지 않는 selector에 저장된 객체의 변경을 허용하는 것이 바람직할 수 있습니다. 이 옵션을 사용하면 개발 모드에서 객체 동결을 재정의할 수 있습니다.***(??)***
- ***`cachePolicy_UNSTABLE`*** : 내부 selecotr 캐시 동작을 정의합니다. 종속성이 많이 변경되는 selector있는 경우 메모리 사용량을 제어하는데 유용합니다.
  - ***`eviction`*** :
    - ***`lru`*** : maxSize를 설정합니다. maxSize를 초과할때 가장 최근에 사용된 값을 selector캐시에서 제거합니다.
    - ***`keep-all`*** : selector의 종속성과 해당 값이 selector캐시에 무기한 저장
    - ***`most-recent`*** :크기 1캐시만 사용하며 가장 최근에 저장된 종속성 집합,값만 유지  
  - 캐시는 모든 종속성과 그 값을 포함하는 키를 기반으로 selector 값을 저장합니다. 즉, 내부 selector 캐시의 크기는 selector 값의 크기와 모든 종속성의 고유 값의 수에 따라 달라집니다. 기본 설정(keep-all)은 향후 변경될 수 있습니다.

```typescript
function selector<T>({
  key: string,
  get: ({
    get: GetRecoilValue,
    getCallback: GetCallback,
  }) => T | Promise<T> | Loadable<T> | WrappedValue<T> | RecoilValue<T>,
  set?: (
    {
      get: GetRecoilValue,
      set: SetRecoilState,
      reset: ResetRecoilState,
    },
    newValue: T | DefaultValue,
  ) => void,
  dangerouslyAllowMutability?: boolean,
  cachePolicy_UNSTABLE?: CachePolicy,
})
```

```typescript
type ValueOrUpdater<T> = T | DefaultValue | ((prevValue: T) => T | DefaultValue);
type GetCallback =
  <Args, Return>(
    callback: CallbackInterface => (...Args) => Return,
  ) => (...Args) => Return;
type GetRecoilValue = <T>(RecoilValue<T>) => T;
type SetRecoilState = <T>(RecoilState<T>, ValueOrUpdater<T>) => void;
type ResetRecoilState = <T>(RecoilState<T>) => void;
type CachePolicy =
  | {eviction: 'lru', maxSize: number}
  | {eviction: 'keep-all'}
  | {eviction: 'most-recent'};
```

## **Selector example**

### **기본적인 selector**

```typescript
const searchTextSelector = selector({
  key: 'searchTextSelector',
  get: ({get}) => get(searchTextState),
});
```

### **동적인 종속성을 가진 selector**

읽기 전용 selector은 종속성을 기반으로 하는 get메서드가 있습니다.  

- 예시 :  searchTextState를 종속성으로 갔는 get

  ```typescript
  const searchTextSelector = selector({
    key: 'searchTextSelector',
    get: ({get}) => searchTextState(searchTextState),
  });

이러한 종속성중 하나라도 업데이트 될 경우 selector이 다시 실행됩니다.  
종속성은 selector이 샐행될때 실제로 사용하는 atom,selector따라 동적으로 결정됩니다. 이전 종속값에 따라 다른 추가 종속값을 동적으로 사용할 수 있습니다.

- 예시 : toggleState 종속성에 따라 selectorA, selectorB로 동적으로 사용합니다. [(출처 Recoil 공식문서 코드)](https://recoiljs.org/docs/api-reference/core/selector#dynamic-dependencies)

  ```typescript
  const toggleState = atom({key: 'Toggle', default: false});

  const mySelector = selector({
    key: 'MySelector',
    get: ({get}) => {
      const toggle = get(toggleState);
      if (toggle) {
        return get(selectorA);
      } else {
        return get(selectorB);
      }
    },
  });
  ```

리코일은 현재 데이터 흐름 그래프를 자동으로 업데이트하여 selecotr가 현재 종속성 집합에서 업데이트만 구독하도록 하며 이를 통해 필요한 부분만 업데이트 되어 성능 최적화가 이루어집니다.

- 예시 : sum은 numberA, numberB를 의존하는 selector로 numberA또는 numberB가 업데이트 되면  Recoil은 데이터 흐름 그래프를 자동으로 업데이트 하여 sum이 해당 변경 사항에 구독되도록 합니다.

  ```typescript
  const numberA = atom({ key: "numberA", default: 1 });
  const numberB = atom({ key: "numberB", default: 2 });
  
  const sum = selector({
    key: "sum",
    get: ({ get }) => {
      const a = get(numberA);
      const b = get(numberB);
      return a + b;
    },
  });
  ```
