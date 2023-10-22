# 설치하기

시스템 요구 사항: Node.js 16.14 이상. macOS, Windows(WSL 포함) 및 Linux가 지원됩니다.

## 자동 설치

모든 것을 자동으로 설치하는 `create-next-app`을 설치하는것이 새 Next.js 앱을 시작하는 것이 좋습니다.

```bash
npx create-next-app@latest
```

설치 시 다음 메시지가 표시 됩니다.

```bash
# 프로젝트 이름 설정
What is your project named? my-app
# 타입스크립트 설정
Would you like to use TypeScript? No / Yes
# ESLint 설정
Would you like to use ESLint? No / Yes
# 테일윈드 css 사용여부
Would you like to use Tailwind CSS? No / Yes
# src 디렉토리 생성 여부
Would you like to use `src/` directory? No / Yes
# App Router 사용 여부
Would you like to use App Router? (recommended) No / Yes
# import alias 사용 여부
Would you like to customize the default import alias (@/*)? No / Yes
What import alias would you like configured? @/*
```

메시지가 표시되면 create-next-app이 프로젝트 이름으로 폴더를 생성하고 필요한 종속성을 설치합니다.

- Next.js는 이제 기본적으로 TypeScript, ESLint 및 Tailwind CSS 구성과 함께 제공됩니다.
- 선택적으로 프로젝트 루트에 src 디렉터리를 사용하여 애플리케이션의 코드를 구성 파일과 분리할 수 있습니다.

## 수동 설치

```bash
npm install next@latest react@latest react-dom@latest
```

`package.json`을 열고 다음 `scripts`를 추가합니다.

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  }
}
```

이 스크립트는 애플리케이션 개발의 여러 단계를 나타냅니다:

- `dev` : dev를 실행하여 개발 모드에서 Next.js를 시작합니다.
- `build` :  빌드를 실행하여 프로덕션용으로 애플리케이션을 빌드합니다.
- `start` :  시작을 실행하여 Next.js 프로덕션 서버를 시작합니다.
- `lint` : 린트를 실행하여 Next.js의 기본 제공 ESLint 구성을 설정합니다.

## 디렉토리 만들기

Next.js는 **파일 시스템 라우팅**을 사용하므로 애플리케이션의 라우팅은 파일 구조에 따라 결정됩니다.

### 앱 디렉토리

새 애플리케이션의 경우 앱 라우터를 사용하는 것이 좋습니다.

이 라우터를 사용하면 React의 최신 기능을 사용할 수 있으며 커뮤니티 피드백을 기반으로 페이지 라우터를 발전시킨 것입니다.

`app/folder name`를 생성한 다음 `layout.tsx` 및 `page.tsx` 파일을 추가합니다. 사용자가 애플리케이션의 루트(`/`)를 방문할 때 렌더링됩니다.

필수 태그`<html>` `<body>`를 사용하여 `app/layout.tsx` 내에 루트 레이아웃을 생성합니다.

```tsx
// app/layout.tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

마지막으로 몇 가지 초기 콘텐츠로 홈페이지 `app/page.tsx`를 만듭니다.

```tsx
// app/page.tsx
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```

### 알아두면 좋습니다

`layout.tsx`를 만드는 것을 잊어버린 경우 다음 개발 서버로 `next dev`를 실행할 때 Next.js가 자동으로 이 파일을 생성합니다.

앱 라우터 사용에 대해 자세히 알아보세요.

### 페이지 디렉토리(선택 사항)

App Router 대신 Page Router를 사용하려는 경우 프로젝트 루트에 `pages/` 디렉토리를 만들 수 있습니다.

그런 다음 페이지 폴더에 `index.tsx` 파일을 추가합니다. 이것이 홈페이지(`/`)가 됩니다:

```tsx
// pages/index.tsx
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```

그런 다음 `pages/` 내에 `_app.tsx` 파일을 추가하여 글로벌 레이아웃을 정의합니다. 

```tsx
// pages/_app.tsx
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```

마지막으로 `pages/` 내에 `_document.tsx` 파일을 추가하여 서버의 초기 응답을 제어합니다. 

```tsx
// pages/_document.tsx
import { Html, Head, Main, NextScript } from 'next/document'
 
export default function Document() {
  return (
    <Html>
      <Head />
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```

### 알아두면 좋습니다.

동일한 프로젝트에서 두 라우터를 모두 사용할 수 있지만 `app`의 라우팅이 `pages`보다 우선합니다. 

혼동을 피하기 위해 새 프로젝트에는 라우터를 하나만 사용하는 것이 좋습니다.

### 공용 폴더(선택 사항)

이미지, 글꼴 등과 같은 정적 자산을 저장할 공용 폴더를 만듭니다.

그러면 공개 디렉토리 내의 파일은 기본 URL(/)에서 시작하는 코드에서 참조할 수 있습니다.

## 개발 서버 실행

`npm run dev`를 실행하여 개발 서버를 시작합니다.

`http://localhost:3000` 을 방문하여 애플리케이션을 확인합니다.

`app/layout.tsx`(또는 `pages/index.tsx`) 파일을 편집하고 저장하면 브라우저에서 업데이트된 결과를 확인할 수 있습니다.