# 에러처리를 위한 Modal 컴포넌트 생성
## 문제점 부분
- 에러부분을 띄어주는데 alert를 사용
  - 브라우저마다 띄어주는 alert가 다르고 스타일도 바꿀 수 없음.

## Modal 작성하기 위한 코드
- 받아오는 props 종류

|props| Modal에서 받는 props 내용|
|:---:|---|
|$isopen|Modal 오픈 여부|
|$type|`detail`와 `error`로 구분|
|modalMessage| 에러메세지 |
- 종료 버튼 및 모달 외부 클릭 시 종료
- 뒤에 버튼 클릭이 안되게 전체적으로 막고 중앙에 표시되도록 생성
- 개별적으로 Modal을 넣는 것 보다는 하나만 하는게 나을 것 같아 Context Api를 사용하여 상태관리를 실행
- [6편 ModalConttent 작성](./09-코드수정하기%20제7편(ModalContent).md)

### Modal 코드 작성 
- **useModalState()** 사용함으로써 **Modal Props** 삭제
- **interface** 부분도 사용 안함으로써 삭제

```typescript
// ... import 부분 생략
const Modal = () => {
  const modalRef = useRef<HTMLDivElement>(null);
  const { modalOpen, modalType, modalMsg, setModalData } = useModalState();
  
  const modalClose = (e: MouseEvent) => {
    if (e.target !== modalRef.current) 
      setModalData({ modalOpen: false, modalType: "detail", modalMsg: "" });
  }

  return (
    <ModalBase $isopen={modalOpen} onClick={modalClose}>
      <ModalContentBase $type={modalType} ref={modalRef}>
        {modalMsg}
        <ModalClose iconName='btn-close' $direction='false' $type={modalType} onClick={modalClose} />
      </ModalContentBase>
    </ModalBase>
  )
}
// ... 스타일 부분 생략
```

## 모달 선언 방식 변경
### 이전 모달 방식
- 이전 코드는 **Context API**를 사용하여 **useModalState**로 데이터를 가져우 **Modal** 컴포넌트에 **props**로 전달 하는 방식을 하였는데 필요없는 방식이라는 걸 깨닫고 수정
```typescript
function App() {
  const routers = createBrowserRouter(routerElement);
  const {modalOpen, modalType, modalMsg} = useModalState();
  return (
    <>
      <GlobalStyles />
      <RouterProvider router={routers} />
      <Modal modalOpen={modalOpen} modalType={modaltype} modalMsg={modalMsg} />
    </>
  );
}
```
### 변경후 모달 컴포넌트 
- Modal 컴포넌트 자체에 **useModalState** 선언하여 데이터를 가져 오도록 수정
- 이전에 props로 전달할 필요없이 작동
```typescript
const Modal = () => {
  const modalRef = useRef<HTMLDivElement>(null);
  const { modalOpen, modalType, modalMsg, setModalData } = useModalState();
  
  const modalClose = (e: MouseEvent) => {
    if (e.target !== modalRef.current) 
      setModalData({ modalOpen: false, modalType: "detail", modalMsg: "" });
  }

  return (
    <ModalBase $isopen={modalOpen} onClick={modalClose}>
      <ModalContentBase $type={modalType} ref={modalRef}>
        {modalMsg}
        <ModalClose iconName='btn-close' $direction='down' $type={modalType} onClick={modalClose} />
      </ModalContentBase>
    </ModalBase>
  )
}
```