# 페이지 및 레이아웃

## 페이지

페이지는 경로에 고유한 UI입니다.

`page.js` 파일에서 컴포넌트를 내보내 페이지를 정의할 수 있습니다.

중첩 폴더를 사용하여 경로를 정의하고 `page.js` 파일을 사용하여 경로를 공개적으로 액세스할 수 있도록 합니다.

`app` 디렉터리 내에 `page.js` 파일을 추가하여 첫 번째 페이지를 만듭니다

![Untitled](https://github.com/codingjwp/mindpalace/assets/113403155/737509f6-c591-4eeb-956e-2b782e48afca)

```tsx
// `app/page.tsx`는 `/` URL의 UI입니다.
export default function Page() {
  return <h1>Hello, Home page!</h1>
}
```

```tsx
// `app/dashboard/page.tsx`는 
// `/dashboard` URL의 UI입니다.
export default function Page() {
  return <h1>Hello, Dashboard Page!</h1>
}
```

### 알아두면 좋습니다:

- 페이지는 항상 경로 하위 트리의 리프입니다.
- 경로 세그먼트에 공개적으로 액세스하려면 `page.js` 파일이 필요합니다.
- 페이지는 기본적으로 서버 컴포넌트이지만 클라이언트 컴포넌트로 설정할 수 있습니다.
- 페이지는 데이터를 가져올 수 있습니다. 자세한 내용은 데이터 가져오기 섹션을 참조하세요.

## 레이아웃

레이아웃은 여러 페이지 간에 공유되는 UI입니다.

탐색에서 레이아웃은 상태를 보존하고 대화형 상태를 유지하며 다시 렌더링하지 않습니다.

레이아웃은 중첩될 수도 있습니다.

레이아웃은 `default`으로 `layout.js` 파일에서 React 컴포넌트를 내보내서 정의할 수 있습니다.

컴포넌트는 렌더링 중에 자식 레이아웃(있는 경우) 또는 자식 페이지로 채워질 자식 프로퍼티를 받아들여야 합니다.

![Untitled 1](https://github.com/codingjwp/mindpalace/assets/113403155/0a93733d-3192-49b4-8513-965bebbbfdee)

### 알아두면 좋습니다

- 가장 위에 있는 레이아웃을 루트 레이아웃이라고 합니다. 이 필수 레이아웃은 애플리케이션의 모든 페이지에서 공유됩니다. 루트 레이아웃에는 `html`및 `body` 태그가 포함되어야 합니다.
- 모든 경로 세그먼트는 선택적으로 자체 레이아웃을 정의할 수 있습니다. 이러한 레이아웃은 해당 세그먼트의 모든 페이지에서 공유됩니다.
- 경로의 레이아웃은 기본적으로 중첩됩니다. 각 부모 레이아웃은 React 자식 프로퍼티를 사용하여 그 아래의 자식 레이아웃을 감싸줍니다.
- 라우트 그룹을 사용하여 공유 레이아웃 안팎에서 특정 라우트 세그먼트를 선택할 수 있습니다.
- 레이아웃은 기본적으로 서버 컴포넌트이지만 클라이언트 컴포넌트로 설정할 수 있습니다.
- 레이아웃은 데이터를 불러올 수 있습니다.
- 상위 레이아웃과 하위 레이아웃 간에 데이터를 전달할 수 없습니다. 그러나 경로에서 동일한 데이터를 두 번 이상 가져올 수 있으며, React는 성능에 영향을 주지 않고 요청을 자동으로 중복 제거합니다.
- 레이아웃은 그 아래의 경로 세그먼트에 접근할 수 없습니다. 모든 경로 세그먼트에 접근하려면 클라이언트 컴포넌트에서 `useSelectedLayoutSegment` 또는 `useSelectedLayoutSegments`를 사용할 수 있습니다.
- `layout.js` 파일과 `page.js` 파일은 같은 폴더에 정의할 수 있습니다. 레이아웃이 페이지를 감싸게 됩니다.

### 루트 레이아웃(필수)

루트 레이아웃은 `app` 디렉터리의 최상위 레벨에 정의되며 모든 경로에 적용됩니다.

이 레이아웃을 사용하면 서버에서 반환된 초기 HTML을 수정할 수 있습니다.

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

### 알아두면 좋습니다

- 기본 제공 SEO 지원 기능을 사용하여 `<head>` HTML 요소(예: `<title>` 요소)를 관리할 수 있습니다.

### 중첩 레이아웃

폴더(`app/dashboard/layout.js`) 내에 정의된 레이아웃은 특정 경로 세그먼트(`acme.com/dashboard`)에 적용되며 해당 세그먼트가 활성화될 때 렌더링됩니다.

기본적으로 파일 계층 구조의 레이아웃은 **중첩**되어 있으므로 하위 레이아웃이 하위 프로퍼티를 통해 하위 레이아웃을 감싸게 됩니다.

![Untitled 2](https://github.com/codingjwp/mindpalace/assets/113403155/fa81b5e9-6c9a-4434-b89a-925d03755092)

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return <section>{children}</section>
}
```

위의 두 레이아웃을 결합하면 루트 레이아웃(`app/layout.js`)이 대시보드 레이아웃(`app/dashboard/layout.js`)을 감싸고, 대시보드 레이아웃은 `app/dashboard/*`을 감싸게 됩니다.

두 레이아웃은 이렇게 중첩됩니다.

![Untitled 3](https://github.com/codingjwp/mindpalace/assets/113403155/2432c18e-99d4-4dfb-82ff-834ebc1a70df)

경로 그룹을 사용하여 공유 레이아웃 안팎에서 특정 경로 세그먼트를 선택할 수 있습니다.

## 템플릿

템플릿은 각 하위 레이아웃 또는 페이지를 래핑한다는 점에서 레이아웃과 유사합니다.

경로 전체에서 지속되고 상태를 유지하는 레이아웃과 달리 템플릿은 탐색 시 각 하위 레이아웃에 대해 새 인스턴스를 생성합니다.

즉, 사용자가 템플릿을 공유하는 경로 사이를 탐색할 때 컴포넌트의 새 인스턴스가 마운트되고, DOM 요소가 다시 생성되며, 상태가 보존되지 않고, 효과가 다시 동기화됩니다.

**특정 동작이 필요한 경우 레이아웃보다 템플릿이 더 적합한 옵션이 될 수 있습니다.**

예를 들어 :

- 사용 효과(예: 페이지 조회 수 로깅) 및 사용 상태(예: 페이지별 피드백 양식)에 의존하는 기능입니다.
- 기본 프레임워크 동작을 변경합니다.
- 예를 들어 레이아웃 내부의 Suspense 경계는 레이아웃을 처음 로드할 때만 fallback을 표시하고 페이지를 전환할 때는 표시하지 않습니다.
- 템플릿의 경우 각 탐색에 fallback이 표시됩니다.

템플릿은 `template.js` 파일에서 기본 React 컴포넌트를 내보내서 정의할 수 있습니다.

컴포넌트는 `children` 프로퍼티를 허용해야 합니다.

![Untitled 4](https://github.com/codingjwp/mindpalace/assets/113403155/63185878-7ff5-414d-bbf5-9055ab6cc375)

```tsx
// app/template.tsx
export default function Template({ children }: { children: React.ReactNode }) {
  return <div>{children}</div>
}
```

중첩 측면에서 `template.js`는 레이아웃과 그 자식 사이에 렌더링됩니다. 다음은 단순화된 출력입니다.

```tsx
<Layout>
  {/* 템플릿에는 고유 키가 부여됩니다. */}
  <Template key={routeParam}>{children}</Template>
</Layout>
```

## `<head>` 수정하기

`app` 디렉토리에서 기본 제공 SEO 지원을 사용하여 `title` 및 `meta`등의 `<head>` HTML 요소를 수정할 수 있습니다.

메타데이터는 `metadata`객체를 내보내거나 `layout.js` 또는 `page.js` 파일에서 `generateMetadata` 함수를 사용하여 정의할 수 있습니다.

```tsx
// app/page.tsx
import { Metadata } from 'next'
 
export const metadata: Metadata = {
  title: 'Next.js',
}
 
export default function Page() {
  return '...'
}
```

### 알아두면 좋습니다:

루트 레이아웃에 `<title>` 및 `<meta>`와 같은 `<head>` 태그를 수동으로 추가해서는 안 됩니다.

대신 `<head>` 요소의 스트리밍 및 중복 제거와 같은 고급 요구 사항을 자동으로 처리하는 **메타데이터 API**를 사용해야 합니다.