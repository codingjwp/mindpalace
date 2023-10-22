# **Recoil 적용**

## **Recoil로 전역 상태 관리를 해보자.**

`SearchBar`에 입력하는 데이터를 가지고 fetch를 사용하여 json-server의 데이터를 가져올 계획.

### **atom 코드**

`SearchBar`에 **onChange** 이벤트에서 변경되는 값이 저장되는 공간

```typescript
import { atom } from 'recoil';

const searchTextState = atom<string>({
  key: "searchTextState",
  default: ""
})
```

### **selector 코드**

selector로 async 코드를 작성하여 pokemonlist 값을 가져오도록 작성.  
***fetch***의 조건은 한글또는 영어로 입력시 찾으며 최대 5개 까지로 제한.  
***`replace(/['+-_: ]+/g, "").toLowerCase()`***으로 띄어쓰기 몇 가지 특수문자를 제외 입력.
 
```typescript
export const searchListState = selector<PokemonListProps[]>({
  key: "searchListState",
  get: async ({get}) => {
    try {
      const pokemonName = get(searchTextState).replace(/['+-_: ]+/g, "").toLowerCase();
      if (pokemonName === "") return [];
      const fetchArray = [
        fetch(`${import.meta.env.VITE_API_KR_LIST}${pokemonName}`,),
        fetch(`${import.meta.env.VITE_API_EN_LIST}${pokemonName}`,),
      ];
      const response = await Promise.all(fetchArray);
      if (!response.every((res) => res.ok))
        throw new Error("Network Error");

      const [korean, english] = await Promise.all<PokemonListProps[]>(
        response.map((res) => res.json()),
      );

      if (!korean || !english)
        throw new Error("Not Found Error");
      return korean.length === 0 ? english : korean;
    } catch (error: unknown) {
      const data = (error as Error).message;
      throw new Error(data);
    }
  }
});
```