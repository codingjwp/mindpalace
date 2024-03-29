# 경로 정의

## 라우터 만들기

Next.js는 폴더가 경로를 정의하는 데 사용되는 파일 시스템 기반 라우터를 사용합니다.

각 폴더는 URL 세그먼트에 매핑되는 경로 세그먼트를 나타냅니다. 

중첩된 경로를 만들려면 폴더를 서로 중첩할 수 있습니다.

![Untitled](https://github.com/codingjwp/mindpalace/assets/113403155/008b96a5-bd12-41cf-9953-c72f66a6583a)

경로 세그먼트를 공개적으로 액세스할 수 있도록 하기 위해 특별한 `page.js` 파일이 사용됩니다.

![Untitled 1](https://github.com/codingjwp/mindpalace/assets/113403155/a61fc57f-fd06-4319-8b13-32757f67d464)

이 예제에서는 `/dashboard/analytics` URL 경로에 해당 `page.js` 파일이 없기 때문에 공개적으로 액세스할 수 없습니다.

이 폴더는 구성 요소, 스타일시트, 이미지 또는 기타 함께 배치된 파일을 저장하는 데 사용할 수 있습니다.

## UI 만들기

각 경로 세그먼트에 대한 UI를 생성하기 위해 특별한 파일 규칙이 사용됩니다.

가장 일반적인 것은 경로에 고유한 UI를 표시하는 페이지와 여러 경로에서 공유되는 UI를 표시하는 레이아웃입니다.

예를 들어 첫 번째 페이지를 만들려면 `app` 디렉토리에 `page.js` 파일을 추가하고 React 컴포넌트를 내보내면 됩니다

```tsx
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```