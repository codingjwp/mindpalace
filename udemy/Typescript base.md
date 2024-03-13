# Typescript 기본

## 기본 형식

기본 타입 형식으로는 아래와 같이 존재합니다.

- **Primitives: number, string. boolean**
- **More complex types: arryas, objects**
- **Function types, parameters**

아래는 기본 타입 형식의 예제 코드 입니다.

```typescript
let age: number;
age = 12;

let userName: string;
userName = 'Max';

let isInstructor: boolean;
isInstructor = false;

// More complex types

let hobbies: string[];
hobbies = ['Sports', 'Cooking'];

let person: {
  name: string,
  age: number,
};
person = {
  name: 'Max',
  age: 32,
}
```

## Union 유형

값을 좀더 유연하게 사용 가능하도록 합니다.

원래 `let course: string = 'React - THe Complete Guide'`으로 되어 잇는 것을 number 형식도 동일하게 사용하고 싶을때 Union 유형을 사용합니다.

```typescript
let course: string | number = 'React - THe Complete Guide'
course = 123456
```

## Type aliases

복잡한 타입을 정의하고 그 타입 별칭을 재사용합니다.

```typescript
type Person = {
  name: string,
  age: number,
};

let persons: Person;
```

## Fuctions & type

리턴값이 존재하는 add 함수 같은 경우 a + b의 타입추론으로 인해 number을 반환 합니다.

하지만 리턴 값이 존재 하지 않는 함수 경우 null, undefined랑 다르게 void를 반환하여 값이 없다는 걸 표현해줍니다. 

```typescript
function add(a: number, b: number) {
  return a + b;
}
const addNmber = add(1, 2)

// 리턴 값이 없는 함수
function print(value: any) {
  console.log(value);
}
```

## Generics 제네릭

여러가지 타입이 들어가는 경우 사용됩니다.  
아래의 코드 경우 `number[]`로 타입추론이 되는 함수이지만 반환이 `any[]`일 경우  
split 부분에서 에러가 발생하여야 하지만 `any[]`로 타입이 반환되어 코드를 작성할때는  
에러가 발생하지 않지만 실행시 에러가 발생합니다.

하지만 제네릭을 사용하여 타입추론으로 인해 코드 작성시 에러가 표시되며 괜찮아 집니다.

```typescript
// function insertAtBeginning(array: any[], value: any) {
//   const newArray = [value, ...array];
//   return newArray;
// }

function insertAtBeginning<T>(array: T[], value: T) {
  const newArray = [value, ...array];
  return newArray;
}

const demoArray = [1, 2, 3]

const updateArray = insertAtBeginning(demoArray, -1);

updateArray[0].split('');
```
