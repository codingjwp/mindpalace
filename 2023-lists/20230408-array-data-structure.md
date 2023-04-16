# Array(Data Structure)

[웹에서 python(Python Tutor)](https://www.pythontutor.com)

\- 배열   
* 연속적인 메모리 공간에 저장된 아이템 집합을 표현한 자료구조
* 같은 타입을 연속적으로 저장하는 방식으로 운영

<br/>

![array list](/record-lists/2023-lists/imgs/20230408-01.png)

\- 배열을 생성하고 메모리 주소가 200으로 시작 했으고 연속적인 공간에 배열 인덱스 0, 1, 2의 값으로 접근할 수 있다.

## 리스트(List)
\- python에서 배열 속성 지원하기 위해 `List`와 `Arry 모듈`이 있다.   
\- 일반적으로는 `Array 모듈`이지만 `python`에서 `List`도 `Array` 처럼 쓸 수 있다.  
\- 차이점: `List`는 여러 타입을 하나의 자료구조로 담을 수 있고 `Array`는 하나의 타입만 넣을 수 있다.   

### List 생성 방법
```python
py_list_empty = []
print(py_list_empty) # 결과값: []

py_list = [1,2,3,4,5]
print(py_list) # 결과값: [1, 2, 3, 4, 5]

py_list_zero_1 = [0 for i in range(10)]
print(py_list_zero_1) # 결과값: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

py_list_zero_2 = [0] * 10
print(py_list_zero_2)# 결과값: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
```

### List 추가 방법
\- append() 추가, extend() 확장, insert() 추가   
```python
py_list = [1, 2, 3, 4, 5]
py_list.append(6)
print(py_list) # 결과값 [1, 2, 3, 4, 5, 6]

py_list_1 = [1, 2, 3]
py_list_2 = [4, 5, 6]
py_list_1.append(py_list_2)
print(py_list_1) # 결과값 [1, 2, 3, [4, 5, 6]]

py_list_1 = [1, 2, 3]
py_list_2 = [4, 5, 6]
py_list_1.extend(py_list_2)
print(py_list_1) # 결과값 [1, 2, 3, 4, 5, 6]

py_list = [1, 2, 3]
py_list.insert(3, 4)
print(py_list) # 결과값 [1, 2, 3, 4]
```

### List 삭제 방법
\- remove(), clear(), del
```python
# list는 중복된 값이 있을 수 있고 여러 값들 중 가장 앞선 인덱스의 요소가 삭제
py_list = [1, 2, 3, 2, 4]
py_list.remove(2)
print(py_list) # 결과값 [1, 3, 2 4]

py_list = [1, 2, 3, 4]
py_list.clear()
print(py_list) # 결과값 []

# 두번째 있는 요소를 삭제한다.
py_list = [1, 2, 3, 4]
del py_list[1]
print(py_list) # 결과값 [1, 3, 4]
```

### List 요소
![array image](/record-lists/2023-lists/imgs/20230408-02.png)

<br/>
<br/>

> 이미지, 내용 출처   
> \- 쓰면서 익히는 알고리즘과 자료구조
