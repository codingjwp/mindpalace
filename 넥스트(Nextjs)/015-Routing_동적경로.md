# 동적 경로

정확한 세그먼트 이름을 미리 알지 못하고 동적 데이터로 경로를 생성하려는 경우 요청 시 채워지거나 빌드 시 미리 렌더링되는 동적 세그먼트를 사용할 수 있습니다.

## 규칙

동적 세그먼트는 폴더 이름을 대괄호로 묶어 만들 수 있습니다. [`폴더 이름`]. 예: `[id]` 또는 `[slug]`.

동적 세그먼트는 레이아웃, 페이지, 라우팅 및 생성 메타데이터 함수에 매개변수로 전달됩니다.

## 예제

예를 들어 블로그에는 다음과 같은 경로 `app/blog/[slug]/page.js`가 포함될 수 있으며, 여기서 `[slug]`는 블로그 게시물의 동적 세그먼트입니다.

```tsx
export default function Page({ params }: { params: { slug: string } }) {
  return <div>My Post: {params.slug}</div>
}
```

| Route | Example URL | params |
| --- | --- | --- |
| app/blog/[slug]/page.js | /blog/a | { slug: 'a' } |
| app/blog/[slug]/page.js | /blog/b | { slug: 'b' } |
| app/blog/[slug]/page.js | /blog/c | { slug: 'c' } |

### 알아두면 좋습니다

동적 세그먼트는 페이지 디렉터리의 동적 경로와 동일합니다.

## 정적 매개변수 생성

`generateStaticParam` 함수는 동적 경로 세그먼트와 함께 사용하여 요청 시 on-demand가 아닌 빌드 시점에 정적으로 경로를 생성할 수 있습니다.

```tsx
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await fetch('https://.../posts').then((res) => res.json())
 
  return posts.map((post) => ({
    slug: post.slug,
  }))
}
```

이 기능의 가장 큰 장점은 데이터를 스마트하게 검색할 수 있다는 점입니다. 요청을 사용하여 함수 내에서 콘텐츠를 가져오면 요청이 자동으로 메모화됩니다.

즉, 여러 개의 , 레이아웃 및 페이지에 걸쳐 동일한 인수를 가진 요청을 한 번만 수행하므로 빌드 시간이 단축됩니다.

`generateStaticParams`, `generateStaticParams`, `fetch`, `fetchgenerateStaticParams`

## **Catch-all Segments**

동적 세그먼트는 괄호 안에 줄임표를 추가하여 `[...folderName]`을 추가하여 모든 후속 세그먼트를 포괄하는 세그먼트로 확장할 수 있습니다.

예를 들어 `,` 뿐만 아니라 `, ,` 등과도 일치합니다.

`app/shop/[...slug]/page.js`, `/shop/clothes`, `/shop/clothes/tops`, `/shop/clothes/tops/t-shirts`

| Route | Example URL | params |
| --- | --- | --- |
| app/shop/[...slug]/page.js | /shop/a | { slug: ['a'] } |
| app/shop/[...slug]/page.js | /shop/a/b | { slug: ['a', 'b'] } |
| app/shop/[...slug]/page.js | /shop/a/b/c | { slug: ['a', 'b', 'c'] } |

## **Optional Catch-all Segments**

캐치올 세그먼트는 매개 변수를 이중 대괄호 안에 포함하여 선택 사항으로 만들 수 있습니다.

`[[...folderName]]`

예를 들어 `, ,` 외에도 `,` 와도 일치합니다.

`app/shop/[[...slug]]/page.js`, `/shop` `/shop/clothes`, `/shop/clothes/tops`, `/shop/clothes/tops/t-shirts`

**Catch-all Segments**와 **Optional Catch-all Segments**의 차이점은 **Optional Catch-all Segments**를 사용하면 매개 변수가 없는 경로(`/shop`)도 일치한다는 점입니다. 

| Route | Example URL | params |
| --- | --- | --- |
| app/shop/[[...slug]]/page.js | /shop | {} |
| app/shop/[[...slug]]/page.js | /shop/a | { slug: ['a'] } |
| app/shop/[[...slug]]/page.js | /shop/a/b | { slug: ['a', 'b'] } |
| app/shop/[[...slug]]/page.js | /shop/a/b/c | { slug: ['a', 'b', 'c'] } |

## 타입스크립트

TypeScript를 사용하는 경우 구성된 경로 세그먼트(`params`)에 따라 유형을 추가할 수 있습니다.

```tsx
export default function Page({ params }: { params: { slug: string } }) {
  return <h1>My Page</h1>
}
```

| Route | params Type Definition |
| --- | --- |
| app/blog/[slug]/page.js | { slug: string } |
| app/shop/[...slug]/page.js | { slug: string[] } |
| app/[categoryId]/[itemId]/page.js | { categoryId: string, itemId: string } |

### 알아두면 좋습니다.

이 작업은 향후 TypeScript 플러그인에 의해 자동으로 수행될 수 있습니다.