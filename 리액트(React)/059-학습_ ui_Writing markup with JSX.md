# Writing markup with JSX

> JSX로 마크업 작성

## 배울려는 내용

- React가 마크업과 렌더링 로직을 혼합하는 이유
- JSX가 HTML과 다른 점
- JSX로 정보를 표시하는 방법

## JSX : JavaScript에 마크업 넣기

Javascript가 HTML을 담당. 그렇기 때문에 React에서는 렌더링 로직과 마크업이 같은 위치의 컴포넌트에 존재.

![image](https://github.com/codingjwp/mindpalace/assets/113403155/28443719-4d87-446f-bf33-b0757efe608c)  

출처 : [react 공식 문서](https://react.dev/learn/writing-markup-with-jsx#jsx-putting-markup-into-typescript)  


JSX와 React는 서로 다른 두 가지. 종종 함께 사용되기도 하지만 서로 [독립적으로 사용.](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html#whats-a-jsx-transform) JSX는 구문 확장이고 React는 자바스크립트 라이브러리.

## JSX의 규칙

JSX가 HTML보다 더 엄격하고 몇 가지 **더 많은 규칙**을 가짐.

**대부분의 경우 React의 화면 오류 메시지는 문제가 있는 위치를 찾는 데 도움.**

### 1. 단일 루트 요소를 반환합니다

단일 상위 태그로 요소를 래핑

```typescript
<div>{....}</div>
```

`<div>` 를 원하지 않는 다면 `<></>` 를 사용.

```typescript
<>{...}</> or <Fragment></Fragment>
```

브라우저 HTML 트리에 흔적을 남기지 않고 항목을 그룹화 가능.

JSX 태그를 래핑 해야하는 이유는??

- JSX는 HTML처럼 보이지만 내부적으로 일반 Javscript 객체로 변환.
- 두 객체를 배열로 래핑하지 않으면 객체를 반환할 수 없음.

### 2. 모든 태그를 닫습니다

html 태그 에서 `<br>` `<img>` 같은 경우 JSX에서는 `<br/>` `<img />` 로 명시적으로 닫아야함.

### 3. camelCase 거의 모든 것

JSX로 작성된 속성은 Typescript 객체의 키로 쓰이고 컴포넌트에서 이러한 속성을 변수로 읽어들이고 싶은 경우가 있지만 Javscript에는 변수 이름에 제한이 있기 때문.

[HTML을 JSX로 변환기](https://transform.tools/html-to-jsx)
