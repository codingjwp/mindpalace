# Nextjs 앱 만들기

React로 완전한 웹 애플리케이션을 처음부터 빌드하려면 고려해야 할 중요한 세부 사항이 많이 있습니다.

- 코드는 webpack과 같은 번들러를 사용하여 번들로 묶고 Babel과 같은 컴파일러를 사용하여 변환해야 합니다.
- 코드 분할과 같은 프로덕션 최적화를 수행해야 합니다.
- 성능 및 SEO를 위해 일부 페이지를 정적으로 미리 렌더링할 수 있습니다. 서버 쪽 렌더링 또는 클라이언트 쪽 렌더링을 사용할 수도 있습니다.
- React 앱을 데이터 저장소에 연결하기 위해 서버 측 코드를 작성해야 할 수도 있습니다.

프레임워크는 이러한 문제를 해결할 수 있습니다. 그러나 그러한 프레임 워크는 적절한 수준의 추상화를 가져야합니다.

그렇지 않으면 그다지 유용하지 않습니다. 또한 훌륭한 "개발자 경험"이 있어야 코드를 작성하는 동안 귀하와 귀하의 팀이 놀라운 경험을 할 수 있습니다.

## Next.js React 프레임워크

Next.js 위의 모든 문제에 대한 솔루션을 제공합니다. 그러나 더 중요한 것은 React 애플리케이션을 구축할 때 귀하와 귀하의 팀을 성공의 구덩이에 빠뜨린다는 것입니다.

Next.js는 동급 최고의 개발자 경험과 다음과 같은 많은 기본 제공 기능을 제공하는 것을 목표로 합니다.

- 직관적인 페이지 기반 라우팅 시스템(동적 경로 지원)
- 사전 렌더링, 정적 생성(SSG) 및 서버 측 렌더링(SSR)은 모두 페이지별로 지원됩니다
- 더 빠른 페이지 로드를 위한 자동 코드 분할
- 최적화된 프리페치를 사용하는 클라이언트 쪽 라우팅Client-side routing with optimized prefetching
- 내장 CSS 및 Sass 지원, 모든 CSS-in-JS 라이브러리 지원
- 빠른 새로 고침을 지원하는 개발 환경
- 서버리스 함수를 사용하여 API 엔드포인트를 빌드하기 위한 API 경로
- 완전 확장 가능

이 자습서에서는 매우 간단한 **블로그 앱을** 만들어 Next.js 기본 사항을 배웁니다. 최종 결과의 예는 다음과 같습니다.

- **[https://next-learn-starter.vercel.app](https://next-learn-starter.vercel.app/)**

## 설치

먼저 개발 환경이 준비되었는지 확인합니다.

- **Node.js**가 설치되어 있지 않은 경우 [여기에서 설치합니다](https://nodejs.org/en/). Node.js 버전 **18** 이상이 필요합니다.
- 이 자습서에서는 사용자 고유의 텍스트 편집기와 터미널 앱을 사용하게 됩니다.

> Windows를 사용하는 경우 [Windows용 Git을 다운로드](https://gitforwindows.org/)하고 이 자습서의 UNIX 관련 명령을 지원하는 Git Bash를 사용하는 것이 좋습니다. WSL([Linux용 Windows 하위 시스템](https://docs.microsoft.com/en-us/windows/wsl/install-win10))은 또 다른 옵션입니다.

### Next.js 앱 만들기

Next.js 앱을 만들려면 터미널을 열고 앱을 만들려는 디렉터리로 이동한 후 다음 명령을 실행합니다

```bash
npx create-next-app@latest nextjs-blog --use-npm --example "https://github.com/vercel/next-learn/tree/main/basics/learn-starter"
```

> 위에 코드 예제는 Page Router를 이용한 예제로 현재 App Router용으로 고쳐서 예제를 적용하기로 하였습니다.

```bash
npx create-next-app@latest nextjs-blog
```

### 개발 서버 실행

이제 라는 새 디렉터리가 생겼습니다. `nextjs-blogcd` 폴더로 들어가 보자

```bash
cd nextjs-blog
```

그런 다음, 다음 명령을 실행합니다.

```bash
npm run dev
```

이렇게 하면 포트 **3000**에서 Next.js 앱의 **개발 서버**가 시작됩니다.

작동하는지 확인해 보겠습니다. 브라우저에서 http://localhost:3000 엽니다.

### Next.js에 오신 것을 환영합니다.

http://localhost:3000 에 액세스할 때 이와 같은 페이지가 표시되어야 합니다. 

Next.js에 대한 몇 가지 유용한 정보를 보여주는 시작 템플릿 페이지입니다.

### 페이지 편집

시작 페이지를 편집해 보겠습니다.

- Next.js 개발 서버가 아직 실행 중인지 확인합니다.
- ~~텍스트 편집기로 엽니다.`pages/index.js`~~

> `src/app/page.js` 부분을 엽니다.

- 태그 아래에서 "**Welcome to"**라는 텍스트를 찾아 "**Learn"**으로 변경합니다.`<h1>`
- 파일을 저장합니다.

파일을 저장하자마자 브라우저는 자동으로 페이지를 새 텍스트로 업데이트합니다.

Next.js 개발 서버에는 빠른 새로 고침이 사용하도록 설정되어 있습니다.

파일을 변경하면 Next.js가 브라우저에 변경 사항을 거의 즉시 자동으로 적용합니다. 새로 고칠 필요가 없습니다! 이렇게 하면 앱을 빠르게 반복하는 데 도움이 됩니다.

## 페이지 간 탐색

### 배울 내용

- ~~통합 파일 시스템 라우팅을 사용하여 새 페이지를 만듭니다.~~

> App Router 버전으로 페이지를 만듭니다.

- `Link` 구성 요소를 사용하여 페이지 간 클라이언트 사이드 탐색을 사용하도록 설정하는 방법을 알아봅니다.
- 코드 분할 및 프리페치에 대한 기본 제공 지원에 대해 알아봅니다.

### 새 페이지 만들기

~~페이지는 **파일 이름을** 기준으로 경로와 연결됩니다. 예를 들어, 개발 중에는 다음과 같습니다.~~

> **폴더 이름과 폴더 하위 파일 page.js** 기준으로 경로와 연결됩니다. 예를 들어 개발 중에는 다음과 같습니다.

- `~~pages/index.js` 은(는) `/` 경로와 연결됩니다.~~

> `src/app/page.js` 는 `/` 경로와 연결됩니다.
- `~~pages/posts/first-post.js`은(는) `/posts/first-post` 경로와 연결됩니다.~~

> `src/app/posts/first-post/page.js` 는 `/posts/first-post` 경로와 연결됩낟.

 `~~pages`아래에 디렉터리를 만듭니다.`posts`~~

> `src/app` 아래에 `posts` 디렉터리를 만듭니다

~~다음 내용으로  `posts`디렉터리 내부에 호출된 파일을 만듭니다.`first-post.js`~~

> `src/app/posts` 아래에 `first-post` 디렉토리를 만들고 `first-post` 디렉토리 내부에 `page.js`를 만듭니다.

```tsx
export default function FirstPost() {
  return <h1>First Post</h1>;
}
```

구성 요소의 이름은 임의로 지정할 수 있지만 `export default`로 내보내야 합니다.

이제 개발 서버가 실행 중인지 확인하고 `http://localhost:3000/posts/first-post` 방문하십시오.

### 링크 구성 요소

웹 사이트의 페이지를 연결할 때 HTML 태그를 사용합니다.`<a>`

Next.js에서 구성 요소 `next/link`를 사용하여 응용 프로그램의 페이지 사이를 연결할 수 있습니다. `<Link>` 태그는 클라이언트 쪽 탐색을 수행할 수 있으며 탐색 동작을 더 잘 제어할 수 있는 props를 허용합니다.

### `<Link>` 사용

~~먼저 `pages/index.js`를 열고 맨 위에 다음 줄을 추가하여 `next/link`에서 구성 요소를 가져옵니다.~~

> 먼저 `src/app/page.js`를 열고 맨 위에 다음 줄을 추가하여 `next/link`에서 구성 요소를 가져옵니다.

```tsx
import Link from 'next/link';
```

그런 다음 다음과 `h1` 태그를 찾습니다. 그것을 다음과 같이 변경하세요.

```tsx
<h1 className={styles.title}>
  Read <Link href="/posts/first-post">this page!</Link>
</h1>
```

~~그런 다음 `pages/posts/first-post.js`  콘텐츠를 열고 다음으로 바꿉니다.~~

> 그런 다음 `src/app/posts/first-post/page.js` 콘텐츠를 열고 다음으로 바꿉니다.

```tsx
import Link from 'next/link';

export default function FirstPost() {
  return (
    <>
      <h1>First Post</h1>
      <h2>
        <Link href="/">Back to home</Link>
      </h2>
    </>
  );
}
```

보시다시피 구성 요소는 `<a href="…">` 태그를 사용하는 것과 비슷하지만 대신 `<Link href="…">` 를 적용합니다.

### 클라이언트 측 탐색

`Link` 구성 요소를 사용하면 동일한 Next.js 앱에서 두 페이지 간에 **클라이언트 쪽을 탐색**할 수 있습니다.

클라이언트 쪽 탐색은 **JavaScript를 사용하여** 페이지 전환이 발생한다는 것을 의미하며, 이는 브라우저에서 수행하는 기본 탐색보다 빠릅니다.

확인할 수 있는 간단한 방법은 다음과 같습니다.

- 브라우저의 개발자 도구를 사용하여 `<html>`의 CSS 속성을 `background: yellow;` 로 변경합니다.
- 링크를 클릭하면 두 페이지 사이를 오갈 수 있습니다.
- 페이지 전환 사이에 노란색 배경이 유지되는 것을 볼 수 있습니다.

이는 브라우저가 전체 페이지를 로드하지 않고 클라이언트 쪽 탐색이 작동하고 있음을 보여 줍니다.

`<Link href="…">` 말고 `<a href="…">` 이 작업을 수행한 경우 브라우저가 완전히 새로 고쳐지기 때문에 링크 클릭 시 배경색이 지워집니다.

### 코드 분할 및 프리페치

Next.js 코드 분할을 자동으로 수행하므로 각 페이지는 해당 페이지에 필요한 것만 로드합니다. 즉, 홈페이지가 렌더링될 때 다른 페이지의 코드는 처음에 제공되지 않습니다.

이렇게 하면 수백 페이지가 있는 경우에도 홈페이지가 빠르게 로드됩니다.

요청한 페이지의 코드만 로드하면 페이지가 격리됩니다. 특정 페이지에서 오류가 발생해도 나머지 응용 프로그램은 계속 작동합니다.

또한 Next.js의 프로덕션 빌드에서는 링크 구성 요소가 브라우저의 뷰포트에 나타날 때마다 Next.js는 백그라운드에서 `링크`된 페이지의 코드를 자동으로 **프리페치합니다**.

링크를 클릭할 때쯤이면 대상 페이지의 코드가 이미 백그라운드에 로드되고 페이지 전환이 거의 즉각적으로 이루어집니다!

**메모:** Next.js 앱 외부의 외부 페이지에 연결해야 하는 경우 `<a>` 태그 사용합니다.