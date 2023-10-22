# `<Profiler>`

`<Profiler>`를 사용하면 프로그램적으로 React 트리의 렌더링 성능을 측정할 수 있습니다.

```typescript
<Profiler id="App" onRender={onRender}>
  <App />
</Profiler>
```

## 참조

### **`<Profiler>`**

컴포넌트 트리를 `<Profiler>`로 감싸서 렌더링 성능을 측정합니다.

### props

- `id` : 측정 중인 UI 부분을 식별하는 문자열입니다.
- `onRender` : 프로파일링된 트리 내의 컴포넌트가 업데이트될 때마다 React가 호출하는 `onRender` 콜백입니다. 이 콜백은 렌더링된 내용과 소요된 시간에 대한 정보를 받습니다.

### 주의사항

- 프로파일링은 약간의 오버헤드를 추가하므로 **상용 빌드에서는 기본적으로 비활성화되어 있습니다.**

### **`onRender` 콜백**

React는 렌더링된 내용에 대한 정보와 함께 `onRender` 콜백을 호출합니다.

```typescript
function onRender(id, phase, actualDuration, baseDuration, startTime, commitTime) {
  // 렌더링 타이밍을 집계하거나 로그를 남깁니다...
}
```

### 매개변수

- **`id`** : 방금 커밋한 `<Profiler>` 트리의 `id` 문자열 prop입니다. 여러 프로파일러를 사용하는 경우 트리의 어느 부분이 커밋되었는지 식별할 수 있습니다.
- **`phase`** : `mount`,`update` 혹은 `nested-update`. 이를 통해 트리가 처음 마운트되었거나, props, state 또는 훅의 변경으로 인해 다시 렌더링되었는지 알 수 있습니다.
- **`actualDuration`** : 현재 업데이트에 대해 `<Profiler>` 및 하위 컴포넌트들을 렌더링하는 데 걸린 시간(밀리초)입니다. 이 값은 하위 트리가 메모화(예: `[memo](https://react-ko.dev/reference/react/memo)`, `[useMemo](https://react-ko.dev/reference/react/useMemo)`)를 얼마나 잘 사용하는지를 나타냅니다. 많은 자손들은 특정 props가 변경되는 경우에만 다시 렌더링하면 되므로, 이상적으로 이 값은 최초 마운트 이후에는 크게 감소해야 합니다.
- **`baseDuration`** : 최적화 없이 전체  `<Profiler>` 하위 트리를 다시 렌더링하는 데 걸리는 시간을 추정한 값(밀리초)입니다. 트리에 있는 각 컴포넌트의 가장 최근 렌더링 시간을 합산하여 계산합니다. 이 값은 최악의 렌더링 비용(예: 초기 마운트 또는 메모화가 없는 트리)을 추정합니다. `actualDuration`과 비교하여 메모화가 잘 작동하는지 확인하세요.
- **`startTime`** : React가 현재 업데이트 렌더링을 시작한 시점에 대한 숫자 타임스탬프입니다.
- **`endTime`** : React가 현재 업데이트를 커밋한 시점의 타임스탬프입니다. 이 값은 커밋의 모든 프로파일러 간에 공유되므로 원하는 경우 그룹화할 수 있습니다.

## 사용법

### **프로그램적으로 렌더링 성능 측정하기**

React 트리에 `<Profiler>` 컴포넌트를 감싸서 렌더링 성능을 측정합니다.

```typescript
<App>
  <Profiler id="Sidebar" onRender={onRender}>
    <Sidebar />
  </Profiler>
  <PageContent />
</App>
```

### 노트

`<Profiler>`를 사용하면 프로그램적으로 측정값을 수집할 수 있습니다. 대화형 프로파일러를 찾고 있다면 React 개발자 도구의 프로파일러 탭을 사용해 보세요. 브라우저 확장 프로그램과 유사한 기능을 노출합니다.