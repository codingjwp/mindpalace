# SearchBar, SearchDetail 수정하기

## 수정 전 코드

### SearchBar 코드

```typescript
  import { searchDetailIndex, searchTextState } from "../apis/recoilState";
  const [searchText, setSearchText] = useRecoilState(searchTextState);
  const [detailIndex, setDetailIndex] = useRecoilState(searchDetailIndex);
```

Key Press 이벤트 발생시 일어나는 코드 입니다.

- `ArrowUp`, `ArrowDown` 키가 눌러졌을 시 `SearchDetail`가 존재하는 경우 **0 ~ 4** 위치에 해당하는 색상을 변경합니다.
- `Enter` 키가 눌러졌을 시 `SearchDetail`에 detailIndex 값이 **0 ~ 4** 일 경우 그 해당 한글값을 `input(SearchBar)`에 입력을 넣어 줍니다.
- **0 ~ 4**가 아닌 경우 첫 번째 목록 값으로 조회하도록 하였습니다.

```typescript
  const hasSearchDetailIndex = (e: KeyboardEvent) => {
    if (!["ArrowUp", "ArrowDown", "Enter"].includes(e.key) || e.nativeEvent.isComposing)
      return;
    if (e.key === "ArrowUp" && detailIndex > 0) {
      setDetailIndex(detailIndex - 1);
    } else if (e.key === "ArrowDown" && detailIndex < 4) {
      setDetailIndex(detailIndex + 1);
    } else if (e.key === "Enter" && detailIndex !== -1) {
      const li = document.querySelectorAll("li");
      if (li && li.item(0).title === "no-search")
        return;
      if (li && li.item(detailIndex)) {
        setSearchText(li.item(detailIndex).innerText.split("(")[0]);
        setDetailIndex(-1);
      }
    } else if (e.key === "Enter" && detailIndex === -1) {
      movePokemonDb();
    }
  };
```

`SearchBar`와 `SearchDetail`에서 코드를 보면서 생각나는 것은 두가지가 있습니다.  

1. `Recoil`의 `atom`을 이용해서 `input` `onChange` 이벤트를 처리할 필요가 있는가??
2. `useEffect`와 `setDetailIndex`가 `SearchDetail`에서 할 필요가 있을까??  

### `Recoil`의 `atom`을 이용해서 `input` `onChange` 이벤트를 처리할 필요가 있는가??

아래의 코드가 사용되는 용도는 `input` `onChange` 이벤트를 통해 값을 받고 `selector`를 통해 비동기를 하여 목록을 받는 역할을 하기위해 사용되었습니다.

```typescript
export const searchTextState = atom<string>({
  key: "searchTextState",
  default: "",
});

const [searchText, setSearchText] = useRecoilState(searchTextState);
```

`searchTextState`의 `atom` 값을 받아 데이터 목록을 비동기로 가져오는 `selector` 입니다.

```typescript
interface PokemonListProps {
  id: string;
  enname: string;
  search: string;
  krname: string;
}

export const searchListState = selector<PokemonListProps[]>({
  key: "searchListState",
  get: async ({ get }) => {
    try {
      const pokemonName = get(searchTextState)
        .replace(/['+-_: ]+/g, "")
        .toLowerCase();
      if (pokemonName === "") return [];
      const fetchArray = [
        fetch(`${import.meta.env.VITE_API_KR_LIST}${pokemonName}`),
        fetch(`${import.meta.env.VITE_API_EN_LIST}${pokemonName}`),
      ];
      const response = await Promise.all(fetchArray);
      if (!response.every((res) => res.ok)) throw new Error("Network Error");

      const [korean, english] = await Promise.all<PokemonListProps[]>(
        response.map((res) => res.json()),
      );

      if (!korean || !english) throw new Error("Not Found Error");
      return korean.length === 0 ? english : korean;
    } catch (error: unknown) {
      const data = (error as Error).message;
      throw new Error(data);
    }
  },
});
```

위 코드를 작성하고 난 후 이런 생각을 하게된 계기는 `pokemondb` 페이지로 간 다음 뒤로가기 하면 `input`에 **값이 그대로 남아 있는 현상**을 보고 리코일이 전역상태관리로 인해 초기화를 할 곳을 어디에 둬야할지 몰라서 였습니다.

리코일에도 초기화 할 수 있는 훅은 `useResetRecoilState(state)`이 존재 했지만 리셋을 반환하는 함수를 쓸만한 곳이 보이지 않았습니다.

그렇게 방법을 찾다가 저는 아래의 코드와 같이 `useEffect`를 이용하여 해결 하였습니다. 이 방법은 마운트시 초기화 하는 방법으로 동작은 하나 의존성에서 경고를 띄우는 현상입니다.

```typescript
useEffect(() => {
  setSearchText("");
}, []);
```

위 코드는 불필요한 코드라고 생각을 하였고 여러 방법을 찾아보며 저는 `useState`를 이용하고 컴포넌트로 `props`를 넘겨 `selectorFamily`를 사용하는 방법으로 수정해 보았습니다.

atom을 useState로 변경

```typescript
const [searchText, setSearchText] = useState('');
```

매개변수를 받기위해 `selector`을 `selectorFamily`로 변경  
타입을 `<반환 타입, 매개변수 타입>`로 지정 `<PokemonListProps[], string>`  
get 부분에 `(name: string)` 추가와 `async()` 에 `{get}` 삭제

```typescript
export const searchListState = selectorFamily<PokemonListProps[], string>({
  key: "searchListState",
  get: (name: string) => async () => {
    try {
      const pokemonName = name.replace(/['+-_: ]+/g, "").toLowerCase();
      if (pokemonName === "") return [];
      const fetchArray = [
        fetch(`${import.meta.env.VITE_API_KR_LIST}${pokemonName}`),
        fetch(`${import.meta.env.VITE_API_EN_LIST}${pokemonName}`),
      ];
      const response = await Promise.all(fetchArray);
      if (!response.every((res) => res.ok)) throw new Error("Network Error");

      const [korean, english] = await Promise.all<PokemonListProps[]>(
        response.map((res) => res.json()),
      );

      if (!korean || !english) throw new Error("Not Found Error");
      return korean.length === 0 ? english : korean;
    } catch (error: unknown) {
      const data = (error as Error).message;
      throw new Error(data);
    }
  },
});
```

`props`로 `searchText`값 전달 및 `selector`을 `selectorFamily`로 가져올 수 있도록 `useRecoilValue(searchListState)`을 `useRecoilValue(searchListState(pokemonName))` 지정

```typescript
interface searchDetailProps {
  isFocused: boolean;
  pokemonName: string;
  detailIndex: number;
  touchDetail?: (e: MouseEvent) => void;
}

const SearchDetail = ({
  isFocused,
  pokemonName,
  detailIndex,
  touchDetail,
}: searchDetailProps) => {
const data = useRecoilValue(searchListState(pokemonName));
//...
}
```

테스트 결과 이상없이 작동하였습니다.

### `useEffect`와 `setDetailIndex`가 `SearchDetail`에서 할 필요가 있을까??  

아래 코드와 같이 detailIndex 값이 넘어 가지 못하도록 하기위해 사용 된 코드입니다.

```typescript

const setDeatilIndex = useSetRecoilState(searchDetailIndex);

useEffect(() => {
  if (data.length <= detailIndex) {
    setDetailIndex((prev) => prev - 1);
  }
}, [detailIndex]);

```

`keyPress` 이벤트로 인해 해당 부분의 값이 넘어갈 수도 있어서 데이터 크기 이상으로 넘어 갈 경우 원상태로 돌려놓는 코드를 작성했는데 필요하지가 않아서 수정하였습니다.

위 코드를 삭제, 0보다 같거나 작을 경우 코드

```typescript
setDetailIndex(prev => prev <= 0 ? prev : prev - 1);
```

li 수보다 많을 경우 코드

```typescript
const length = document.querySelectorAll("li").length;
setDetailIndex(prev => prev + 1 === length ? prev : prev + 1);
```

전체적인 코드

```typescript
const [detailIndex, setDetailIndex] = useRecoilState(searchDetailIndex);

  const hasSearchDetailIndex = (e: KeyboardEvent) => {
    if (!["ArrowUp", "ArrowDown", "Enter"].includes(e.key) || e.nativeEvent.isComposing )
      return;

    if (e.key === "ArrowUp" && detailIndex > 0) {
      setDetailIndex(prev => prev <= 0 ? prev : prev - 1);
    } else if (e.key === "ArrowDown" && detailIndex < 4) {
      const length = document.querySelectorAll("li").length;
      setDetailIndex(prev => prev + 1 === length ? prev : prev + 1);
    } else if (e.key === "Enter" && detailIndex !== -1) {
      const li = document.querySelectorAll("li");
  
      if (li && li.item(0).title === "no-search")
        return;

      if (li && li.item(detailIndex)) {
        setSearchText(li.item(detailIndex).innerText.split("(")[0]);
        resetIndex();
      }
    } else if (e.key === "Enter" && detailIndex === -1) {
      movePokemonDb();
    }
  };
```

if 문에도 조건을 추가해서 추가적인 문제가 발생할 방법을 차단하였습니다.
