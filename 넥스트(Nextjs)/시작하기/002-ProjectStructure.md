# Next.js 프로젝트 구조

이 페이지에서는 Next.js 프로젝트의 파일 및 폴더 구조에 대한 개요를 제공합니다.
`app`및 `pages`디렉터리 내의 최상위 파일 및 폴더, 구성 파일, 라우팅 규칙을 다룹니다.

## 탑 레벨 폴더들

| 폴더 이름 | 내용 |
| --- | --- |
| app | App Router |
| pages | Pages Router |
| public | 제공할 정적 에셋 |
| src | 선택적 애플리케이션 소스 폴더 |

## 탑 레벨 폴더들

| Next.js | 내용 |
| --- | --- |
| next.config.js | Next.js용 구성 파일 |
| package.json | 프로젝트 종속성 및 스크립트 |
| instrumentation.ts | 원격 분석 및 계측 파일 |
| middleware.ts | Next.js 요청 미들웨어 |
| .env | 환경 변수 |
| .env.local | 로컬 환경 변수 |
| .env.production | 프로덕션 환경 변수 |
| .env.development | 개발 환경 변수 |
| .eslintrc.json | ESLint용 구성 파일 |
| .gitignore | 무시할 Git 파일 및 폴더 |
| next-env.d.ts | Next.js용 TypeScript 선언 파일 |
| tsconfig.json | TypeScript용 구성 파일 |
| jsconfig.json | JavaScript용 구성 파일 |

## App 라우팅 규칙

### 라우팅 파일들
| | | |
| --- | --- | --- |
| layout | .js .jsx .tsx | Layout |
| page | .js .jsx .tsx | Page |
| loading | .js .jsx .tsx | Loading UI |
| not-found | .js .jsx .tsx | Not found UI |
| error | .js .jsx .tsx | Error UI |
| global-error | .js .jsx .tsx | Global error UI |
| route | .js .ts | API endpoint |
| template | .js .jsx .tsx | Re-rendered layout |
| default | .js .jsx .tsx | 병렬 경로 폴백 페이지 |

### 중첩 경로

|||
| --- | --- |
| folder | 경로 세그먼트 |
| folder/folder | 중첩 경로 세그먼트 |

### 동적 경로

|||
| --- | --- |
| [folder] | 동적 경로 세그먼트 |
| [...folder] | 포괄 경로 세그먼트 |
| [[...folder]] | 선택적 catch-all 경로 세그먼트 |

### 라우팅 그룹 및 비공개 폴더

|||
| --- | --- |
| (folder) | 라우팅에 영향을 주지 않고 경로 그룹화 |
| _folder | 폴더 및 모든 하위 세그먼트를 라우팅에서 제외하기 |

### 병렬 및 가로채기 경로

|||
| --- | --- |
| @folder | 명명된 슬롯 |
| (.)folder | 같은 레벨 가로채기 |
| (..)folder | 한 레벨 위 가로채기 |
| (..)(..)folder | 두 레벨 위 가로채기 |
| (...)folder | 루트에서 가로채기 |

## 메타데이터 파일 규칙

### App 아이콘들

||||
| --- | --- | --- |
| favicon | .ico | 파비콘 파일 |
| icon | .ico .jpg .jpeg .png .svg | 앱 아이콘 파일 |
| icon | .js .ts .tsx | 생성된 앱 아이콘 |
| apple-icon | .jpg .jpeg, .png | Apple 앱 아이콘 파일 |
| apple-icon | .js .ts .tsx | 생성된 Apple 앱 아이콘 |

### 오픈 Graph 및 트위터 이미지

|  |  |  |
| --- | --- | --- |
| opengraph-image | .jpg .jpeg .png .gif | 오픈 그래프 이미지 파일 |
| opengraph-image | .js .ts .tsx | 생성된 오픈 그래프 이미지 |
| twitter-image | .jpg .jpeg .png .gif | 트위터 이미지 파일 |
| twitter-image | .js .ts .tsx | 생성된 트위터 이미지 |

### SEO

||||
| --- | --- | --- |
| sitemap | .xml | 사이트맵 파일 |
| sitemap | .js .ts | 생성된 사이트맵 |
| robots | .txt | 로봇 파일 |
| robots | .js .ts | 생성된 로봇 파일 |

## `pages` 라우팅 규칙

### 특수 파일

|  |  |  |
| --- | --- | --- |
| _app | .js .jsx .tsx | 커스텀 앱 |
| _document | .js .jsx .tsx | 사용자 지정 문서 |
| _error | .js .jsx .tsx | 사용자 지정 오류 페이지 |
| 404 | .js .jsx .tsx | 404 오류 페이지 |
| 500 | .js .jsx .tsx | 500 오류 페이지 |

### **경로**

| 폴더 규칙 |  |  |
| --- | --- | --- |
| index | .js .jsx .tsx | 홈페이지 |
| folder/index | .js .jsx .tsx | 중첩된 페이지 |
| 파일 규칙 |  |  |
| index | .js .jsx .tsx | 홈페이지 |
| file | .js .jsx .tsx | 중첩된 페이지 |

### **동적 경로**

| 폴더 규칙 |  |  |
| --- | --- | --- |
| [folder]/index | .js .jsx .tsx | 동적 경로 세그먼트 |
| [...folder]/index | .js .jsx .tsx | Catch-all 경로 세그먼트 |
| [[...folder]]/index | .js .jsx .tsx | 선택적 catch-all 경로 세그먼트 |
| 파일 규칙 |  |  |
| [file] | .js .jsx .tsx | 동적 경로 세그먼트 |
| [...file] | .js .jsx .tsx | Catch-all 경로 세그먼트 |
| [[...file]] | .js .jsx .tsx | 선택적 catch-all 경로 세그먼트 |

⏪ Previous : [설치하기](./001-Installation.md)

⏩ Next : [App Router](./003-AppRouter.md)