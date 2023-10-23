# SVG 스프라이트 만들기

이번에 코드를 수정하면서 **`react-icons`** 라는 라이브러리를 없애고 **icon** 을 추가하고 싶어서 5가지 정도의 **SVG**를 넣어볼려고 하였다.
그러다가 생각해보니 내가 넣을 곳에 **TodoList** 로 글자가 추가 되면 개별적으로 **SVG**을 불러오는게 아닐까?? 라는 생각에 조금이라도 최적화를 해보자는 생각에 방법에 대해 찾아 보았다.

## 무슨 방법이 좋을까??

### 5개 SVG 사용

- 당연히 제외된 방식 최적화를 하기위해서인데 그대로 사용한다??? 정 방법이 없다면 맞겠지만 아직 여러 방향으로 생각을 해보지 않았기 떄문에 제외.

### 이미지 스프라이트

- 보통 이미지를 많이 쓸떄 스프라이트로 만들어서 이미지 위치로 해당 이미지를 가져온다.  

  |장점|단점|
  |---|---|
  |HTTP 요청의 수 감소|스프라이트 관리 과정 번거러움|
  |네트워크 트래픽, 메모리 사용량 절약|배경이미지의 위치가 정확하게 지정|
  |마우스 오버, 클릭시 이미지 깜빡임 방지|이미지 변경시 스프라이트 파일 다시 생성|

### SVG 스프라이트

- 이미지 스프라이트와 비슷하게 **SVG** 파일을 여러개 합쳐서 사용
- 하지만 이미지랑 다르게 **크기**, **높이**, **색상**, **애니메이션**이 가능

## 선택한 SVG 스프라이트

- 이전에 사용한 버튼중에 애니메이션이 있고 크기 변화에도 품질이 꺠지지 않고 색상도 변화 가능한 SVG 스프라이트를 사용하기로 결정
- 찾아보니 사용할 **SVG** 파일들을 **Spritebot**이라는 프로그램에 넣으면 스프라이트를 만들어 준다는데 5개 정도를 가지고 사용할 정도는 아닌 것 같아 다른 방법을 찾아 보았다.
- 다른 방법을 찾아 보니 `SVG`에 `symbol` 태그를 넣고 거기에 `id`를 지정해서 만드는 형식

### SVG에 symbol 넣기

- **symbol**에 **id**를 지정해주고 앞에 svg 내용을 넣어준다.
- `btn-close`, `checkbox`, `uncheckbox` 추가

```xml
<svg>
  <symbol id="btn-send-cancel">...</symbol>
  <symbol id="btn-send">...</symbol>
  <symbol id="btn-delete">...</symbol>
  <symbol id="btn-edit">...</symbol>
  <symbol id="btn-uparrow">...</symbol>
  <symbol id="btn-close">...</symbol>
  <symbol id="checkbox">...</symbol>
  <symbol id="uncheckbox">...</symbol>
</svg>
```

### SVG use 사용하기

- **symbol**의 **id**를 사용하기 위해서는 **use**를 사용해야 한다.
- **href** 속성에 svg스프라이트가 있는 경로 **`${svg}`**, **`#${id}`** 를 합쳐서 string으로 넣어줘야 한다.
- rotate 때문에 $direction 추가

```typescript
export interface SvgIconProps {
  iconName: "btn-edit" | "btn-delete" | "btn-send" | "btn-send-cancel"
    | "btn-uparrow" | "btn-close" | "checkbox" | "uncheckbox";
  $direction: "up" | "down" | "left" | "right";
}

export const SvgIcon = ({iconName}: SvgIconProps) => {
  return (
    <svg aria-label={iconName} width={24} height={24} {...props}
      xmlns="http://www.w3.org/2000/svg">
      <use href={`${svg}#${iconName}`} />
    </svg>
  )
}
```

- 하지만 이럴 경우 크기 지정 및 색상 변경을 해 줄 수가 없어 더 추가를 해줘야 했다.
- **width**와 **height**, ((viewBox)) 추가
- **SVGProps**로 svg에 필요한 속성 interface를 가져오고 **...prop**s로 관련 속성을 자동으로 넣어 줄수 있도록 수정하였다.

```typescript
export interface SvgIconProps extends SVGProps<SVGSVGElement> { //...생략 }

export const SvgIcon: FC<SvgIconProps> = ({iconName, $direction, ...props}) => {
  const rotate = {
    "up": `rotate(180 0 2)`,
    "down": `rotate(0 0 0)`,
    "left": `rotate(270 0 2)`,
    "right": `rotate(90 0 2)`,
  }
  
  return (
    <svg width={props.width ?? 24} height={props.height ?? 24}
      transform={rotate[$direction]} viewBox="0 0 24 24" {...props}
      xmlns="http://www.w3.org/2000/svg">
      <use href={`${svg}#${iconName}`} />
    </svg>
  )
}
```

## SVG 사용 이미지

### 기본적인 사용

![스크린샷 2023-07-25 225027](https://github.com/codingjwp/mindpalace/assets/113403155/6d1a8605-f026-40b0-ba83-1604acc94956)
![스크린샷 2023-07-25 225042](https://github.com/codingjwp/mindpalace/assets/113403155/2155080c-6343-49be-aebc-af3d3c4c6740)

### 색상 및 크기 변환

- 이전 이미지 코드가 변경 되어 이미지 삭제
- 이전 코드는 scale을 썼으나 현재는 `width`와 `height`로 props에 **iconWidth, iconHeight** 추가

```typescript
return (
  <IconButton 
    aria-label="create-todo-btn"
    type="button"
    iconName="btn-uparrow"
    iconWidth="48"
    iconHeight="48"
    iconFill="#ffffff"
    $size="circle"
    $btnType={btnType}
    $direction={isOpen}
    $isIconOfText={"no"}
    onClick={createTodoOpen}/>
);
```
