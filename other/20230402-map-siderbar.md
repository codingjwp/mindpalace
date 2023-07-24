# map을 사용한 siderbar 만들기
map을 이용하여 Compnent를 만들어 사용하기.

### 사이드바에 사용할 데이터 정보
`Array.prototype.reduce()`는 배열의 각 요소의 값을 하나의 결과값으로 변환  
밑에서는 siderInfo에 있는 데이터중 필요한 부분만을 추출하여 하나의 Array로 변환 
```typescript
//Routers.tsx
const siderInfo: SidebarItems[] = [
  {
    index: true,
    idx: 1000,
    element: <Main />,
    errorElement: <NotFound />,
    linkLabel: "홈",
  },
  {
    idx: 1001,
    path: "musiclist",
    element: <Musiclist />,
    errorElement: <NotFound />,
    linkLabel: "둘러보기",
  },
  {
    idx: 1002,
    path: "mylist",
    element: <Mylist />,
    errorElement: <NotFound />,
    linkLabel: "내리스트",
  },
];

export const siderContent: SidebarItems[] = siderInfo.reduce((prev, current) => {
    if (!current.linkLabel) return prev;

    return [
      ...prev,
      {
        idx: current.idx,
        path: current.index ? "/" : current.path,
        linkLabel: current.linkLabel,
      }
    ]
}, [] as SidebarItems[]);

```

### siderbar 코드
```typescript
import { sideContent } from './Routers'

const SiderBar = () => {
  return (
    <ul>
      {sideContent.map((current) => {
        <li key={current.idx}>
          <a href={current.path}>
            {current.linkLabel}
          </a>
        </li>
      })}
    </ul>
  )
}

export default SiderBar;
```