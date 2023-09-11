# Sharing State Between Components

> 컴포넌트 간의 state 공유 

가끔 **두 컴포넌트의 state가 항상 함께 변경**되기를 원할 때 두 컴포넌트에서 state를 제거하고 가장 가까운 공통 부모로 이동한 다음 props를 통해 전달. 이러한 기능을 **state 끌어올리기**라고 하며 React코드를 작성할 때 가장 흔히 하는 작업 중 하나.

## 배울 내용들

1. state를 부모 컴포넌트로 끌어올려 컴포넌트 끼리 공유하는 방법
2. 제어 컴포넌트와 비제어 컴포넌트

## 예조로 알아 보는 state 끌어 올리기

![Untitled](Sharing%20State%20Between%20Components%20fbf76508682348d488cde5e7d47b42ab/Untitled.png)

하나의 `Panel`에 있는 `isActive` 버튼을 눌러도 다른 `Panel`에는 영향을 주지 않고 독립적으로 동작

이 기능을 **한 번에 하나의 패널만 열리도록 변경**, 즉 두 번째 패널을 열리게 하기 위해 첫 번째 패널이 닫혀야함

- 자식 컴포넌트에서 state **제거**
- 공통 부모 컴포넌트에 하드 코딩된 데이터를 **전달**
- 공통 부모 컴포넌트에 state를 **추가**하고 이벤트 핸들러와 함께 전달

### 스탭 1: 자식 컴포넌트에서 state 제거

`Panel`의 `isActive` 권한을 공통 부모로 이동

```javascript
const [isActive, setIsActive] = useState(false);
```

부모로 이동시킨 `isActive` state를 자식`Panel`로  props로`isActive` 전달

```javascript
function Panel({ title, children, isActive }) {
```

### 스탭 2: 공통 부모에 하드 코딩된 데이터 전달하기

`Panel`에 `isActive`에 따라 화면에 결과가 제대로 표시되는지 확인

```javascript
<Panel title="About" isActive={true}>
	{/* ... 생략 */}
</Panel>
<Panel title="Etymology" isActive={true}>
	{/* ... 생략 */}
</Panel>
```

### 스탭 3: 공통 부모에 state추가

위의 코드 경우 isActive시 두개의 패널이 동시에 보이게 됨으로 하나의 패널만 활성화하기 위해 어떤 패널이 활성화 된 패널인지 추적해야함.

```javascript
const [activeIndex, setActiveIndex] = useState(0);

<>
  <Panel
    isActive={activeIndex === 0}
    onShow={() => setActiveIndex(0)}
  >
    {/* ... 생략 */}
  </Panel>
  <Panel
    isActive={activeIndex === 1}
    onShow={() => setActiveIndex(1)}
  >
    {/* ... 생략 */}
  </Panel>
</>

```

![Untitled](Sharing%20State%20Between%20Components%20fbf76508682348d488cde5e7d47b42ab/Untitled%201.png)

### 제어 및 비제어 컴포넌트

일반적으로 일부 로컬 state를 가진 컴포넌트를 **비제어 컴포넌트**라고 함.

`isActive` state 변수가 있는 원래 `Panel` 컴포넌트는 부모가 패널의 활성화 여부에 영향을 줄 수 없기 때문에 제어되지 않음

반대로 컴포넌트의 중요한 정보가 자체 로컬 state가 아닌 props에 의해 구동되는 경우 컴포넌트가 **제어**된다고 말할 수 있음

이렇게 하면 부모 컴포넌트가 그 동작을 완전히 지정할 수 있습니다. 최종 `Panel` 컴포넌트에는 `isActive` props가 있으며, `Accordion` 컴포넌트에 의해 제어됨.

컴포넌트를 작성할 때는 (props를 통해) 컴포넌트에서 어떤 정보를 제어해야 하는지, (state를 통해) 어떤 정보를 제어하지 않아야 하는지 고려.

## ****각 state의 단일 진실 공급원(SSOT)****

많은 컴포넌트는 고유한 state를 가지고 있으며. 일부 state는 입력값과 같이 leaf 컴포넌트(트리의 맨 아래에 있는 컴포넌트)에 가깝게 위치하며 다른 state는 상단에 더 가깝게 위치 할 수 있습니다.

**각 고유한 state들에 대해 해당 state를 소유하는 컴포넌트를 선택하게 되며** 이 원칙은 **단일 진실 공급원** 이라고 함. 이는 모든 state가 한 곳에 있다는 뜻이 아니라 각 state마다 해당 정보를 소유하는 특정 컴포넌트가 있다는 뜻이며 컴포넌 간에 공유하는 state를 복제하는 대신 공통으로 공유하는 부모로 끌어올려서 필요한 자식에게 전달.

⏮️ Previous : [Choosing the State Structure](./023-리액트%20Choosing%20the%20State%20Structure.md)

⏭️ Next : [Preserving and Resetting State](./025-리액트%20Preserving%20and%20Resetting%20State.md)