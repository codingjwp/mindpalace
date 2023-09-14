# Passing props to a component

> 컴포넌트에 props 전달하기

React 컴포넌트는 props를 통해 서로 통신

## 배울 내용들

- props를 컴포넌트에 전달하는 방법.
- 컴포넌트에서 props 읽는 방법
- props의 기본값 지정.
- JSX를 컴포넌트에 전달하는 방법.
- 시간이 지남에 따라 props 변환

## 1단계: Props를 하위 컴포넌트에 전달

```javascript
export default function Profile() {
  return (
    <Avatar
      person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }}
      size={100}
    />
  );
}
```

## 2단계: 하위 컴포넌트 내부의 props 읽기

Props를 사용하면 부모 및 자식 구성 요소를 독립적으로 생각할 수 있음.

React  커포넌트 함수는 단일 인수인 props 객체를 허용.

```javascript
function Avatar({ person, size }) {
  // person and size are available here
}

// or

function Avatar(props) {
  //....
}
```

## 소품의 default value 지정

`size={undefined}` 와 size props가 없을 경우  **default value**가 적용됨. 

`size={null}` ,`size={0}` 경우 **default value**가 사용되지 않음.

```javascript
function Avatar({person, size = 100}) {
}
```

## JSX 스프레드 구문을 사용하여  props 전달

Prop 전달 경우 반복되는 경우.

간결한 구문을 사용하는 것이 합리적일 수 있음.

```javascript
function Profile({ person, size, isSepia, thickBorder }) {
  return (
    <div className="card">
      <Avatar
        person={person}
        size={size}
        isSepia={isSepia}
        thickBorder={thickBorder}
      />
    </div>
  );
}
```

```javascript
function Profile(props) {
  return (
    <div className="card">
      <Avatar {...props} />
    </div>
  );
}
```

## JSX을 자식으로 전달

```javascript
import Avatar from './Avatar.js';

function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

export default function Profile() {
  return (
    <Card>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi',
          imageId: 'YfeOqp2'
        }}
      />
    </Card>
  );
}
```

## 시간에 따른 구성요소 변경

컴포넌트가 시간이 지남에 따라 다른 prop을 받을 수 있음.

`props`는 불변입니다. 컴포넌트가 `props`를 변경해야 하는 경우 상위 컴포넌트에 다른 `props`, 즉 새로운 객체를 전달 하도록 **요청**이 필요. 그런 다음 오래된 `props`는 폐기되고 해당 `prop`가 차지한 메모리를 회수.

⏮️ Previous : [Javascript in JSX with Curly Braces](./008-%EB%A6%AC%EC%95%A1%ED%8A%B8%20JavaScript%20in%20JSX%20with%20curly%20braces.md)

⏭️ Next: [Conditional Rendering](./010-%EB%A6%AC%EC%95%A1%ED%8A%B8%20Conditional%20rendering.md)
