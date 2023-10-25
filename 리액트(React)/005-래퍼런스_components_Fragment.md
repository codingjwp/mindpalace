# `<Fragment> (<>...</>)`

종종 `<>…</>` 구문으로 사용하는 `<Fragment>`는 감싸는 노드 없이도 엘리먼트를 그룹화할 수 있게 해줍니다.

```typescript
<>
  <OneChild />
  <AnotherChild />
</>
```

## 참조

### `<Fragment>`

단일 엘리먼트가 필요한 상황에서 엘리먼트들을 `<Fragment>`로 묶어 함께 그룹화합니다. `<Fragment>`로 엘리먼트들을 그룹화하더라도 실제 DOM에는 아무런 영향을 주지 않으며, 그룹화하지 않은 것과 동일합니다. 빈 JSX 태그 `<></>`는 대부분의 `<Fragment>`의 축약 표현입니다.

### props

- `key`:   명시적인 `<Fragment>` 구문으로 선언한 Fragment에는 선태적으로 키를 추가할 수 있습니다.

### 주의사항

- Fragment에 key를 전달하려는 경우 `<>…</>`구문을 사용할 수 없습니다. `'react'` 에서 `Fragment`를 명시적으로 불러오고 `<Fragment key={yourKey}>...</Fragment>`를 렌더링해야합니다.
- `{show ? <><Child /></> : [<Child />]}` 경우 React가 `<><Child /></>`와 `[<Child />]`를 렌더링할 때 같은 위치에 있는 컴포넌트로 인식합니다.

## 사용법

### **변수에 여러 엘리먼트를 할당하기**

```typescript
function CloseDialog() {
  const buttons = (
    <>
      <OKButton />
      <CancelButton />
    </>
  );
  return (
    <AlertDialog buttons={buttons}>
      Are you sure you want to leave this page?
    </AlertDialog>
  );
}
```

### **Fragment 목록 렌더링하기**

아래코드는 `<></>`구문 대신 명시적으로 `Fragment`를 작성해야하는 상황입니다.

```typescript
function Blog() {
  return posts.map(post =>
    <Fragment key={post.id}>
      <PostTitle title={post.title} />
      <PostBody body={post.body} />
    </Fragment>
  );
}
```