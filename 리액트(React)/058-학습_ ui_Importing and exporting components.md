# Importing and exporting components

> 컴포넌트 내보내기와 가져오기  

## 배울수 있는 정보

- 루트 컴포넌트 파일이란?
  - 컴포넌트 가져오기 및 내보내기 방법
  - 기본 및 명명된 가져오기 및 내보내기를 사용하는 경우
  - 하나의 파일에서 여러 컴포넌트를 가져오거나 내보내는 방법
  - 컴포넌트를 여러 파일로 분할하는 방법

## Default vs named export (기본 내보내기 및 명명 내보니기)

![image](https://github.com/codingjwp/mindpalace/assets/113403155/13b39040-edf0-4cea-8a75-6e98dc4cb896)

출처: [react 공식 문서](https://react.dev/learn/importing-and-exporting-components#default-vs-named-exports)
  

내보내기 종류들

| Syntax | Export statement | import statement |
| --- | :---: | :---: |
| Default | `export default function Button() {}` | `import Button from './Button.js'` |
| Named | `export function Button() {}` | `import { Button } from ‘./Button.js’` |

파일을 하나의 컴포넌트를 내보낼때 `export default`를 사용

여러개 컴포넌트를 내보낼때  `named export`  를 사용

항상 컴포넌트 함수와 그 함수에 포함된 파엘에 의미 있는 이름 지정

`export default () ⇒{}` 이름 없는 컴포넌트는 디버깅이 어렵게 만들수 있기에 사용하지 않는 것이 좋다.