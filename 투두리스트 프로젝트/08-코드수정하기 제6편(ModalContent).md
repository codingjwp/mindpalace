# Context API를 사용한 상태관리용
## 사용이유
-  **API** 입력시 에러내용을 처리할때 마다 API 사용하는 곳에 Modal을 생성하는 것이 별로 좋지 
못하다고 판단   
- **Context API** 사용하여 **APP**에서 처리

## ModalContext 코드 내용
 - **ModalDataProps** Modal에 사용할 props 내용
 - **ModalContextPRops** 상태관리를 위해 사용할 내용
 ### Interface 및 createContext 내용
```typescript
import { Dispatch, SetStateAction, createContext, useState, ReactNode, useContext } from 'react'

interface ModalDataProps {
  modalOpen: boolean;
  modalType: "sucess" | "error";
  modalMsg: string;
}

interface ModalContextProps {
  modalData: ModalDataProps;
  setModalData: Dispatch<SetStateAction<ModalDataProps>>;
}

const ModalContext = createContext<ModalContextProps | undefined>(undefined);
```
### 전달 할 데이터 지정
- 상태관리하여 넘길 정보를 **useSate**로 만들고 **Provider**의 **value**에 작성
```typescript
export const ModalProvider = ({children}: {children: ReactNode}) => {
  const [modalData, setModalData] = useState<ModalDataProps>({
    modalOpen: false,
    modalType: "sucess",
    modalMsg: "",
  })
  
  return (
    <ModalContext.Provider value={{modalData, setModalData}}>
      {children}
    </ModalContext.Provider>
  )
}
```
### useContext를 Hook으로 만들어 사용.
- **createContext**할때 `default` 값으로 `undefined` 넣어 그냥 useContext만 하면 써야 하는 곳에서 선언한 후 쓸려고 하면 **typescript**에서 `undefined`로 인해 데이터를 못 찾게 됨
- 그렇기 때문에 **modalState** 가 없으면 에러를 발생시키고 아니면 **Modal Props**과 **setState**를 반환
```typescript
export const useModalState = () => {
  const modalState = useContext(ModalContext);
  if (!modalState)
    throw new Error('Cannot find IssueProvider');
  const modalOpen = modalState.modalData.modalOpen;
  const modalType = modalState.modalData.modalType;
  const modalMsg = modalState.modalData.modalMsg;
  const setModalData = modalState.setModalData;
  
  return {modalOpen, modalType, modalMsg, setModalData};
}
```

