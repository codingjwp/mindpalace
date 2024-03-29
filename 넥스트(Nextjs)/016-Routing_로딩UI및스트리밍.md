# 로딩 UI 및 스트리밍

특수 파일 `loading.js`를 사용하면 **React Suspense**로 의미 있는 로딩 UI를 만들 수 있습니다.

이 규칙을 사용하면 경로 세그먼트의 콘텐츠가 로드되는 동안 서버에서 즉각적인 로딩 상태를 표시할 수 있습니다.

렌더링이 완료되면 새 콘텐츠가 자동으로 교체됩니다.

![Untitled](https://github.com/codingjwp/mindpalace/assets/113403155/b45de816-9f07-4684-9101-7fd59243b57a)

## 즉시 로딩 상태

즉시 로딩 상태는 탐색 시 즉시 표시되는 폴백 UI입니다.

스켈레톤, 스피너와 같은 로딩 표시기를 미리 렌더링하거나 표지 사진, 제목 등과 같이 작지만 의미 있는 부분을 향후 화면에 표시할 수 있습니다.

이를 통해 사용자가 앱이 반응하고 있음을 이해하고 더 나은 사용자 경험을 제공할 수 있습니다.

폴더 안에 `loading.js` 파일을 추가하여 로딩 상태를 만듭니다.

![Untitled 1](https://github.com/codingjwp/mindpalace/assets/113403155/e0f89104-36fe-4239-b7f6-74cacb520d2f)

```tsx
export default function Loading() {
  // 스켈레톤을 포함한 모든 UI를 로딩 내에 추가할 수 있습니다.
  return <LoadingSkeleton />
}
```

같은 폴더에서 `loading.js`는 `layout.js` 안에 중첩됩니다. `page.js` 파일과 그 아래의 모든 하위 파일은 자동으로 `<Suspense>` 경계로 감싸집니다.

![Untitled 2](https://github.com/codingjwp/mindpalace/assets/113403155/517621d2-2731-4f2c-a38b-911aa08d9770)

### 알아두면 좋습니다

- 서버 중심 라우팅을 사용하더라도 내비게이션이 즉시 실행됩니다.
- 내비게이션은 중단 없이 진행되므로 경로를 변경할 때 다른 경로로 이동하기 전에 경로의 콘텐츠가 완전히 로드될 때까지 기다릴 필요가 없습니다.
- 새 경로 세그먼트가 로드되는 동안 공유 레이아웃은 대화형 상태로 유지됩니다.

### 권장 사항

Next.js가 이 기능을 최적화하므로 경로 세그먼트(레이아웃 및 페이지)에 `loading.js` 규칙을 사용하세요.

## 서스펜스 스트리밍

`loading.js` 외에도 자체 UI 컴포넌트에 대한 `Suspense` 바운더리를 수동으로 생성할 수도 있습니다. 

앱 라우터는 Node.js 및 Edge 런타임 모두에 대해 `Suspense`를 사용한 스트리밍을 지원합니다.

### 스트리밍이란 무엇인가요?

React와 Next.js에서 스트리밍이 어떻게 작동하는지 알아보려면 **서버 사이드 렌더링(SSR)**과 그 한계를 이해하는 것이 도움이 됩니다.

SSR을 사용하면 사용자가 페이지를 보고 상호 작용하기 전에 완료해야 하는 일련의 단계가 있습니다:

- 먼저 지정된 페이지의 모든 데이터를 서버에서 가져옵니다.
- 그런 다음 서버는 페이지의 HTML을 렌더링합니다.
- 페이지의 HTML, CSS 및 JavaScript가 클라이언트로 전송됩니다.
- 생성된 HTML과 CSS를 사용해 비대화형 사용자 인터페이스가 표시됩니다.
- 마지막으로 React는 사용자 인터페이스에 수분을 공급하여 대화형 인터페이스로 만듭니다.

![Untitled 3](https://github.com/codingjwp/mindpalace/assets/113403155/956496dd-b646-4553-9b4c-7549e251e201)

이러한 단계는 순차적이고 차단적이므로 서버는 모든 데이터를 가져온 후에만 페이지의 HTML을 렌더링할 수 있습니다. 그리고 클라이언트에서는 페이지의 모든 컴포넌트에 대한 코드가 다운로드된 후에야 React가 UI에 hydrate을 공급할 수 있습니다.

React 및 Next.js를 사용한 SSR은 사용자에게 비대화형 페이지를 최대한 빨리 보여줌으로써 체감 로딩 성능을 개선하는 데 도움이 됩니다.

![Untitled 4](https://github.com/codingjwp/mindpalace/assets/113403155/086dc0ce-2d9e-4ab6-9afb-42ff6c7a755e)

하지만 사용자에게 페이지를 표시하기 전에 서버에서 모든 데이터 가져오기를 완료해야 하므로 여전히 느릴 수 있습니다.

스트리밍을 사용하면 페이지의 HTML을 더 작은 청크로 나누고 해당 청크를 서버에서 클라이언트로 점진적으로 전송할 수 있습니다.

![Untitled 5](https://github.com/codingjwp/mindpalace/assets/113403155/f24450aa-b132-4c5d-a1f2-bb15530dcec1)

이렇게 하면 UI를 렌더링하기 전에 모든 데이터가 로드될 때까지 기다릴 필요 없이 페이지의 일부를 더 빨리 표시할 수 있습니다.

스트리밍은 각 컴포넌트를 청크로 간주할 수 있기 때문에 React의 컴포넌트 모델과 잘 작동합니다.

우선순위가 높거나(예: 제품 정보) 데이터에 의존하지 않는 컴포넌트(예: 레이아웃)를 먼저 전송할 수 있으며, React는 하이드레이션을 더 일찍 시작할 수 있습니다.

우선순위가 낮은 컴포넌트(예: 리뷰, 관련 제품)는 데이터를 가져온 후 동일한 서버 요청으로 전송할 수 있습니다.

![Untitled 6](https://github.com/codingjwp/mindpalace/assets/113403155/69076544-7192-4b75-a0d5-72f8a5cb06de)

스트리밍은 긴 데이터 요청으로 인해 페이지 렌더링이 차단되는 것을 방지할 때 특히 유용합니다. 스트리밍은 TTFB(Time To First Byte)와 FCP(First Contentful Paint)를 줄일 수 있기 때문입니다.

또한 특히 느린 디바이스에서 인터랙티브 타임 투 인터랙티브(TTI)를 개선하는 데 도움이 됩니다.

### 예제

`<Suspense>`는 비동기 액션(예: 데이터 불러오기)을 수행하는 컴포넌트를 래핑하고, 액션이 진행되는 동안 폴백 UI(예: 스켈레톤, 스피너)를 표시한 다음, 액션이 완료되면 컴포넌트를 교체하는 방식으로 작동합니다.

```tsx
import { Suspense } from 'react'
import { PostFeed, Weather } from './Components'
 
export default function Posts() {
  return (
    <section>
      <Suspense fallback={<p>Loading feed...</p>}>
        <PostFeed />
      </Suspense>
      <Suspense fallback={<p>Loading weather...</p>}>
        <Weather />
      </Suspense>
    </section>
  )
}
```

Suspense를 사용하면 다음과 같은 이점을 얻을 수 있습니다.

- **스트리밍 서버 렌더링** - 서버에서 클라이언트로 HTML을 점진적으로 렌더링합니다.
- **선택적** **Hydration** - React는 사용자 상호작용에 따라 어떤 컴포넌트를 먼저 인터랙티브하게 만들지 우선순위를 정합니다.

### SEO

- Next.js는 `generateMetadata` 내부의 데이터 가져오기가 완료될 때까지 기다렸다가 클라이언트로 UI를 스트리밍합니다. 이렇게 하면 스트리밍된 응답의 첫 부분에 `<head>` 태그가 포함되도록 보장합니다.
- 스트리밍은 서버에서 렌더링되므로 SEO에 영향을 미치지 않습니다. Google의 모바일 친화적 테스트 도구를 사용하여 Google의 웹 크롤러에 페이지가 어떻게 표시되는지 확인하고 직렬화된 HTML을 볼 수 있습니다.

### 상태 코드들

스트리밍할 때 요청이 성공했음을 알리는 `200` 상태 코드가 반환됩니다.

서버는  `redirect` or `notFound`. 등을 사용하여 스트리밍된 콘텐츠 자체 내에서 오류나 문제를 클라이언트에 전달할 수 있습니다.

응답 헤더가 이미 클라이언트에 전송되었으므로 응답의 상태 코드는 업데이트할 수 없습니다. 이는 SEO에 영향을 미치지 않습니다.

