# Thinking in React

[**리액트 공시문서 Thinking in React**](https://react.dev/learn/thinking-in-react)

React를 사용하여 UI를 구출할때 먼저 이를 구성요소라는 조각으로 분리.

## **Step 1: Break the UI into a component hierarchy**

> 1단계: Ui를 구성요소 계층 구조로 나누기.
> 
- Progamming : 구성요소는 이상적으로 한 가지 작업만 수행해야합니다 (단일 책임 원칙)
- CSS: 클래스 선택자를 무엇으로 만들 것인지 고려.
- Design: 디자인 레이어를 어떻게 구성할지 고려.


다섯 가지의 구성요소

구성 요소를 식별했으므로 이를 계층 구조로 정렬

- `FilterableProductTable`
    - `SearchBar`
    - `ProductTable` (자체 구성요소가 아니며 이것은 구성요소로 포함할지 말지는 선호 문제)
        - `ProductCategoryRow`
        - `ProductRow`

## **Step 2: Build a static version in React**

> 2단계: React에서 정적 버전 빌드
> 

가장 간단한 접근 방식은 **인터랙티브을** 적용하지 않고 데이터 모델에서 UI를 렌더링하는 버전을 구축

**Static(정적)** 버전을 먼저 구축한후 인터랙티브를 적용하는게 아직은 쉬운 경우가 많음.

<aside style="background-color: #befae9; border-radius: 10px; padding: 10px;">
<img src="https://www.notion.so/icons/light-bulb_green.svg" alt="https://www.notion.so/icons/light-bulb_green.svg" width="40px" />
<h3 style="display: inline; color: green;"><b>Static 버전을 빌드 하는데 state 사용금지</b></h3>
</aside>

**하향식** : 높은 구성요소 부터 구축하며 `(FilterableProductTable)` 내려가는 방식 (간단한 프로젝트)  
**상향식** :  낮은 구성요소`(ProductRow)` 부터 구축하며 올라 가는 방식 (대규모 프로젝트)  
**단방향 데이터 흐름(one-way data flow)** : 최상위 Component에서 하단 Component로 흘러내리는 방식.

## **Step 3: Find the minimal but complete representation of UI state**

> 3단계: 최소한의 완전한 UI 상태 표현 찾기
> 

UI를 대화형으로 만들기 위해 사용자가 기본 Data 모델을 변경할 수 있어야 하며 이를 위해 state를 사용합니다.

**state**: 앱이 기억해야하는 최소한의 변경 데이터 집합.

상태를 구조화 할때 가장 중요한 원칙 [DRY(반복하지 않기)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

<aside style="background-color: #befae9; border-radius: 10px; padding: 10px;">
<img src="https://www.notion.so/icons/light-bulb_green.svg" alt="https://www.notion.so/icons/light-bulb_green.svg" width="40px" />
<h3 style="display: inline; color: green;">애플리케이션에 필요한 <b>최소한의 표현</b> 을 파악하고 필요에 따라 모든것을 계산 합니다.</h3>

<br>

ex ) 쇼핑 목록을 작성하는 경우 항목의 상태를 배열로 저장.  
목록의 항목 수도 표시하려면 항목 수를 다른 상태값으로 저장하지말고 배열의 길이를 읽기.
</aside>

<br>

데이터 조각

1. 원래 제품 목록 **(props로 전달 됨으로 상태가 아님)**
2. 사용자가 입력한 검색어 **(시간에 지남에 따라 변하고 어떤 것도 계산할 수 없기에 상태)**
3. 체크박스 값 **(시간에 지남에 따라 변하고 어떤 것도 계산할 수 없기에 상태)**
4. 필터링 된 제품 목록 **(원래 제품목록으로 계산할 수 있으므로 상태가 아님)**

<aside style="background-color: #befae9; border-radius: 10px; padding: 10px;">
<img src="https://www.notion.so/icons/light-bulb_green.svg" alt="https://www.notion.so/icons/light-bulb_green.svg" width="40px" /> 
<h3 style="display: inline; color: green;"><b>상태가 되는 것은 무엇일까??? (전부 상태가 아니다)</b></h3>

- 시간이 지나도 변함없이 유지 되는가?
- props를 통해 부모로 전달되는가?
- 기존 state나 props로 계산할 수 있는가?
</aside>

### Props vs State

props: 전달하는 인수와 같습니다. 상위 구성요소가 하위 구성요소에 데이터를 전달하고 모양을 정의 할 수 있습니다.

state: 구성요소의 메로리와 같습니다. 일부 정보를 추적하고 상호작용을 통해 이를 변경할 수 있습니다.

## **Step 4: Identify where your state should live**

<aside style="background-color: #befae9; border-radius: 10px; padding: 10px;">
<img src="https://www.notion.so/icons/light-bulb_green.svg" alt="https://www.notion.so/icons/light-bulb_green.svg" width="40px" />
<h3 style="display: inline; color: green;"><b>애플리케이션 state에 대해</b></h3>

1. 해당 상태를 기반으로 무언가를 렌더링하는 모든 컴포넌트를 식별.
2. 가장 가까운 공통 상위 컴포넌트, 즉 계층 구조에서 모든 컴포넌트 위에 있는 컴포넌트를 찾기.
3. 상태가 어디에 위치할지 결정
    1. 흔히 상태를 공통 부모 컴포넌트에 직접 넣을 수 있습니다.
    2. 상태를 공통 부모 위에 있는 컴포넌트에 넣을 수도 있습니다.
    3. 상태를 소유하기에 적합한 컴포넌트를 찾을 수 없는 경우 상태를 보유하기 위한 컴포넌트를 새로 만들어 공통 부모 컴포넌트 위의 계층 구조 어딘가에 추가하세요.
</aside>

<br>

1. 상태를 사용하는 구성 요소를 식별.
    1. `ProductTable` : 해당 상태를 기준으로 제목, 목록 필터링
    2. `SearchBar` : 해당 상태를 표시
2. 공통 상위 요소 찾기
    1. `FilterableProductTable` : 두 구성요소가 공유하는 첫번째 상위 구성요소.
3. 상태가 어디에 있는지 결정.
    1. `FilterableProductTable` : 

## **Step 5: Add inverse data flow**

> 5단계: 역방향 데이터 흐름 추가
> 

단방향 데이터로 흐리지만 사용자 입력에 따라 상태를 변경하려면 데이터가 다른 방향으로 흐르는 것을 지원해야 합니다.

```jsx
function FilterableProductTable({ products }) {
  const [filterText, setFilterText] = useState('');
  const [inStockOnly, setInStockOnly] = useState(false);

  return (
    <div>
      <SearchBar 
        filterText={filterText} 
        inStockOnly={inStockOnly}
        onFilterTextChange={setFilterText}
        onInStockOnlyChange={setInStockOnly} />

// SearchBar onChange 이벤트 핸들러 
<input 
  type="text" 
  value={filterText} 
  placeholder="Search..." 
  onChange={(e) => onFilterTextChange(e.target.value)} />
```

Previous : [Tutorial Tic-Tac-Toe](./001-리액트%20Tutorial%20Tic-Tac-Toe.md)  
Next : [Installation](./003-리액트%20Installation.md)