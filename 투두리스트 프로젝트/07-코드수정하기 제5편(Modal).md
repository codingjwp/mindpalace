# 에러처리를 위한 Modal 컴포넌트 생성
## 문제점 부분
- 에러부분을 띄어주는데 alert를 사용
  - 브라우저마다 띄어주는 alert가 다르고 스타일도 바꿀 수 없음.

## Modal 작성하기 위한 코드
- 받아오는 props 종류

|props| Modal에서 받는 props 내용|
|:---:|---|
|$isopen|Modal 오픈 여부|
|$type|`sucess`와 `error`로 구분|
|modalMessage| 에러메세지 |
- 종료 버튼 및 모달 외부 클릭 시 종료
- 뒤에 버튼 클릭이 안되게 전체적으로 막고 중앙에 표시되도록 생성
- 개별적으로 Modal을 넣는 것 보다는 하나만 하는게 나을 것 같아 Context Api를 사용하여 상태관리를 실행
- [6편 ModalConttent 작성](./08-코드수정하기%20제6편(ModalContent).md)

```typescript
// ... import 부분 생략
interface ModalProps {
  $isopen: boolean;
  $type: "sucess" | "error";
  modalMessage: string;
}

const Modal = ({$isopen, $type, modalMessage}: ModalProps) => {
  const modalRef = useRef<HTMLDivElement>(null);
  const { setModalData } = useModalState();
  
  const modalClose = (e: MouseEvent) => {
    if (e.target !== modalRef.current) 
      setModalData({
        modalOpen: false,
        modalType: "sucess",
        modalMsg: "",
      });
  }

  return (
    <ModalBase $isopen={$isopen} onClick={modalClose}>
      <ModalContentBase $type={$type} ref={modalRef}>
        {modalMessage}
        <ModalClose iconName='btn-close' fill='#ffffff' onClick={modalClose} />
      </ModalContentBase>
    </ModalBase>
  )
}
// ... 스타일 부분 생략
```
