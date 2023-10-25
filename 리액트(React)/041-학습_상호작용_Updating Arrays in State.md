# Updating Arrays in State

> 배열 state 업데이트

객체와 마찬가지로 state에 저장된 배열을 업데이트하려면, 새로운 배열을 만들고 새 배열을 사용하도록 state를 설정

## 배울 내용들

1. React state 안의 배열 항목을 추가, 제거 또는 변경하는 방법
2. 배열 내부의 객체를 업데이트 하는 방법
3. Immer로 덜 반복적으로 배열을 복사하는 방법

## 변이 없이 배열 업데이트

객체와 마찬가지로 **React state의 배열은 읽기 전용으로 취급**

`arr[0] = ‘bird’`, `push()`, `pop()` 같이 배열을 변이하는 메서드도 사용해서는 안됨.

배열을 업데이트 할떄 마다 state 설정 함수에 새 배열을 전달하고 싶을 때 `filter()` 및 `map()`과 같은 비변이 메서드를 호출하여 새 배열을 만들면 됩니다.

일반적인 배열 연산에 대한 참조표 (React state 내에서 배열을 다룰때는 왼쪽 열의 메서드를 피하고 대신 오른쪽 열 메서드를 선호해야 함

|  | avoid (mutates the array)비추천 (배열 직접 변이) | prefer (returns a new array)추천 (새 배열 반환) |
| --- | --- | --- |
| adding 추가 | push, unshift | concat, [...arr] spread syntax (https://react-ko.dev/learn/updating-arrays-in-state#adding-to-an-array) |
| removing 삭제 | pop, shift, splice | filter, slice (https://react-ko.dev/learn/updating-arrays-in-state#removing-from-an-array) |
| replacing 교체 | splice, arr[i] = ... assignment | map (https://react-ko.dev/learn/updating-arrays-in-state#replacing-items-in-an-array) |
| sorting 정렬 | reverse, sort | copy the array first (https://react-ko.dev/learn/updating-arrays-in-state#making-other-changes-to-an-array)배 |

### 주의사항

slice와 splice는 이름은 비슷하지만 매우 다름.

- `slice`는 배열 또는 배열의 일부를 복사할 수 있음
- `splice`는 배열을 항목을 삽입하거나 삭제하기 위해 **변이**

React에서는 state의 객체나 배열을 변이하고 싶지 않기 때문에 `slice`(!p 없음)을 훨씬 더 자주 사용.

### 배열에 추가하기

```javascript
setArtists( // Replace the state
  [ // with a new array
    ...artists, // that contains all the old items
    { id: nextId++, name: name } // and one new item at the end
  ]
);
```

앞에 새 배열을 배치하면 `unshift()` 뒤에 배치하면 `push()`를 수행

### 배열에서 제거

가장 시운 방법은 **필터링**

`filter`는 원래 배열을 수정하지 않는다는 점에 유의.

```javascript
setArtists(
  artists.filter(a => a.id !== artist.id)
); 
```

### 배열 변경하기

배열 일부 또는 모든 항목을 변겨할려면 map()을 사용하여 새로운 배열을 만들 수 있음.

```javascript
const nextShapes = shapes.map(shape => {
      if (shape.type === 'square') {
        return shape;
      } else {
        return {
          ...shape,
          y: shape.y + 50,
        };
      }
    });
setShapes(nextShapes);
```

### 배열 항목 변경하기

```javascript
const nextCounters = counters.map((c, i) => {
      if (i === index) {
        return c + 1;
      } else {
        return c;
      }
    });
setCounters(nextCounters);
```

### 배열에 삽입하기

특정 위치에 항목을 삽입하고 싶을 떄 이를 위해 `...` 배열 전개 구문과 `slice()` 메서드를 함꼐 사용할 수 있음.

```javascript
const nextArtists = [
      ...artists.slice(0, insertAt),
      { id: nextId++, name: name },
      ...artists.slice(insertAt)
    ];
setArtists(nextArtists);
```

### 배열에 다른 변경 사항 적용하기

배열을 반전시키거나 정렬하고 싶을때 `map`과 `filter`, `전개구문` 같은 비변이 메서드만으로 할수 없음. 하지만 Javascript `reverse()` 및 `sort()` 메서드는 원래 배열을 변이하므로 직접 사용할 수 없음.

대신 **배열을 먼저 복사한 다음 변이하면 됩니다.**

```javascript
function handleClick() {
    const nextList = [...list];
    nextList.reverse();
    setList(nextList);
  }
```

**배열을 복사하더라도 배열 내부의 기존 항목을 직접 변이할 수는 없음.** 

이는 얕은 복사가 이루어져 새 배열에는 원래 배열과 동일한 항목이 포함되기 때문. 따라서 복사된 배열 내부의 객체를 수정하면 기존 state를 변이하는 것.

예를 들어, 다음과 같은 코드가 문제가 됨.

```javascript
const nextList = [...list];
nextList[0].seen = true; // Problem: mutates list[0]
setList(nextList);
```

`nextList` 와 `list`는 서로 다른 배열이지만 `nextList[0]`과 `list[0]`은 같은 객체를 가리킴.

이것은 state를 변이하므로 피해야함. 중첩된 Javascript 객체 업데이트와 비슷한 방법으로 이 문제를 해결할 수 있는데, 변경하려는 개별 항목 변이하는 대신 복사하는 것.

## 배열 내부의 객체 업데이트

**중첩된 state를 업데이트할 때는 업데이트하려는 지점부터 최상위 수준까지 복사본을 만들어야 합니다.**

```javascript
function handleToggleMyList(artworkId, nextSeen) {
	const myNextList = [...myList];
  const artwork = myNextList.find(a => a.id === artworkId);
  artwork.seen = nextSeen; // Problem: mutates an existing item
  setMyList(myNextList);
}
```

`myNextList` 배열 자체는 새 배열이지만, *항목 자체*는 원래의`myList` 배열과 동일

따라서`artwork.seen`을 변경하면 원본 작품 항목이 변경됨.

그러므로 `map`을 사용하여 이전 항목에 대한 변이 없이 업데이트된 버전으로 대체할 수 있습니다.**

```javascript
function handleToggleMyList(artworkId, nextSeen) {
	setMyList(myList.map(artwork => {
    if (artwork.id === artworkId) {
      // Create a *new* object with changes
      return { ...artwork, seen: nextSeen };
    } else {
      // No changes
      return artwork;
    }
  }));
}
```

일반적으로 **방금 만든 객체만 변이해야 함.** *새로운* artwork을 삽입하는 경우에는 변이해도 되지만, 이미 있는 state의 artwork을 다루는 경우에는 복사본을 만들어야 함.

### Immer로 간결한 업데이트 로직 작성

mmer를 사용하면 이제 `artwork.seen = nextSeen`과 같은 변이도 괜찮음

```javascript
const [myList, updateMyList] = useImmer(
    initialList
);

function handleToggleMyList(id, nextSeen) {
  updateMyList(draft => {
    const artwork = draft.find(a =>
      a.id === id
    );
    artwork.seen = nextSeen;
  });
}
```