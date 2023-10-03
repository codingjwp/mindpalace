# 자산, 메타데이터 및 CSS

추가한 두 번째 페이지에는 현재 스타일이 없습니다. 페이지 스타일을 지정하기 위해 CSS를 추가해 보겠습니다.

Next.js에는 CSS 및 Sass에 대한 지원이 내장되어 있습니다. 이 과정에서는 CSS를 사용합니다.

이 단원에서는 Next.js가 이미지와 같은 정적 자산과 태그와 같은 페이지 메타데이터를 `<title>` 등을 처리하는 방법에 대해서도 설명합니다.

## 이 단원에서 배울 내용

- Next.js에 정적 파일(이미지 등)을 추가하는 방법.
- `<head>` 각 페이지에 들어갈 내용을 사용자 지정하는 방법입니다.
- CSS 모듈을 사용하여 스타일이 지정된 재사용 가능한 React 구성 요소를 만드는 방법.
- `~~pages/_app.js`에 전역 CSS를 추가하는 방법.~~

> `src/app/layout.js`에 전역 CSS를 추가하는 방법.

- Next.js의 스타일링에 대한 몇 가지 유용한 팁.

## 자산

Next.js 최상위 `public` 디렉토리에서 이미지와 같은 **정적 자산을** 제공할 수 있습니다. 내부 파일은 `app`와 유사하게 응용 프로그램의 루트에서 참조할 수 있습니다.

이 디렉토리는 , Google 사이트 확인 및 기타 정적 자산에도 유용합니다. 자세한 내용은 정적 파일 제공에 대한 설명서를 확인하세요. `public` `robots.txt` `favicon.ico`

### 프로필 사진 다운로드하기

- 프로필 사진을 형식으로 **다운로드**하거나 [이 파일을 사용합니다](https://github.com/vercel/next-learn/blob/main/basics/basics-final/public/images/profile.jpg).`.jpg`
- `public` 디렉터리 내에 `images` 디렉터리를 만듭니다.
- `public/images` 디렉터리에 `profile.jpg` 로 그림을 저장합니다.
- 이미지 크기는 약 400x400픽셀입니다.
- 사용하지 않는 SVG 로고 파일은 `public` 디렉토리 바로 아래에서 제거할 수 있습니다.

### 최적화되지 않은 이미지

일반 HTML을 사용하면 다음과 같이 프로필 사진을 추가할 수 있습니다.

```tsx
<img src="/images/profile.jpg" alt="Your Name" />
```

그러나 이는 다음을 수동으로 처리해야 함을 의미합니다.

- 이미지가 다양한 화면 크기에서 반응하는지 확인
- 타사 도구 또는 라이브러리로 이미지 최적화
- 뷰포트에 들어갈 때만 이미지 로드

대신 Next.js는 이를 처리할 수 있는 구성 요소(`Image`)를 즉시 제공합니다.

### 이미지 구성 요소 및 이미지 최적화

`next/image`는 최신 웹을 위해 진화한 HTML 요소의 확장입니다.

Next.js는 기본적으로 이미지 최적화도 지원합니다. 이를 통해 브라우저가 지원하는 경우 WebP와 같은 최신 형식으로 이미지 크기를 조정, 최적화 및 제공할 수 있습니다.

이렇게 하면 뷰포트가 더 작은 기기로 큰 이미지를 배송하는 것을 방지할 수 있습니다. 또한 Next.js는 향후 이미지 형식을 자동으로 채택하여 해당 형식을 지원하는 브라우저에 제공할 수 있습니다.

자동 이미지 최적화는 모든 이미지 소스에서 작동합니다.

이미지가 CMS와 같은 외부 데이터 소스에 의해 호스팅되더라도 여전히 최적화할 수 있습니다.

### 이미지 구성 요소 사용

빌드 시 이미지를 최적화하는 대신 Next.js는 사용자가 요청할 때 요청 시 이미지를 최적화합니다. 정적 사이트 생성기 및 정적 전용 솔루션과 달리 10개의 이미지를 제공하든 10, 10만 개의 이미지를 제공하든 빌드 시간은 증가하지 않습니다.

이미지는 기본적으로 지연 로드됩니다. 즉, 뷰포트 외부의 이미지에 대해 페이지 속도에 불이익이 발생하지 않습니다. 이미지는 뷰포트로 스크롤될 때 로드됩니다.

이미지는 항상 Google이 [검색 순위에 사용할](https://webmasters.googleblog.com/2020/05/evaluating-page-experience.html) [핵심 웹 필수](https://web.dev/vitals/#core-web-vitals) 요소인 [누적 레이아웃 이동](https://web.dev/cls/)을 방지하는 방식으로 렌더링됩니다.

다음은 `next/image`를 사용하여 프로필 사진을 표시하는 예입니다. 및 props는 원본 이미지와 동일한 가로 세로 비율을 가진 원하는 렌더링 크기여야 합니다.

```tsx
import Image from 'next/image';

const YourComponent = () => (
  <Image
    src="/images/profile.jpg" // Route of the image file
    height={144} // Desired size with correct aspect ratio
    width={144} // Desired size with correct aspect ratio
    alt="Your Name"
  />
);
```

## 메타데이터

HTML 태그와 같은 페이지의 메타데이터를 수정하려면 어떻게 해야 합니까?

정적 메타데이터를 정의하려면 `Metadata` 객체 또는 `layout.js`, `page.ja` 정적 파일에서 정의합니다.

```tsx
import type { Metadata } from 'next'
 
export const metadata: Metadata = {
  title: '...',
  description: '...',
}
```

### 동적 메타데이터

동적 값이 필요한 메타데이터에 function을 사용할 수 있습니다.

```tsx
import type { Metadata, ResolvingMetadata } from 'next'
 
type Props = {
  params: { id: string }
  searchParams: { [key: string]: string | string[] | undefined }
}
 
export async function generateMetadata(
  { params, searchParams }: Props,
  parent: ResolvingMetadata
): Promise<Metadata> {
  const id = params.id
  const product = await fetch(`https://.../${id}`).then((res) => res.json())
 
  const previousImages = (await parent).openGraph?.images || []
 
  return {
    title: product.title,
    openGraph: {
      images: ['/some-specific-page-image.jpg', ...previousImages],
    },
  }
}
 
export default function Page({ params, searchParams }: Props) {}
```

정적 및 동적 메타데이터는 **모두 서버 구성 요소에서만 지원됩니다**.

### 기본  필드

경로가 메타데이터를 정의하지 않는 경우에도 항상 추가되는 두 가지 기본 태그가 있습니다.

- 이 `meta charset tag` 웹 사이트의 문자 인코딩을 설정합니다.
- 이 `meta viewport tage` 웹 사이트의 뷰포트 너비와 배율을 설정하여 다른 장치에 맞게 조정합니다.

```tsx
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
```

### 제 3자 JavaScript

**타사 JavaScript**는 타사 소스에서 추가된 모든 스크립트를 나타냅니다.

보통 타사 스크립트는 그렇지 않은 사이트에 새로운 기능을 도입하기 위해 포함됩니다. 분석, 광고 및 고객 지원 위젯과 같이 처음부터 작성해야 합니다.

### 추가하기

Next.js 페이지에 타사 스크립트를 추가하는 방법에 대해 알아보겠습니다.

### 스크립트 구성 요소 사용

`next/script`는 HTML 요소의 확장이며 추가 스크립트를 가져와서 실행할 때를 최적화합니다.

동일한 파일에서 파일 시작 부분에 `import` 을 추가합니다.

```tsx
import Script from 'next/script';
```

이제 구성 요소를 포함하도록 구성 요소를 업데이트합니다

```tsx
export default function FirstPost() {
  return (
    <>
      <h1>First Post</h1>
      <h2>
        <Link href="/">← Back to home</Link>
      </h2>
      <Script
        src="https://connect.facebook.net/en_US/sdk.js"
        strategy="lazyOnload"
        onLoad={() => console.log(`script loaded correctly, window.FB has been populated`)} />
    </>
  );
}
```

몇 가지 추가 속성이 Script 구성 요소에 정의되어 있습니다.

| Prop | Example | Type | Required |
| --- | --- | --- | --- |
| src | src="주소" | String | 인라인 스크립트를 사용하지 않는 한 필수입니다. |
| strategy | strategy="lazyOnload" | String | - |
| onLoad | onLoad={onLoadFunc} | Function | - |
| onReady | onReady={onReadyFunc} | Function | - |
| onError | onError={onErrorFunc} | Function | - |
- `strategylazyOnload`: 타사 스크립트가 로드되어야 하는 시기를 제어합니다.
- `onLoad`: 는 스크립트 로드가 완료된 직후 JavaScript 코드를 실행하는 데 사용됩니다.
- `onReady` : 일부 타사 스크립트에서는 스크립트 로드가 완료된 후 구성 요소가 탑재될 때마다(예: 경로 탐색 후) 사용자가 JavaScript 코드를 실행해야 합니다.
- `onError` : 때로는 스크립트가 로드되지 않을 때 catch하는 것이 도움이 됩니다. 이러한 오류는 onError 속성으로 처리할 수 있습니다.

## CSS 스타일링

파일 구조를 살펴보면 `global.css`와 `Home.module.css`이라는 두 개의 CSS 파일로 호출된 폴더를 볼 수 있습니다.

프로젝트에 이러한 파일이 없는 경우 여기에서 시작 코드를 다운로드할 수 있습니다.

```bash
npx create-next-app nextjs-blog --use-npm --example "https://github.com/vercel/next-learn/tree/main/basics/assets-metadata-css-starter"
```

### css module

CSS 모듈을 사용하면 고유한 클래스 이름을 자동으로 만들어 구성 요소 수준에서 CSS의 범위를 로컬로 지정할 수 있습니다.

이렇게 하면 클래스 이름 충돌에 대한 걱정 없이 다른 파일에서 동일한 CSS 클래스 이름을 사용할 수 있습니다.

CSS 모듈 외에도 다음과 같은 다양한 방법으로 Next.js 응용 프로그램의 스타일을 지정할 수 있습니다.

- 파일을 가져올 수 있는 Sass입니다.
- Tailwind CSS와 같은 PostCSS 라이브러리.
- styled-jsx, styled-components 및 emotion과 같은 CSS-in-JS 라이브러리

### 레이아웃 구성 요소

먼저 모든 페이지에서 공유할 **Layout** 구성 요소를 만들어 보겠습니다.

`src/app/posts/first-post` 디렉토리에 `layout.js` 파일을 만듭니다.

```tsx
import { ReactNode } from 'react'

export default function PostLayout({children}
    : {children: ReactNode}
    ) {
    return (
        <div>{children}</div>
    )
}
```

`src/app/posts/first-post/page.js` 가장 바깥쪽 구성요소로 만듭니다.

```tsx
import type { Metadata } from 'next';
import Link from 'next/link';

export const metadata: Metadata = {
  title: 'First Post',
}

export default function FirstPost() {
  return (
		<>
      <h1>First Post</h1>
      <h2>
        <Link href="/">← Back to home</Link>
      </h2>
    </>
  );
}
```

### css 추가

CSS 모듈을 사용하려면 CSS 파일 이름이 `.module.css` 로 끝나야 합니다.

`src/app/posts/first-post` 디렉토리에 `layout.module.css`를 만들고 아래 코드를 입력합니다.

```css
.container {
  max-width: 36rem;
  padding: 0 1rem;
  margin: 3rem auto 6rem;
}
```

`src/app/posts/first-post/layout.js` 파일을 열고 다음과 같이 바꿔줍니다.

```tsx
import { ReactNode } from 'react'
import styles from './layout.module.css'

export default function PostLayout({children}
    : {children: ReactNode}
    ) {
    return (
        <div className={styles.container}>{children}</div>
    )
}
```

### 고유한 클래스 이름을 자동으로 생성

자동으로 고유 한 클래스 이름을 생성합니다. CSS 모듈을 사용하는 한 클래스 이름 충돌에 대해 걱정할 필요가 없습니다.

또한 Next.js의 코드 분할 기능은 CSS 모듈에서도 작동합니다. 각 페이지에 대해 최소한의 CSS가 로드되도록 합니다. 그 결과 번들 크기가 작아집니다.

CSS 모듈은 빌드 시 JavaScript 번들에서 추출되며 Next.js에 의해 자동으로 로드되는 파일을 생성합니다

## 전역 스타일

CSS 모듈은 구성 요소 수준 스타일에 유용합니다. 그러나 **모든 페이지에서** 일부 CSS를로드하려면 Next.js도 지원합니다.

### 전역 CSS 추가

Next.js에서는 `app/layout.js`에서 전역 CSS 파일을 가져와서 추가할 수 있습니다. 다른 곳에서는 전역 CSS를 가져올 **수 없습니다**.

전역 CSS를 외부로 가져올 수 없는 이유는 전역 CSS가 페이지의 모든 요소에 영향을 미치기 때문입니다.

홈페이지에서 페이지로 이동하면 홈페이지의 전역 스타일이 의도치 않게 영향을 줄 수 있습니다.`/posts/first-post`

전역 CSS 파일을 아무 곳에나 배치하고 원하는 이름을 사용할 수 있습니다.

- 최상위 디렉터리에서 `app/global.css` 를 생성합니다.
- 해당 내용을 `app/layout.js`에 가져옵니다.

```tsx
import './globals.css'

// ... meta 데이터 생략

export default function RootLayout({ children, }: { children: React.ReactNode }) {
  return (// ... 코드 생략);
}
```