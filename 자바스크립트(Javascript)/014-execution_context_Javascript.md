# Execution Context 실행 컨텍스트

Execution Context는 실행할려는 JS 코드와 코드를 실행할때 필요한 정보를 담고 있는 특수한 환경입니다.  
코드 실행에 필요한 모든 데이터를 들고 있는 환경으로 보시면 됩니다.  
크게 두개로 나뉘어 집니다.  
- Global Context : 최상이 Execution Context로 코드를 실행하면 무조건 생성되는 context로 웹에서 window 객체나 nodejs에서의 global 객체를 생서하고 들고 있습니다.
- Function Context : 함수가 실행될 떄 마다 함수별로 실행되는 context입니다. 함수 실행에 대한 모든 전보가 있습니다.

## Execution Context Stack

javascript는 싱글 스레드입니다.  
하나의 Memory Heap와 하나의 Call Stack을 가지고 있습니다.  

- Creation Phase

  Global Object를 생성합니다.  
  window 또는 global 객체가 생성되고 함수에서는 agruments 객체가 생성됩니다.  
  this는 window 또는 globla에 바인딩 됩니다.  
  변수와 함수는 Memory Heap에 배정하고 기본 값을 undefined로 저장합니다.(호이스팅을 말합니다.)  

- Execution Phae

    코드를 실행한다.
 필요하다면 새로운 Execution Context를 생성한다.
