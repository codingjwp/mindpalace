# Atoms

## atom Options

**key** : 내부적으로 atom을 식별하는데 사용되는 고유한 문자열.  
**default** : atom의 초기값 또는 Promise 또는 동일한 타입의 값을 나타내는 다른 atom이나 seletor.  
**effects_UNSTABLE** : atom을 위한 선택적 atom Effects 배열.  
**dangerouslyAllowMutability** : 경우에 따라 상태 변경을 나타내지 않는 원자 단위로 저장된 객체의 변경을 허용하는 것이 바람직할 수 있습니다. 이 옵션을 사용하면 개발 모드에서 오브젝트 동결을 재정의할 수 있습니다. **(??)**  

```typescript
function atom<T>({
  key: string,
  default: T | Promise<T> | RecoilValue<T>,
  effects_UNSTABLE?: $ReadOnlyArray<AtomEffect<T>>,
  dangerouslyAllowMutability?: boolean,
}): RecoilState<T>
```

### atom과 상호작용하기 위해 사용되는 Hooks

`useRecoilState()` : atom에 읽고 쓰려 할 때 사용하는 Hook.  
`useRecoilValue()` : atom에 읽기만 할 때 사용하는 Hook.  
`useSetRecoilState()` : atom에 쓰기만 할 때 사용하는 Hook.  
`useResetRecoilState()` : atom에 초기값으로 초기화 할 때 사용하는 Hook.  
`useRecoilCallback()` : 컴포넌트를 구독하지않고 atom값을 읽어야 할 때 사용하는 Hook.  

### atom의 주의점

atom을 설정할때 Promise를 할당할 수 없습니다.  
직접적으로 Promise나 RecoilValue를 저장할 수 없지만 객체를 감싸는 데는 사용할 수 있습니다.  

### 추가된 내용??

`atomFamily()` 는 관련 상태의 컬렉션을 저장하거나 원자 상태의 범위를 지정하는 데 유용할 수 있습니다. 

전역적으로 상태를 관리하는게 아닌 특정 컴포넌트내에만 작동하도록 범위를 지정하는 형태 **(??)**