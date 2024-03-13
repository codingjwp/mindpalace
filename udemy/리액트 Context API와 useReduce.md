# 리액트 Context API와 useREduce

## Prop Drilling 

리액트는 APP 컨포넌트에서 트리형태로 컴포넌트가 구축됩니다.

그로 인해 데이터를 공유하기 위해서는 컴포넌트에서 하위 컴포넌트로 전달을 해야하는데 이를  
여러 층의 컴포넌트로 prop를 전달하는 것을 `Prop Drilling`라고 합니다.

## Prop Drilling 해결방법

- **Component Composition(컴포넌트 합성)** : 하위에 전달한 props를 사용하는 부분을 상위 컴포넌트에 선언 해서 사용하는 방법

컴포넌트 합성 시 `{children}`이 필요합니다.  
하지만 이 방법은 모든 컴포넌트에 사용하기에는 무리가 있습니다.

```javascript
// 하위 컴포넌트
export default function Shop({ onAddItemToCart }) {
  return (
    <section id="shop">
      <h2>Elegant Clothing For Everyone</h2>

      <ul id="products">
      {/* props 내용인 onAddItemToCart를 사용하는 부분*/}
        {DUMMY_PRODUCTS.map((product) => (
          <li key={product.id}>
            <Product {...product} onAddToCart={onAddItemToCart} />
          </li>
        ))}
      </ul>
    </section>
  );
}

// 상위 컴포넌트
<Shop onAddItemToCart={handleAddItemToCart} />
```

```javascript
// 변경된 하위 컴포넌트 
export default function Shop({ children }) {
  return (
    <section id="shop">
      <h2>Elegant Clothing For Everyone</h2>
      <ul id="products">
        {children}
      </ul>
    </section>
  );
}
// 변경된 상위 컴포넌트
<Shop>
  {DUMMY_PRODUCTS.map((product) => (
    <li key={product.id}>
      <Product {...product} onAddToCart={onAddItemToCart} />
    </li>
  ))}
</Shop>
```


## Context API

컴포넌트 간의 데이터 공유를 편하게 해줍니다.

```javascript
import {createContext} from 'react';

export const CartContext = createContext({
  items:[]
})
```

`.`부분은 특정 오브젝트 안에 중첩된 속성이 실질적인 컴포넌트가 되는 경우 

```javascript
import {CartContext} from '';

<CartContext.Provider>
</CartContext.Provider>
```

## useReduce

하나 또는 그 이상의 복잡한 값을 더 단순한 형태로 만드는 함수  
합쳐질 상태와 상태를 변환 시켜줄 `dispatch` 함수  

```javascript
import {useReducer} from 'react';

const [shoppingCartState, shoppingCartDispatch] = useReducer();
```

리듀서로 만드는 함수는 함수가 재생성 되지 않도록 컴포넌트 밖에 생성을 합니다.

```javascript
```
