# `<Suspense>`

`<Suspense>`를 사용하면 자식이 로딩을 완료할 때까지 폴백을 표시할 수 있습니다.

```typescript
<Suspense fallback={<Loading />}>
  <SomeComponent />
</Suspense>
```

## 참조

### `<Suspense>`

### props

- `children` : 렌더링하려는 실제 UI입니다. 렌더링하는 동안 `children`이 일시 중단되면 Suspense 경계가 `fallback` 렌더링으로 전환됩니다.
- `fallback` : 로딩이 완료되지 않은 경우에 실제 UI 대신 렌더링할 대체 UI입니다. 유효한 어떤 React 노드든 괜찮지만 주로 로딩 스피너나 스켈레톤과 같은 가벼운 플레이스홀더 뷰를 사용합니다

### 주의사항

- React는 처음 마운트하기 전에 일시 중단된 렌더링의 state를 보존하지 않습니다. 컴포넌트가 로드되면 React는 일시 중단된 트리의 렌더링을 처음부터 다시 시도합니다.
- Suspense가 트리에 대한 콘텐츠를 표시하고 있다가 다시 일시 중단된 경우, 그 원인이 된 업데이트가 `startTransition`이나 `useDeferredValue`로 인한 것이 아니라면 `fallback`이 다시 표시됩니다.
- React가 다시 일시 중단되어 이미 표시된 콘텐츠를 숨겨야 하는 경우, 콘텐츠 트리에서 layout Effect를 클린업 합니다. 콘텐츠가 다시 표시될 준비가 되면 React는 layout Effect를 다시 실행합니다.
- React에는 Suspense와 통합된 스트리밍 서버 렌더링 및 선택적 Hydration과 같은 내부 최적화가 포함되어 있습니다. [아키텍처 개요](https://github.com/reactwg/react-18/discussions/37) 및 [기술 강연](https://www.youtube.com/watch?v=pj5N-Khihgc)에서 자세히 알아보세요.

## 사용법

**오직 Suspense를 도입한 데이터 소스에서만 Suspense 컴포넌트를 활성화할 수 있습니다.** 여기에는 다음이 포함됩니다.

- Relay 및 Next.js와 같은 Suspense 도입 프레임워크를 사용한 데이터 페칭
- `lazy`를 사용한 지연 로딩 컴포넌트 코드

## 문제해결

### **업데이트 중에 UI가 폴백으로 대체되는 것을 방지하려면 어떻게 해야 할까요?**

표시되는 UI를 폴백으로 대체하면 사용자 환경이 불안정해집니다. 이는 업데이트로 인해 컴포넌트가 일시 중단되었는데 가장 가까운 Suspense 경계에는 이미 콘텐츠가 표시되고 있을 때 발생할 수 있습니다. 

이런 일이 발생하지 않도록 하려면 `startTransition`을 사용하여 업데이트를 긴급하지 않은 것으로 표시하세요. 트랜지션이 진행되는 동안 React는 원치 않는 폴백이 나타나지 않기에 충분한 데이터가 로드될 때까지 기다립니다:

```typescript
function handleNextPageClick() {
  // 이 업데이트가 일시 중단되어도 이미 표시된 콘텐츠를 숨기지 않습니다
  startTransition(() => {
    setCurrentPage(currentPage + 1);
  });
}
```