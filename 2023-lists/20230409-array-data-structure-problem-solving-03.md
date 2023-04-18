# Array Problem Solving 03

## 배열에서 삽입 위치 찾기


[웹에서 python(Python Tutor)](https://www.pythontutor.com)

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
|??? 중복 요소값이 없는 건가??|
|2. 배열이 비어있을 경우와 목푯값이 비어있을 경우|
|- 배열이 비어있는 경우는 상관 없는데 목푯값이 비어있는 경우??|
|3. 배열에 목푯값이 있을 수도 없을 수도 있다|
|- 있을 경우는 목푯값과 같은 값의 인덱스를 넘겨 준다|
|- 없을 경우 목푯값이 들어가는 인덱스 위치를 넘겨준다|
|4. 목푯값이 같은게 여러개 있을 경우??|
|- 첫번째로 같은 위치에 넣도록 한다.|

***

### 아이디어(시간 복잡도, 공간 복잡도)
| 내가 작성한 아이디어 1 `(추가 제한 사항)`|
|:---|
|1. 변수 nums, target 지정, 함수 arrayInsertIndex(A로 지칭) 지정|
|- 배열 nums가 비어 있을 경우 index 0 반환|
|- 배열 nums마지막 요소가 target보다 작은 경우 마지막 index 반환|
|2. 함수 A에 반복문(for) 0 ~ len(nums) 까지 실행|
|3. trget과 같은 요소 값이 있는 경우|
|- 해당 인덱스 값을 return|
|4. trget과 같은 요소가 없는 경우|
|- trget보다 큰 요소가 나오는 경우|
|- 해당 인덱스 값을 return| 

| 내가 작성한 아이디어 2 `(추가 제한 사항)`|
|:---|
|1. 변수 nums, target 지정, 함수 arrayInsertIndex(A로 지칭) 지정|
|- 배열 nums가 비어 있을 경우 index 0 반환|
|- 배열 nums마지막 요소가 target보다 작은 경우 마지막 index 반환|
|2. 배열의 길이 / 2 를 중간 index를 구하여 해당 요소와 비교 한다.|
|3. 비교하여 target이 해당 요소 보다 작은 경우|
|- 0 ~ 과 중간 index 범위에서|
|- 2번 행동을 반복하여 해당 하는 조건에 맞는 경우를 따라 간다(3, 4, 5)|
|- target과 같은 경우 찾으면 해당 index를 반환
|- target이 없는 경우 마지막에 비교한 인덱스에 따라 작으면 +1 크면 해당 인덱스 를 반환 |
|4. 비교하여 target이 해당 요소 와 같은 경우|
|- 해당 index 값을 반환한다.| 
|5. 비교하여 target이 해당 요소 와 큰 경우|
|- 중간 index 와 마지막 index 범위에서|
|- 2번 행동을 반복하여 해당 하는 조건에 맞는 경우를 따라 간다(3, 4, 5)|
|- target과 같은 경우 찾으면 해당 index를 반환
|- target이 없는 경우 마지막에 비교한 인덱스에 따라 작으면 +1 크면 해당 인덱스 
|`---------------------------------------------------------------------------------`|
| `1. 배열 요소를 이진 탐색으로 접근한다.`|
| `2. 요소를 찾는다면 해당 인덱스 반환` |
| `3. 끝까지 못찾는다면 최종 접근했던 낮은 인덱스 값을 반환`|

***

### 코드
|내가 쓴 코드 `(수정 후 너무 조건을 많이 넣어 간결히 함.)`|
|:---|
```python
from typing import List

def arrayInsertIndex(nums: List[int], target: int)-> int:
  for i in range(0, len(nums)):
    if nums[i] >= target:
      return i
  return len(nums)


nums1 = [1, 2, 3, 4, 5]
target1 = 3
nums2 = [1, 4, 5, 6]
target2 = 0
nums3 = [1, 4, 5, 6]
target3 = 3
nums5 = [1, 4, 5, 6]
target5 = 6
nums4 = [1, 4, 5, 6]
target4 = 100
print(arrayInsertIndex(nums1, target1), end=" ")
print(arrayInsertIndex(nums2, target2), end=" ")
print(arrayInsertIndex(nums3, target3), end=" ")
print(arrayInsertIndex(nums5, target5), end=" ")
print(arrayInsertIndex(nums4, target4))

```

<br />

|수정 코드|
|:---|
```python
from typing import List

def arrayInsertIndex(nums: List[int], target: int)-> int:
  low = 0
  hig = len(nums) - 1

  while low <= hig:
    mid = int((low + hig) / 2)
    if target == nums[mid]:
      return mid
    if target > nums[mid]:
      low = mid + 1
    else:
      hig = mid - 1 
    return low

nums1 = [1, 2, 3, 4, 5]
target1 = 3
nums2 = [1, 4, 5, 6]
target2 = 0
nums3 = [1, 4, 5, 6]
target3 = 3
nums5 = [1, 4, 5, 6]
target5 = 6
nums4 = [1, 4, 5, 6]
target4 = 100
print(arrayInsertIndex(nums1, target1), end=" ")
print(arrayInsertIndex(nums2, target2), end=" ")
print(arrayInsertIndex(nums3, target3), end=" ")
print(arrayInsertIndex(nums5, target5), end=" ")
print(arrayInsertIndex(nums4, target4))

```

***
### 테스트 케이스
`nums = [1, 2, 3, 4, 5] target=3`   
`nums = [1, 4, 5, 6] target=3`
`nums = [1, 4, 5, 6] target = 0`
`nums = [1, 4, 5, 6] target = 6`
`nums = [1, 4, 5, 6] target = 100`

***
### 관련 사이트
[LeetCode](https://leetcode.com/problems/search-insert-position)   
[Hackerrank](https://www.hackerearth.com/practice/algorithms/searching/linear-search/practice-problems/algorithm/simple-search-4/)   

***

[@다음문제](20230409-array-data-structure-problem-solving-04.md)