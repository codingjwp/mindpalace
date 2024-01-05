# Git과 Github Part 1

깃은 어디에 사용될까??

- 버전관리 도구
- 현업도구

## 기본적인 커맨드

명령어 입력하는 방법

- CLI: 직업 입력하는 방법
- GUI: 그래픽으로 입력하는 방법

### 명령어

- `clone` : 불러오기
- `commit` : 저장
- `push` : 업로드
- `pull` : 다운로드

### branch와 merge

- `branch` : 분할
- `merge` : 병합

## 브랜치 전략

Git-flow 전략

- `Main` : 무조건 안정적인 코드
- `Develop` : 개발 브랜치, pull 받아야하는 브랜치
- `Release` : 배포 브랜치, (버그만 수정하는 곳), **Dev와 Main에 무조건 Merge를 해줘야 합니다.**
- `Feature` : 개발을 하는데 사용하는 실험실, 개발하다 망하면 버려도 되는 곳, 브랜치 하나당 기능 하나, Dev에서 다 같이 개발 시 코드가 꼬일 수 있어서 사용
- `Hotfix` : 급하게 수정되어야 하는 이슈, 버그 / 기능, **Dev와 Main에 무조건 Merge를 해줘야 합니다.** 

## stash 기능

임시 저장과 같은 기능  
의미 없는 커밋 메세지를 만들지 않을 수 있습니다.

### 기본적으로 임시 저장 하는 방법

```bash
# 자동적으로 메세지를 넣어줍니다.
git stash 
# 직접 이유를 넣습니다.
git stash push -m "(임시저장하는 이유)"
```

하지만 위에 커맨드는 변경된 내용을 전체 적으로 저장하는 방법입니다.  

그럼 각각의 파일 예를 들어 `A.file`, `B.file`에 각각 변경사항이 있을때 `A.file`만 `stash`를 하고 싶으면 어떻게 해야할까?

- `--` 옵션의 끝을 나타내고 파일 경로나 패턴을 지정하는데 사용.

```bash
## Staging area에 등록을 할려면 git add 또는 --index 옵션을 사용하여 넣어줘야 합니다
git stash push -- (파일 위치)
```

### 임시 저장 한 목록을 확인하는 방법

저장한 목록을 봅니다 기본적으로 Stack 형태로 최신 저장한 부분이 위에 작성되어 있습니다.  

```bash
# 항목 보기
git stash list

# ex stash@{0}: WIP on main: 4f669bc docs: 깃과깃허브 파트
```

### 다른 브랜치에 stash 한 내용을 main 브랜치에 불러오기

`aplly`와 `pop` 둘다 저장한 `stash` 내용을 불러오지만 `pop` 경우는 불러오고 `stash` 내용을 삭제 하지만 `apply`는 불러오기만 하며 다른 명령어로 삭제해 줘야 합니다.

```bash
# 가장 마지막 stash
git stash apply
# 특정 구역 stash
git stash apply stash@{(번호)}

# 가장 마지막 stash
git stash pop
# 특정 구역 stash
git stash pop stash@{(번호)}
```

[추가 적인 내용](https://git-scm.com/book/ko/v2/Git-%EB%8F%84%EA%B5%AC-Stashing%EA%B3%BC-Cleaning)

## cherryPick

본인이 필요한 부분만 골라서 커밋할 수 있는 기능

```bash
```

