# 연결 및 탐색

Next.js에서 경로 사이를 탐색하는 방법에는 두 가지가 있습니다

- `<Link>` 컴포넌트 사용하기
- `useRouter` 훅 사용하기

## **`<Link>` Component**

`<Link>`는 HTML `<a>` 태그를 확장하여 경로 간 프리페칭 및 클라이언트 측 탐색을 제공하는 기본 제공 컴포넌트입니다.

Next.js에서 경로 간을 탐색하는 기본 방법입니다.

```tsx
import Link from 'next/link'
 
export default function Page() {
  return <Link href="/dashboard">Dashboard</Link>
}
```

`<Link>`에서 전달할 수 있는 props도 존재합니다.

## 예제

### 동적 세그먼트에 링크하기

동적 세그먼트에 링크할 때 템플릿 리터럴 및 표현식 사입법을 사용하여 링크 목록을 생성할 수 있습니다.

```tsx
import Link from 'next/link'
 
export default function PostList({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>
          <Link href={`/blog/${post.slug}`}>{post.title}</Link>
        </li>
      ))}
    </ul>
  )
}
```

### 활성 링크 확인

`usePathname()`을 사용하여 링크가 활성 상태인지 확인할 수 있습니다. 

예를 들어 활성 링크에 클래스를 추가하려면 현재 `pathname`이 링크의 `href`와 일치하는지 확인할 수 있습니다

```tsx
'use client'
 
import { usePathname } from 'next/navigation'
import Link from 'next/link'
 
export function Links() {
  const pathname = usePathname()
 
  return (
    <nav>
      <ul>
        <li>
          <Link className={`link ${pathname === '/' ? 'active' : ''}`} href="/">
            Home
          </Link>
        </li>
        <li>
          <Link
            className={`link ${pathname === '/about' ? 'active' : ''}`}
            href="/about"
          >
            About
          </Link>
        </li>
      </ul>
    </nav>
  )
}
```

### `id`로 스크롤 하기

Next.js 앱 라우터의 기본 동작은 새 경로의 상단으로 스크롤하거나 뒤로 및 앞으로 탐색을 위해 스크롤 위치를 유지하는 것입니다.

탐색에서 특정 아이디로 스크롤하려면 URL에 `#`해시 링크를 추가하거나 `href` 프로퍼티에 해시 링크를 전달하면 됩니다.

이는 `<Link>`가 `<a>` 요소에 렌더링되기 때문에 가능합니다.

```tsx
<Link href="/dashboard#settings">Settings</Link>
 
// Output
<a href="/dashboard#settings">Settings</a>
```

### 스크롤 복원 비활성화

Next.js 앱 라우터의 기본 동작은 새 경로의 상단으로 스크롤하거나 뒤로 및 앞으로 탐색을 위해 스크롤 위치를 유지하는 것입니다.

이 동작을 비활성화하려면 `<Link>` 컴포넌트에 `scroll={false}`를 전달하거나, `router.push()` 또는 `router.replace()`에 `{scroll: false}`를 전달할 수 있습니다.

```tsx
// next/link
<Link href="/dashboard" scroll={false}>
  Dashboard
</Link>
```

```tsx
// useRouter
import { useRouter } from 'next/navigation'
 
const router = useRouter()
 
router.push('/dashboard', { scroll: false })
```

## **`useRouter()` Hook**

`useRouter` 훅사용하면 프로그래밍 방식으로 경로를 변경할 수 있습니다.

이 훅은 클라이언트 컴포넌트 내에서만 사용할 수 있으며 `next/navigation`에서 가져옵니다.

```tsx
'use client'
 
import { useRouter } from 'next/navigation'
 
export default function Page() {
  const router = useRouter()
 
  return (
    <button type="button" onClick={() => router.push('/dashboard')}>
      Dashboard
    </button>
  )
}
```

### 권장 사항

`useRouter` 사용에 대한 특별한 요구 사항이 없는 한 `<Link>` 구성 요소를 사용하여 경로를 탐색하세요.

## 라우팅 및 내비게이션 작동 방식

앱 라우터는 라우팅 및 내비게이션 에 하이브리드 접근 방식을 사용합니다.

서버에서는 애플리케이션 코드가 **경로 세그먼트별로 자동으로 코드 분할됩니다.** 그리고 클라이언트에서는 Next.js가 경로 세그먼트를 프리페치하고 캐싱합니다.

즉, 사용자가 새 경로로 이동할 때 브라우저가 페이지를 새로고침하지 않고 **변경된 경로 세그먼트만 다시 렌더링**하여 내비게이션 환경과 성능을 개선합니다.

### Prefetching

Prefetching은 사용자가 경로를 방문하기 전에 백그라운드에서 경로를 미리 로드하는 방법입니다.

Next.js에서 경로를 프리페치하는 방법에는 두 가지가 있습니다.

- `<Link>` 컴포넌트 : 경로가 사용자의 뷰포트에 표시될 때 자동으로 프리페칭됩니다. 프리페칭은 페이지가 처음 로드될 때 또는 스크롤을 통해 페이지가 시야에 들어올 때 발생합니다.
- `router.preFetch()` : 라우터 훅을 사용하여 프로그래밍 방식으로 경로를 프리페치할 수 있습니다.

정적 경로와 동적 경로에서 `<Link>`의 프리페칭 동작이 다릅니다.

- `Static Routes` : 프리페치 기본값은 참입니다. 전체 경로가 프리페치되어 캐시됩니다.
- `Dynamic Routes` : 프리페치 기본값은 자동입니다. 첫 번째 `loading.js` 파일까지의 공유 레이아웃만 프리페치되고 `30초` 동안 캐시됩니다. 이렇게 하면 전체 동적 경로를 불러오는 데 드는 비용이 절감되며, 로딩 상태를 즉시 표시하여 사용자에게 더 나은 시각적 피드백을 제공할 수 있습니다.

`prefetch` 프로퍼티를 `false`로 설정하여 프리페치를 비활성화할 수 있습니다.

### 알아두면 좋습니다.

프리페칭은 개발 환경에서는 활성화되지 않으며 **프로덕션 환경**에서만 활성화됩니다.

### Caching

Next.js에는 라우터 캐시라는 인메모리 클라이언트 측 캐시가 있습니다.

사용자가 앱을 탐색할 때 미리 가져온 경로 세그먼트와 방문한 경로의 React 서버 컴포넌트 페이로드가 캐시에 저장됩니다.

즉, 탐색 시 서버에 새로운 요청을 하는 대신 캐시를 최대한 재사용하여 요청과 데이터 전송 횟수를 줄여 성능을 개선합니다.

### 부분 렌더링

부분 렌더링은 내비게이션에서 변경되는 경로 세그먼트만 클라이언트에서 다시 렌더링되고 공유 세그먼트는 모두 유지됨을 의미합니다.

예를 들어, 두 형제 경로인 `/dashboard/settings`와 `/dashboard/analytics` 사이를 탐색하는 경우 `settings` 및 `analytics` 페이지가 렌더링되고 공유 `dashboard` 레이아웃은 유지됩니다.

![Untitled](https://github.com/codingjwp/mindpalace/assets/113403155/ef67be30-fd65-4867-9506-48bd43ee2e99)

부분 렌더링이 없으면 탐색할 때마다 전체 페이지가 서버에서 다시 렌더링됩니다.

변경되는 세그먼트만 렌더링하면 전송되는 데이터의 양과 실행 시간이 줄어들어 성능이 향상됩니다.

### Soft Navigation

기본적으로 브라우저는 페이지 간 하드 탐색을 수행합니다.

즉, 브라우저는 페이지를 다시 로드하고 앱의 사용 `useState`훅와 같은 React 상태와 사용자의 스크롤 위치 또는 포커싱된 요소와 같은 브라우저 상태를 재설정합니다.

하지만 Next.js에서 앱 라우터는 소프트 내비게이션을 사용합니다.

즉, React는 React와 브라우저 상태를 유지하면서 변경된 세그먼트만 렌더링하며 전체 페이지를 다시 로드하지 않습니다.

### Back and Forward Navigation

기본적으로 Next.js는 뒤로 및 앞으로 탐색을 위한 스크롤 위치를 유지하고 라우터 캐시에서 경로 세그먼트를 재사용합니다.