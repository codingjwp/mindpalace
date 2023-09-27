# NextJs 설치 및 실행하기

**version** : NextJs 13 App router 사용버전을 공부합니다.

## 설치 방법

`.`부분에 설치하고 싶은 저장소 위치를 입력합니다.

```bash
npx create-next-app@latest .
```

### next 설치 시 선택 내용
위 코드를 입력 후 나타나는 선택하는 부분 내용들 입니다.

```bash
# 타입 스크립트를 사용하시겠습니까?
? Would you like to use TypeScript? > No / Yes
# EsLint를 사용하시겠습니까?
? Would you like to use EsLint? > No / Yes
# Tailwind CSS를 사용하시겠습니까?
? Would you like to use Tailwind CSS? > No / Yes
# `src/`를 디렉토리로 사용하시겠습니까?
? Would you like to use `src/` directory? > No / Yes
# App Router를 사용하시겠습니까?
? Would you like to use App Router? (recommended) > No / Yes
# import 경로를 커스텀하여 사용하시겠습니까?
? Would you like to customize the default import alias? > No / Yes
# 위의 코드를 Yes를 선택했 때 나오는 내용입니다.
# 기본 을 @/* 지정하겠습니까?
? What import alias would you like configured? > @/*
```

### 설치되는 라이브러리
설치되는 내용들 입니다.

```bash
Creating a new Next.js app in #{install fold path}

Using npm.

Initializing project with template: app-tw 


Installing dependencies:
- react
- react-dom
- next

Installing devDependencies:
- typescript
- @types/react
- @types/node
- @types/react-dom
- autoprefixer
- postcss
- tailwindcss
- eslint
- eslint-config-next
```

## 실행 방법

보통 리액트 실행할 때는 개발서버 실행할때 `npm run start` 사용하지만 Nextjs경우에는 다른 명령어를 작동시킵니다.

```bash
npm install
npm run dev
```

**NextJs**는 실행했을때 `src/app/page.tsx`를 먼저 실행합니다.

`src/app/page.tsx`결과를 받아서 `src/app/layout.tsx`가 있다면 `children`으로 주입하며 최종적인 결과를 응답한다.
