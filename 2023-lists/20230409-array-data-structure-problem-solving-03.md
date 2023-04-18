# Array Problem Solving 03

## 배열에서 삽입 위치 찾기

***

<details open>
  <summary>문제</summary>
   정렬된 배열과 목푯값이 주어지는데 목푯값을 찾는다면 배열의 해당 인덱스를 반환하고, 찾지 못한다면 정렬된 배열이 되도록 목푯값이 배열을 들어가야 하는 인덱스를 구하는 문제이다.<br/>
   - 입력값: nums=[1,2,3,4,5] target=3<br/>
   - 출력값: 2 <br/>
   정렬된 배열에 target이 없을 시 <br/>
   - 입력값: nums=[1,4,5,6] target=3<br/>
   - 출력값: 1 <br/>
</details>

***
### 제한 사항
| 내가 작성한 제한사항 `(추가 제한 사항)`|
|:---|
|1. `정수형` 정렬된 배열과 목푯값을 입력값으로 받는다.|
|2. `배열 값은 클 수도 있다`|

| 내가 작성한 부분은 아이디어 쪽에 넣어야 하는 내용|
|:---|
|2. 배열이 비어있을 경우와 목푯값이 비어있을 경우|
|- 배열이 비어있는 경우는 상관 없는데 목푯값이 비어있는 경우??|
|3. 배열에 목푯값이 있을 수도 없을 수도 있다|
|- 있을 경우는 목푯값과 같은 값의 인덱스를 넘겨 준다|
|- 없을 경우 목푯값이 들어가는 인덱스 위치를 넘겨준다|
|4. 목푯값이 같은게 여러개 있을 경우??|
|- 첫번째로 같은 위치에 넣도록 한다.|
***
### 아이디어(시간 복잡도, 공간 복잡도)
| 내가 작성한 아이디어 `(추가 제한 사항)`|
|:---|
***
### 코드
|내가 쓴 코드|
|:---|
```python
```

<br />

|수정 코드|
|:---|
```python
```

***
### 테스트 케이스
***
### 관련 사이트
[LeetCode](https://leetcode.com/problems/remove-duplicates-from-sorted-array)   
[Hackerrank](https://www.hackerrank.com/contests/doyoulikeit/challenges/remove-duplicates-from-sorted-array)   
[백준 (배열 이용이 아닌 중복제거)](https://www.acmicpc.net/problem/13701)
***

[@다음문제](20230409-array-data-structure-problem-solving-04.md)