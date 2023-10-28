# Array reduce

콜백 함수 : `(prev, next) => prev + next`
초깃값 : 0

```javscript
const numbers = [1, 8, 7, 6, 3];
numbers.reduce((prev, next) => prev + next, 0);
```

## 진행 순서

- prev에 0 값이 입력
- numbers 첫번째 index 값인 1이 next에 입력
- `prev + next => 0 + 1`의 결괏값 1이 반환
- 위에 결괏값 1이 prev에 입력
- numbers 두번째 index 값인 8이 next에 입력
- `prev + next => 1 + 8`의 결괏값 9이 반환
- 해당 입력이 number 리스트의 모든 값이 순회
