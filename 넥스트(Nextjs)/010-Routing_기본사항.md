# Routing 기본사항

## 용어

문서 전체에서 이러한 용어가 사용되는 것을 볼 수 있습니다.

![Untitled](https://github.com/codingjwp/mindpalace/assets/113403155/7d5b09b2-eb43-49ac-99d4-1ab5eb241ef2)


**Tree** : 계층 구조를 시각화하기 위한 규칙입니다. 예를 들어 부모 및 자식 컴포넌트가 있는 컴포넌트 트리, 폴더 구조 등이 있습니다.

**Subtree** : 트리의 일부로, 새 루트에서 시작하여 잎에서 끝나는 트리의 일부입니다.

**Root** : 루트 레이아웃과 같은 트리 또는 하위 트리의 첫 번째 노드입니다.

**Leaf** : 하위 트리의 노드 중 자식이 없는 노드입니다.

![Untitled 1](https://github.com/codingjwp/mindpalace/assets/113403155/80dc581e-ab8b-4923-b124-21cc8385f2d6)


**URL 세그먼트** : 슬래시로 구분된 URL 경로의 일부입니다.
**URL 경로** : 도메인 뒤에 오는 URL의 일부입니다.

## 앱 라우터

version 13에서는 공유 레이아웃, 중첩 라우팅, 로딩 상태, 오류 처리 등을 지원하는 **React Server 컴포넌트**를 기반으로 하는 새로운 **App Router**가 도입되었습니다.

**App Router**는 `app`이라는 새 디렉토리에서 작동합니다. `app` 디렉토리는 `pages` 디렉터리와 함께 작동하여 점진적인 적용이 가능합니다.

따라서 애플리케이션의 일부 경로를 새 동작으로 선택하면서 다른 경로는 이전 동작을 위해 `pages`디렉터리에 유지할 수 있습니다.

애플리케이션에서 `pages`디렉터리를 사용하는 경우 **Pages Router** 문서도 참조하세요.

### 알아두면 좋습니다.

**App Router**가 **Pages Router**보다 우선합니다.

디렉터리 간 경로는 동일한 URL 경로로 확인되어서는 안 되며 충돌을 방지하기 위해 빌드 시간 오류가 발생합니다.

![Untitled 2](https://github.com/codingjwp/mindpalace/assets/113403155/2699a36f-1ef6-4135-a419-9d50ef03aa6d)


기본적으로 앱 내부의 컴포넌트는 React 서버 컴포넌트입니다.

이는 성능 최적화를 위한 것으로 쉽게 채택할 수 있으며, 클라이언트 컴포넌트를 사용할 수도 있습니다.

권장 사항: 서버 컴포넌트를 처음 사용하는 경우 서버 페이지를 확인하세요.

## 폴더 및 파일의 역할

Next.js는 파일 시스템 기반 라우터를 사용합니다.

- 폴더는 경로를 정의하는 데 사용됩니다.
경로는 중첩된 폴더의 단일 경로로, 파일 시스템 계층 구조를 따라 **root folder**에서 `page.js` 파일이 포함된 최종 **leaf folder**까지 내려갑니다.

경로 정의하기를 참조하세요.

파일은 경로 세그먼트에 표시되는 UI를 만드는 데 사용됩니다. [특수 파일을 참조하세요.](Routing%20%E1%84%80%E1%85%B5%E1%84%87%E1%85%A9%E1%86%AB%E1%84%89%E1%85%A1%E1%84%92%E1%85%A1%E1%86%BC%2048ed238fd2ce425d93345f727284fa10.md)

## 경로 세그먼트

경로의 각 폴더는 경로 세그먼트를 나타냅니다. 각 경로 세그먼트는 URL 경로의 해당 세그먼트에 매핑됩니다.

![Untitled 3](https://github.com/codingjwp/mindpalace/assets/113403155/774e53a2-8805-400f-b2a0-1c8890860bb2)

## 중첩된 경로

중첩된 경로를 만들려면 폴더를 서로 중첩하면 됩니다. 

예를 들어, `app` 디렉터리에 두 개의 새 폴더를 중첩하여 새 `/dashboard/settings` 경로를 추가할 수 있습니다.

- `/` (Root segment)
- `dashboard` (Segment)
- `settings` (Leaf segment)

## 파일 규칙

Next.js는 중첩된 경로에서 특정 동작을 하는 UI를 생성하기 위한 특수 파일 세트를 제공합니다.

|||
| --- | --- |
| layout | 세그먼트와 그 하위 세그먼트에 대한 공유 UI |
| page | 경로의 고유 UI 및 공개적으로 액세스 가능한 경로 만들기 |
| loading | 세그먼트 및 하위 세그먼트에 대한 로드 중인 UI |
| not-found | 세그먼트 및 하위 세그먼트에 대한 찾을 수 없음 UI |
| error | 세그먼트 및 그 자식에 대한 오류 UI |
| global-error | 글로벌 오류 UI |
| route | 서버 측 API 엔드포인트 |
| template | 특수하게 다시 렌더링된 레이아웃 UI |
| default | 병렬 경로용 Fallback UI |

알아두면 유용한 정보 : 특수 파일에는 `.js`, `.jsx`, `.tsx` 파일 확장자를 사용할 수 있습니다.

## 컴포넌트 계층 구조

경로 세그먼트의 특수 파일에 정의된 React 컴포넌트는 특정 계층 구조로 렌더링됩니다.

- `layout.js`
- `template.js`
- `error.js` (리액트 Error boundary)
- `loading.js` (리액트 Suspense boundary)
- `not-found.js` (리액트 Error boundary)
- `page.js` 또는 중첩된 `layout.js`

![Untitled 4](https://github.com/codingjwp/mindpalace/assets/113403155/402f5abc-14da-4754-b77b-9b7cc9f088f7)

중첩된 경로에서 세그먼트의 구성 요소는 상위 세그먼트의 구성 요소 안에 중첩됩니다.

![Untitled 5](https://github.com/codingjwp/mindpalace/assets/113403155/1f46147d-33d3-49fe-bf47-0d5b257060fd)

## 코로케이션

특수 파일 외에도 컴포넌트, 스타일, 테스트 등 고유한 파일을 `app` 디렉터리의 폴더 안에 배치할 수 있습니다.

폴더가 경로를 정의하는 반면, `page.js` 또는 `route.js`가 반환하는 콘텐츠만 공개적으로 주소를 지정할 수 있기 때문입니다.

![Untitled 6](https://github.com/codingjwp/mindpalace/assets/113403155/6076c02b-a476-420b-97b7-4b5e033c4457)

## 고급 라우팅 패턴

앱 라우터는 고급 라우팅 패턴을 구현하는 데 도움이 되는 일련의 규칙을 제공합니다.

여기에는 다음이 포함됩니다:

**Parallel Routes** : 독립적으로 탐색할 수 있는 두 개 이상의 페이지를 동일한 보기에 동시에 표시할 수 있습니다.
자체 하위 탐색이 있는 분할 보기에 사용할 수 있습니다. 

**Intercepting Routes** : 경로를 가로채서 다른 경로의 컨텍스트에 표시할 수 있습니다.
현재 페이지의 컨텍스트를 유지하는 것이 중요한 경우에 사용할 수 있습니다.
예를 들어, 하나의 작업을 편집하는 동안 모든 작업을 보거나 피드에서 사진을 확장하는 경우입니다.

이러한 패턴을 사용하면 더 풍부하고 복잡한 UI를 구축할 수 있으므로 소규모 팀이나 개별 개발자가 구현하기에는 복잡했던 기능을 대중화할 수 있습니다.