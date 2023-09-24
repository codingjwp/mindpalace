# Your first component

## 배울 수 있는 정보

- 구성요소란 무엇일까?
- React 애플리케이션에서 컴포넌트가 수행하는 역할
- React 컴포넌트 작성하는 방법.

React를 사용하면 `MarkUp`, `CSS`, `Typescript`를 앱의 재사용 가능한 UI 요소인 사용자 정의 '컴포넌트'로 결합할 수 있습니다.

## 컴포넌트의 정의

React 컴포넌트는 Markup으로 뿌릴 수 있는 Typescript 함수입니다.

### 1단계: 컴포넌트 내보내기

- `export default` : 컴포넌트 가져오기 및 내보내기

### 2단계: 함수 정의

- React 컴포넌트는 이름을 **대문자로** 시작해야 한다.

### 3단계: 마크업 추가

```typescript
return <img src="" alt="" />;
```

**주의사항**
makeup이 `return` 키워드와 같은 줄에 있지 않으면 괄호로 묶기

- 괄호 없을 시 다음 줄의 모든 코드 `return` 무시

```typescript
return (
<div>
 <img src="" alt="" />
</div>
)
```

## 컴포넌트 사용법

```typescript
const Profile = () = > {
 return (<img src="" alt="" />);
}

export default function Gallery() {
 return (
  <section>
   <h1>Gallery</h1>
   <Profile />
   <Profile />
   <Profile />
  </section>
 )
} 
```

- `<section>` 소문자로 HTML태그를 참조.
- `<Profile />` : React 컴포넌트 사용

### 브라우저 표시되는 내용

```typescript
<section>
 <h1>Gallery</h1>
 <img src="" alt="" />
 <img src="" alt="" />
 <img src="" alt="" />
</section>
```

## 컴포넌트 중첩 및 구성

- 컴포넌트를 한번 정의하면 원하는 만큼 여러번 적용 가능

**주의 사항**  
컴포넌트는 다른 구성요소를 렌더링 할 수 있지만 정의를 중첩해서는 안됨. 느리고 버그를 유발

```typescript
export default function Gallery() {
 function Profile(){
 }
}
```

최상위 수준에서 모든 컴포넌트를 정의

```typescript
export default function Gallery() {
}

function Profile(){
}
```

⏮️ Previous: [Describing the UI](./000-리액트%20Describing%20the%20UI.md)

⏭️ Next : [Importing and Exporting Components](./002-리액트%20Importing%20and%20exporting%20components.md)
