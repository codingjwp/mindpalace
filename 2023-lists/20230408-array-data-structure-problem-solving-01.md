# Array Problem Solving 01

## 두 수 합 찾기
<details open>
    <summary>문제</summary>
    주어진 정수형 배열에서 2개의 숫자를 선택하여 더한 값이 특정 목표값을 만들때 그 선택한 2개의 정수가 있는 배열의 인덱스를 반환하는 프로그램을 작성하라.
    입력값으로 주어지는 배열에는 정확히 하나의 정답이 존재하며, 같은 요소의 값을 중복해서 사용할 수 없다.<br/>
    * 입력값: num=[2,7,10,19], target=9<br/>
    * 출력값: [0, 1]   
</details>

***
### 제한사항:
| 내가 만든 것 | 책에서 적혀있는데로 작상해보기 |
|:---|:---|
|1. 정수형 배열을 사용| 1. 정수형 배열
|2. 특정 목표값을 만들다.| 2. 두 수의 합이 정수형을 초과할 수 있는가?<br/> - 문제에 언급이 안되어 있음
|3. 하나의 정답만 존재| 3. 두 수의 합이 값이 배열내에 무조건 존재하는가?<br/> - 무조건 정확히 하나의 해결책이 존재한다.
|4. 같은 요소의 값을 중복해서 사용 불가능| 4. 중복된 요소의 값을 2번 이상 사용하여 결괏값을 만들어서는 안된다.
***
### 아이디어(시간복잡도, 공간복잡도):
\- ***중복***이라는 말이 낳오면 ***해시 테이블(Hash Table or Set)*** 을 검토해보라.
|내가 작성한 내용|
|:---|
|1. 배열을 받아와야하면 특정 목표값도 받아와야 한다.|
|2. 합이기 때문에 특정 목표값보다 크면 무시한다.|
|3. 선택한 요소의 값이 특정 목표값보다 작으면 다음 요소값과 더해본다.|
|4. 선택한 요소의 값과 다음 요소값이 같으면 무시한다.|
|5. 선택한 요소와 다음 요소값의 합이 정수 최대 크기보다 클 경우 무시한다.|
|6. 특정 목표값과 같은지 확인하며 맞으면 해당 인덱스를 리스트에 넣는다.|
|시간복잡도: $O(n^2)$ 공간복잡도: $O(1)$|

|책에서 작성된 내용 1|
|:---|
|1. 배열의 모든 요소의 조합을 찾는다.|
| - 루프는 `i = 0 ~ n`, `j = i + 1 ~ n`으로 2중 루프를 구성한다|
| - 1번째 루프(n번) 2번째 루프(n-1)을 기준으로 n(n-1)로 계산|
|2. 해당 조합으로 목표값과 비교해서 같다면|
|- 해당 루프를 종료하고 각 값을 가진 인덱스를 반환한다.|
|시간복잡도: $O(n^2)$ 공간복잡도: $O(1)$|

|책에서 작성된 내용 2|
|:---|
|1. 해시테이블을 작성한다.|
|- 키값으로는 배열의 요소 값으로는 요소의 인덱스로 구성|
|2. 각 요소값을 순회하면서|
|- 몫표값 - 현재요소 = 다른 요소|
|- 해시 테이블에서 다른 요소 값을 찾는다.|
|- 만약 다른 요소가 해시테이블에 있다면 현재요소의 인덱스와 해시테이블 값(인덱스)를 반환한다.|
|- 다른요소가 없다면, 현재 요소를 키값으로 넣고 인덱스를 해시 테이블 값으로 추가한다.|
|시간복잡도: $O(n)$, 공간복잡도: $O(n)$|

***
### 코드:
#### 첫번 째 아이디어 Brute-force
```python
# import typing만 할 경우 typing.List[int] 이런식으로 사용
# from typing import List 경우 typing 모듈에서 List 클래스만 import함
from typing import List

def twoSum(nums: List[int], target: int) -> List[int]:
    for i in range(0, len(nums)):
        for j in range((i + 1), len(nums)):
            if (nums[i] + nums[j]) is target:
                return [i, j]
    return [-1, -1]

nums: List[int] = [2, 3, 4, 5, 9, 7]
target: int = 13
print(twoSum(nums, target))
```
#### 두번 째 아이디어 Hash Table
```python
from typing import List

def twoSum(nums: List[int], target: int)-> List[int]:
    hash_tables = {}
    
    for i in range(0, len(nums)):
        value: int = target - nums[i]
        
        if hash_tables.get(value) is not None \
            and hash_tables[value] != i:
            return sorted([i, hash_tables[value]])
        hash_tables[nums[i]] = i
        
    return [-1, -1]
    
    
nums: List[int] = [2, 7, 8, 11]
target: int = 13
print(twoSum(nums, target))
```
***
### 테스트 케이스:
```
nums: List[int] = [2, 7, 8, 11] target: int = 13
```
***
### 관련 문제 사이트
[LeetCode](https://leetcode.com/problems/two-sum)   
[Hackerrank](https://www.hackerrank.com/challenges/pairs/problem)   
[백준](https://www.acmicpc.net/problem/3273)   