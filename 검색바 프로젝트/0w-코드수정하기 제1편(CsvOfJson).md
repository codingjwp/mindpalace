# 검색바 프로젝트 사용할 데이터 만들기

## 검색바를 가져올 데이터를 바꾸자

데이터를 사용할 확장명으로 **JSON**을 골랐지만 내가 찾아본 데이터 파일은 **CSV file**이였다.

또한 데이터의 기준이 되는 **Number** 부분이 중복인걸 확인하여 새로운 유니크 값이 필요했습니다.

```bash
number,name,form,type1,type2,hp,attack,defense,spattack,spdefense,speed
001,Bulbasaur,,Grass,Poison,45,49,49,65,65,45
002,Ivysaur,,Grass,Poison,60,62,63,80,80,60
003,Venusaur,,Grass,Poison,80,82,83,100,100,80
003,Venusaur,Mega-Venusaur,Grass,Poison,80,100,123,122,120,80
# .... 생략
```

## UUID를 키로 삼자

이부분은 별로 중요하지 않아서 자세한 내용은 쓰지 않겠으나 사용한 부분은 **`uuid`**랑 **`with open`**을 사용한 읽기용 쓰기용 을 두개 나누어 uuid를 추가한 내용을 글을 새로운 csv파일로 생성하였다.

```python
import uuid

key =  uuid.uuid4()
```

## 한글네임 추가와 csv를 JSON으로 변경하자

한글은 동일한 데이터를 찾기 힘들어 일일이 내용을 추가하여서 하였습니다 .

그리고 JSON을 변환하기 위한 코드는 아래와 같습니다.

- **ensure_ascii=False** 경우 한글 같은 문자를 변환 문자 형태로 바꿔누는 걸 막기위해 false로 설정
- **indent=2**는 json 정렬을 위해 들여쓰기를 2 넣어서 출력하도록 설정

  ```python
  import csv, json

  input_file = './pokemon.csv'
  out_file = './pokemon.json'

  with open(input_file, 'r', encoding='utf-8') as csv_file:
    csv_reader = csv.reader(csv_file)

    header = next(csv_reader)

    lines = []
    for row in csv_reader:
      row_data = {header[i]: row[i] for i in range(len(header))}
      lines.append(row_data)

  with open(out_file, 'w', encoding='utf-8') as json_file:
    json.dump(lines, json_file, ensure_ascii=False, indent=2)
  ```
