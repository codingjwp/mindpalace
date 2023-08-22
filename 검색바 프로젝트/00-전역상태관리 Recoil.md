# 전역상태관리 Recoil

## 주요 개념

### 개요

공유상태(`atoms`)에서 순수 함수(`selectors`)를 거쳐 React 컴포넌트로 내려가는 Data-Flow Graph를 만들수 있습니다.  
**Atoms**는 컴포넌트가 구독할 수 있는 상태의 단위입니다.  
**Selectors**는 이 상태를 동기, 비동기적으로 변경합니다.

### 동기

Recoil은 React tree에 [직교(orthogonal)](#직교orthogonal)하며 본질적으로 tree에 붙어있는 [방향 그래프(directed graph)](#방향-그래프directed-graph)를 정의합니다.  

#### 접근방식

- 공유상태(shared state)도 React의 내부상태처럼 간단한 get/set 인터페이스를 가질 수 있도록 [보일러플레이트(boilerplate-free API)](#보일러플레이트boilerplate-free-api)를 제공하며 필요한 경우 reducers로 캡슐화도 있습니다.
- [동시성 모드(Concurrent Mode)](#동시성-모드concurrent-mode)를 비롯한 새로운 React기능과 호환 가능성을 갖습니다.
- 상태 정의는 [점진적(incremental)](#점진적incremental)이고 분산되어 있어 코드 분할이 가능합니다.
- 상태를 사용하는 컴포넌트를 수정하지 않고 파생된 데이터로 상태를 대체할 수 있습니다.
- 파생된 데이터는 이를 사용하는 컴포넌트를 수정하지 않고도 동기식과 비동기식 사이를 이동할 수 있습니다.
- 내비게이션을 최상위 개념으로 취급하여 링크에서 상태 전환을 인코딩할 수도 있습니다.
- 이전 버전과 호환되는 방식으로 전체 애플리케이션 상태를 쉽게 유지할 수 있으므로 애플리케이션이 변경되더라도 지속된 상태가 유지될 수 있습니다.

#### 이해하기 힘든 단어들

##### **직교(orthogonal)**

- ***두 특성이 완전 독립이다***. 서로 완전 다른 성질을 가지고 있기 때문에 Recoil의 특성을 뽑아내도 React tree에는 전혀 영향 없습니다.

##### **방향 그래프(directed graph)**

- ***방향성***(정점에서 정점으로 이동할수 있는 방향)을 가진 그래프 입니다.

##### **보일러플레이트(boilerplate-free API)**

- 최소한의 변경으로 여러 곳에서 **재사용**되는 코드 예를 들어 react에서는 CRA(create-react-app)이 있습니다.

##### **동시성 모드(Concurrent Mode)**

- react에서 동시성 모드란 렌더링 작업의 우선순위를 고려하고 나누어 처리합니다

##### **점진적(incremental)**

- 필요한 부분만 선택적으로 상태 관리에 적용할 수 있기 때문에 전체 프로젝트 구조를 크게 변경하지 않아도 됩니다. 따라서 프로젝트의 규모에 관계없이 Recoil을 사용하여 상태 관리를 일부분만 개선하거나, 점차 확장해 나갈 수 있습니다.

### Atoms

상태의 단위이며 업데이트와 구독이 가능합니다. atom가 업데이트되면 각각 구독된 컴포넌트는 새로운 값을 반영하여 다시 렌더링 되며 동일한 atom이 여러 컴포넌트에서 사용되는 경우 모든 컴포넌트가 상태를 공유합니다.

#### atom 함수

- atom은 고유한 키가 필요하며 두 개의 atom이 같은 키를 갖는 것은 오류이기 때문에 키값은 전역적으로 고유한 값을 가져야 한다.
- defalut는 처음 사용하는 컴포넌트에서 전달 받는 초기값을 정의한다.

```typescript
const textLabelState = atom({
    key: 'textLabelState',
    default: "text",
});
```

- useRecoilState() : atom을 읽고 쓰기위한 훅
- input에 value값이 변경이 나면 textLabelState의 상태가 변경된다.

```typescript
function TextLabel() {
    const [textLabel, setTextLabel] = useRecoilState(textLabelState);
    const textLabelChange = (e: MouseEvent) => {
        setTextLabel(e.target.value);
    }
    return (
        <input type="text" value={textLabel} onChange={textLabelChange} />
    )
}
```

### Selectors

atoms나 다른 selectors를 입력으로 받아드리는 순수 함수, 상위 atoms또는 selectors가 업데이트 되면 하위 selectors함수도 다시 실행된다.  
컴포넌트들은 selector을 구독할 수 있으며 selectors가 변경되면 해당 컴포넌트도 다시 렌더링 된다.  
상태를 기반으로하는 파생데이터를 계산하는데 사용, 최소한의 상태만 atoms에 저장하고 다른 파생되는 데이터는 selectors에 명시한 함수를 통해 효율적으로 계산합니다.

#### selector 함수

- 계산될 함수 **get**

```typescript
const textLabelColorState = selector({
    key: 'textLabelColorState',
    get: ({get}) => {
        const text = get(textLabelState);
        const randomIndex = Math.floor(Math.random() * 3);
        const colorArray = ['red', 'blue', 'green'];
        if (text === '')
            return 'black';
        return colorArray[randomIndex];
    }
})
```

- **useRecoilValue**를 통해 값을 반환 받아 적용한다.
- selector도 set 하는 부분이 있으나 요기서는 간단하게만 설명하고 이후 작성

```typescript
function TextLabel() {
    const [textLabel, setTextLabel] = useRecoilState(textLabelState);
    const textcolor = useRecoilValue(textLabelColorState);
    const textLabelChange = (e: MouseEvent) => {
        setTextLabel(e.target.value);
    }
    return (
        <div>
            <input type="text" value={textLabel} onChange={textLabelChange} />
            <span style={`color: ${textcolor}`}></span>
        </div>
    )
}
```

## 주의점

### ES5 지원

Recoil 빌드는 **ES5**로 트랜스파일 되지 않는다.  
Recoil을 **ES5**와 사용하는 것은 지원하지 않는다.  
ES6 기능을 제공하지 않는 브라우저를 지원하는 경우 `Babel`로 컴파일하고 `preset @babel/preset-env` 수행이 가능하나 문제가 발생할 수 있다.  
이런 경우 성능상 문제를 야기할 수 있다.

### EsLint

프로젝트에서 `eslint-plugin-react-hooks`를 사용하는 경우  
`useRecoilCallback`을 additionalHooks 목록에 추가하는 것이 좋다.  
추가하는 경우 전달된 종속성이 잘못됬을 경우 경고를 표시하고 해결방안을 제시한다.

```json
{
  "plugins": ["react-hooks"],
  "rules": {
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": [
      "warn",
      {
        "additionalHooks": "useRecoilCallback"
      }
    ]
  }
}
```
