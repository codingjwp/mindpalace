# Passing Data Deeply with Context

> context로 데이터 깊숙이 전달하기

일반적으로 부모 에서 자식으로 props를 통해 정보를 전달.

하지만 중간에 여러 컴포넌트를 거쳐야 하거나 앱의 여러 컴포넌트가 동일한 정보를 필요한 경우 props로 전달하면 불편해질 수 있음.

`Context`를 사용하면 부모가 props를 통해 명시적으로 전달 하지 않고 깊이 여부와 무관하게 그 아래 트리의 모든 컴포넌트에 일부 정보를 사용할 수 있음.

## 배울 내용들

1. Prop drilling이란 무엇인가
2. 반복적인 prop 전달을 Context로 대체하는 방법
3. Context의 일반적인 사용 사례
4. Context에 대한 일반적인 대안

## props 전달 문제

트리 깊숙이 prop을 전달해야 하거나 많은 컴포넌트에 동일한 prop이 필요한 경우 prop 전달이 **장황하고 불편해질 수** **있음**. 

가장 가까운 공통 조상이 데이터가 필요한 컴포넌트에서 멀리 떨어져 있을 수 있으며, state를 그렇게 높이 끌어올리면 **prop drilling** 이라고 불리는 상황이 발생할 수 있음.

### prop drilling

props를 오로지 하위 컴포넌트로 전달하는 용도로만 쓰이는 컴포넌트를 거치면서 React Comoinnt트리의 한 부분에서 다른 부분으로 데이터를 전달하는 과정

![Untitled](Passing%20Data%20Deeply%20with%20Context%20b3a424ec67df4d5ab0806dbb6df24e25/Untitled.png)

## Context: props 전달의 대안

Context를 사용하면 상위 컴포넌트가 그 아래 전체 트리에 데이터를 제공할 수 있음.

아래 코드는 `level` props을 각 `<Heading>`에 개별적으로 전달

```javascript
<Section>
  <Heading level={3}>About</Heading>
  <Heading level={3}>Photos</Heading>
  <Heading level={3}>Videos</Heading>
</Section>
```

이렇게 props로 전달 하는 것보다 `<Section>`에 lavel prop을 전달하고 `<Heading>` 에서 제거 할 수 있으면 좋을 것 같음.

```javascript
<Section level={3}>
  <Heading>About</Heading>
  <Heading>Photos</Heading>
  <Heading>Videos</Heading>
</Section>
```

하지만 `<Heading>` 컴포넌트가 가장 가까운 `<Section>`의 level을 어떻게 알 수 있을까?? **그러기 위해서는 자식이 트리 위 어딘가에 데이터를 요청 할 수 있는 방법이 필요.**

props 만으론 부족하며 context가 중요한 역할을 함.

1. context를 생성
2. 데이터가 필요한 컴포넌트에 해당 context를 사용
3. 데이터를 지정하는 컴포넌트에서 해당 context를 제공

context는 멀리 떨어져 있는 상위 트리라도 그안에 있는 전체 트리에 일부 데이터를 제공할 수 있게 해줌.

![Untitled](Passing%20Data%20Deeply%20with%20Context%20b3a424ec67df4d5ab0806dbb6df24e25/Untitled%201.png)

### 스탭 1: Context 만들기

컴포넌트에서 사용할 수 있도록 파일에서 내보내기를 해야함.

```javascript
import { createContext } from 'react';

export const LevelContext = createContext(1);
```

`createContext`의 유일한 인수는 **기본값.**

여기서 `1` 은 가장 큰 제목 수준을 의미 하지만 모든 종류의 값을 전달 가능

### 스탭 2: Context 사용하기

React와 context에서 `useContext` 훅을 가져옴

```javascript
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';
```

props에서 가져오던 level을 지우고 useContext를 사용하여 LevelContext에서 값을 읽기, useContext는 훅으로 컴포넌트 최상단에만 호출 가능.

```javascript
export default function Heading({children}) {
	const level = useContext(LevelContext);
}
```

**`useContext`는 React에게 `Heading` 컴포넌트가 `LevelContext`를 읽기를 원한다고 알려줌.**

context를 제공하지 않으면 React는 이전 단계에서 지정한 기본 값을 사용.

### 스탭 3: Context 제공하기

**context provider로 감싸** `LevelContext`를 제공

```javascript
import { LevelContext } from './LevelContext.js';

export default function Section({level, children}) {
	return (
		<section className="section">
			<LevelContext.Provider value={level}>
				{children}
			</LevelContext>
		</section>
	)
}
```

React에게 “이 `<Section>` 안에 있는 컴포넌트가 `LevelContext`를 요청하면 이 `level`을 제공하라고 지시. 컴포넌트는 그 위에 있는 UI 트리에서 가장 가까운 `<LevelContext.Provider>`의 value 값을 사용.

## 동일한 컴포넌트에서 Context 사용 및 제공

하지만 여전히 각 section의 level을 수동으로 지정해야 함.

context를 사용하면 위의 컴포넌트에서 정보를 읽을 수 있으므로 각 `Section`은 위이 `Section`에서 `level`을 읽고 `level + 1`을 자동으로 아래로 전달 할 수 있음.

```javascript
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Section({ children }) {
  const level = useContext(LevelContext);
  return (
    <section className="section">
      <LevelContext.Provider value={level + 1}>
        {children}
      </LevelContext.Provider>
    </section>
  );
}
```

이렇게 변경하면 level prop을 `<Section>`이나 `<Heading>` 모두에게 전달할 필요가 없음

```javascript
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
			{/* ... 생략*/ }
      <Section>
				{/* ... 생략*/ }
        <Section>
					{/* ... 생략*/ }
          <Section>
					{/* ... 생략*/ }
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

### 노트

중첩된 컴포넌트가 context를 재정의하는 방법을 시각적으로 보여주기 위해 제목 level을 사용 하였음.

하지만 context는 다른 많은 사용 사례에도 유용. 

context를 사용하여 현재 색상 테마, 현재 로그인한 사용자 등 전체 하위 트리에 필요한 모든 정보를 전달할 수 있음.

## Context는 중간 컴포넌트를 통과합니다.

context를 제공하는 컴포넌트와 context를 사용하는 컴포넌트 사이에 원하는 만큼의 컴포넌트를 삽입할 수 있음

**Context를 사용하면 “주변 환경에 적응”하고 렌더링되는 *위치* (*context*)에 따라 다르게 표시되는 컴포넌트를 작성할 수 있음**

context가 작동하는 방식은 [CSS 속성 상속](https://developer.mozilla.org/en-US/docs/Web/CSS/inheritance)을 떠올리게 할 수 있음.

CSS에서는 `<div>`에 `color: blue`을 지정할 수 있으며, 중간에 다른 DOM 노드가 `color: green`으로 재정의하지 않는 한 그 안에 있는 모든 DOM 노드는 아무리 깊어도 그 색을 상속받음. 

마찬가지로 React에서 위에서 오는 context를 재정의하는 유일한 방법은 children을 다른 값으로 context provider로 감싸는 것.

서로 다른 React context도 서로 재정의하지 않음.

`createContext()`로 만드는 각 context는 다른 context와 완전히 분리되어 있으며, 특정 context를 사용하거나 제공하는 컴포넌트를 함께 묶음.

하나의 컴포넌트가 문제없이 다양한 context를 사용하거나 제공할 수 있음.

## Context를 사용하기 전에

**props를 몇 단계 깊이 전달해야 한다고 해서 해당 정보를 context에 넣어야 한다는 의미는 아님.**

context 사용하기 전에 고려해야할 대안들

1. **props 전달로 시작** : 수십 개의 props를 수십 개의 컴포넌트에 전달해야 하는 경우가 드물지 않으며 이럴 경우 어떤 컴포넌트가 어떤 데이터를 사용하는지 매우 명확해짐.
2. **컴포넌트를 추출하고 JSX를 children으로 전달** : 일부 데이터를 해당 데이터를 사용하지 않는 중간 컴포넌트의 여러 레이어를 거쳐 전달한다면, 이는 종종 그 과정에서 일부 컴포넌트를 추출하는 것을 잊었다는 것을 의미. 예를 들어, `posts`과 같은 데이터 props를 직접 사용하지 않는 시각적 컴포넌트에 `<Layout posts={posts} />` 와 같은 방법 대신, Layout이 children을 prop으로 사용하도록 만들고 `<Layout><Posts posts={posts} /></Layout>`를 렌더링하면 데이터를 지정하는 컴포넌트와 데이터를 필요로 하는 컴포넌트 사이의 레이어 수가 줄어듭니다

이 두 가지 접근 방식이 모두 적합하지 않은 경우 context를 고려

## Context 사용 사례

- 테마 : 앱에서 사용자가 앱의 모양을 변경할 수 있는 경우, 앱 상단에 context provider를 배치하고 시각적 모양을 조정해야 하는 컴포넌트에서 해당 context를 사용할 수 있음.
- 현재 계정 : 많은 컴포넌트에서 현재 로그인한 사용자를 알아야 할 수  있음. 이정보를 context에 넣으면 트리 어느 곳에서나 편리하게 읽을 수 있음. 또한 일 부 앱에서는 여러 계정을 동시에 조작할 수 있음. 이러한 경우 UI의 일부를 다른 현재 계정 값으로 중첩된 provider로 감싸는 것이 편리할 수 있음
- 라우팅 : 대부분의 라우팅 솔루션은 내부적으로 context를 사용하여 현재 경로를 유지 함. 이것은 모든 링크가 활성 상태인지 아닌지를 아는 방식. 자체 라우터를 구축하는 겨우에도 이러한 방식을 사용할 수 있음.
- state 관리 : 앱에 성장함에 따라 앱 상단에 많은 state가 가까워질 수 있음. 아래에 있는 많이 떨어진 컴포넌트에서 이를 변경하고 싶을 수 있음. context와 reducer을 사용하여 복잡한 state를 관리하고 번거러움 없이 멀리 떨어진 컴포넌트에 전달하는 것이 일반적.

Context는 정적 값에만 국한되지 않으며 다음 렌더링에서 다른 값을 전달하면 React는 **아래에서 이를 읽는 모든 컴포넌트를 업데이트**!! 이것이 context가 state와 함께 자주 사용되는 이유.

일반적으로 트리의 다른 부분에 있는 멀리 떨어진 컴포넌트에서 일부 정보가 필요한 경우 context가 도움이 될 수 있음.

⏮️ Previous : [Extracting State Logic into a Reducer](./026-리액트%20Extracting%20State%20Logic%20into%20a%20Reducer.md)

⏭️ Next : [Scaling Up with Reducer and Context](./028-리액트%20Scaling%20Up%20with%20Reducer%20and%20Context.md)