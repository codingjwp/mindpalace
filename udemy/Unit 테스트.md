# 앱 테스트 (Unit 테스트)

## 왜??

### 수동적 테스트(Manual Testring)

일반적으로 코드를 실행하고 브라우저를 통해 보면서 테스트하는 작업입니다.

하지만 이런 테스트같은 경우 오류가 쉽게 나며 왜냐하면 수동으로는 가능한 모든  
조합과 시나리오를 테스트하기 힘들기 때문입니다.

### 자동적 테스트(Automated Testing)

물론 자동적 테스트도 자동 테스트를 대체하는 것은 아닙니다.

추가적으로 코드를 작성해서  실행되면서 다른 코드를 테스트 합니다.  
전체 애플리케이션을 테스트하는 코드를 작성하기 때문에 항상 모든 것을 테스트 할 수 있습니다.

## 다양한 종류의 테스트

- **Unit Tests:** 가장 작은 단위에 대한 테스트 작성하는 것 입니다. (함수나 컴포넌트)
- **Integration Test:** 통합 테스트는 여러개의 구성 요소의 조합을 테스트 합니다. (유닛 테스트와 헷갈리 때도 존재하나 단위 테스트보다는 갯수가 작습니다.)
- **End-to-End (e2e) Tests:** 애플리케이션 전체 워크플로우를 테스트합니다. 실제로 사람이 애플리케이션에 수행하는 작업을 재현합니다. (전체 시나리오)

### 무엇을 테스트해야 하는가???  

가장 작은 단위의 구성요소를 테스트합니다.  
각각의 주요 사항만 테스트합니다.

### 어떻게 테스트를 해야하는가??  
발생할 수 있는 성공 및 오류 사례를 테스트합니다.  
드물지만 가능한 시나리오와 결과도 테스트합니다.

## 테스트를 위한 세가지 과정

테스트를 작성할때 일반적으로 세가지 과정을 거칩니다. (Three "A"s)  

- **Arrange(준비):** 테스트하고자 하는 컴포넌트를 렌더링  
- **Act(실행):** 실제로 테스트하고자 하는 내용  
- **Assert(결과):** 브라우저에 나오는 아웃 풋이 예상과 같은지 보는 부분

기본적인 테스트 코드 

```javascript
const Greeting = () => {
  return (
    <div>
      <h2>Hello World!</h2>
      <p>It's good to see you</p>
    </div>
  );
};

export default Greeting;
```

`{ exact: false}`를 작성하여 완벽하게 매칭 되지 않아도 통과하도록 하였습니다.  
만약 해당 부분이 삭제 시 **Hello World** 부분에 **!**가 없기 때문에 에러가 납니다.

```javascript
import Greeting from "./Greeting"
import { render, screen, expect } from '@testing-library/react';

test('renders Hello World as a text', () => {
  // Arrange
  render(<Greeting />);

  // Act
  // ... nothing

  // Assert
  const helloworldElement = screen.getByText('Hello world', { exact: false});
  expect(helloworldElement).toBeInTheDocument();
})
```

- **Test Suites:** 그룹으로 지정하는 테스트
- **Tests:** 테스트 종류

## HTTP 데스트 요청 시

일반적으로 서버로 HTTP 요청을 하지 않습니다. 해당 방법은 테스트시 많은 트래픽을 발생하거나  
잘못된 데이터가 서버로 전송될 수도 있기 때문입니다.

그걸 피하기 위해 테스트를 작성할 때는 진짜 요청을 전송하지 않거나 테스트 서버로 요청을 합니다.

요청으로 인한 컴포넌트가 올바르게 작동하는지에 대해서만 테스트를 합니다.


## 가짜 mock 만들기 

가짜 mock를 만들어서 테스트를 해야 트래픽 및 서버 문제가 발생하지 않스빈다.

```javascript
import { render, screen  } from '@testing-library/react';
import Async from './Async';

describe('Async component', () => {
  test('renders posts if request succeds', async () => {
    window.fetch = jest.fn();
    window.fetch.mockResolvedValueOnce({
      json: async () => [{ id: 'p1', title: 'First post'}]
    });
    render(<Async />)

    // const listItemElements =  screen.getAllByRole('listitem');  // 즉시 screen의 요소를 찾기 때문에 [] 빈배열로 인해 에러 발생합니다.
    const listItemElements = await screen.findAllByRole('listitem'); // 디폴트 1초로 1초 동안 해당 screen 내용이 있는지 찾아봅니다.
    expect(listItemElements).not.toHaveLength(0);
  })
})
```