# Array Problem Solving 02

## 정렬된 배열에서 중복 제거

***

<details open>
  <summary>문제</summary>
    정렬된 배열의 요소들을 중복 없이 단 1번 씩만 가질 수 있도록 그대로 수정하고, 수정된 배열의 새로운 길이를 반환하라. <br/>
    * 추가적인 배열의 할당은 하지 않고 중복된 요소를 하나만 남기고 걸러내는 함수를 만드는 것이다. <br/>
    반환 된 길이 이후 데이터는 무시해도 무방하다 <br/>
    입력값 : nums [0, 0, 0, 1, 2, 2, 2]
  
</details>

***
### 제한 사항
| 내가 작성한 제한사항 `(추가 제한 사항)`|
|:---|
| 1. 정렬된 배열 요소 |
| - 단 1번씩만 가질 수 있다 |
| - 입력 값에 제한이 없다. (정수형 배열로 정해진듯) |
| `- 입력 배열 길이는 0 일 수 있다.` | 
| 2. 추가적인 배열 할당 금지 |
| - 배열을 할당 안하면 수정할때 어떤 식으로 수정해야하지?? |
| 3. 반환된 길이 이후 데이터는 무시 |
| `4. 반환값은 정수이며 배열의 길이보다 작거나 같다` |
***
### 아이디어(시간 복잡도, 공간 복잡도)
| 내가 작성한 아이디어 `(추가 제한 사항)`|
|:---|
|1. currnt 변수 index: 0을 넣는다.|
|2. for에서 i = 0 ~ len(Array) - 1  까지 순회 합니다|
|3. currnt 값이 같으면 다음 요소로 넘깁니다.|
|`4. currnt 값이 같지 않으면 현재 값을 currnt에 업데이트 합니다`|
|`5. count 값을 증가하기 전 해당 인덱스가 되어 달라진 currnt값을 넣습니다.`|
|`6. count 값을 하나 증가 합니다.`|
|`7. 순회가 완료후 count값 을 반환`|
***
### 코드
|내가 쓴 코드|
|:---|
```python
from typing import List

def removeDuplication(nums: List[int])-> int:
  if len(nums) is 0:
    return 0

  count: int = 0;
  currnt: int = nums[0];
  for i in range(1, len(nums)):
    if num[i] != currnt:
      currnt = nums[i]
      ++count
      nums[count] = currnt
  return count

testArray = [0, 0, 0, 0, 1, 2, 2, 3, 4, 4]
print(removeDuplication(testArray))
```

<br />

|수정 코드|
|:---|
```python
from typing import List

def removeDuplication(nums: List[int])-> int:
  if len(nums) is 0:
    return 0

  count: int = 1; # 0을 쓰면 index까지는 돼지만 길이 값은 안됨
  currnt: int = nums[0];

  for i in range(1, len(nums)):
    if num[i] != currnt:
      currnt = nums[i]
      nums[count] = currnt
      count += 1 # ++사용 했는데 안되는 어서 변환
  return count

testArray = [0, 0, 0, 0, 1, 2, 2, 3, 4, 4]
print(removeDuplication(testArray))
```
***
### 테스트 케이스
`testArray = []`
`testArray = [0, 1, 2, 4]`
`testArray = [0, 0, 0, 0, 1, 2, 2, 3, 4, 4]`
***
### 관련 사이트
[LeetCode](https://leetcode.com/problems/remove-duplicates-from-sorted-array)   
[Hackerrank](https://www.hackerrank.com/contests/doyoulikeit/challenges/remove-duplicates-from-sorted-array)   
[백준 (배열 이용이 아닌 중복제거)](https://www.acmicpc.net/problem/13701)
***

[@다음문제](20230409-array-data-structure-problem-solving-03.md)