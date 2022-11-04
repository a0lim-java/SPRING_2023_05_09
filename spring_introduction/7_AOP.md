## AOP가 필요한 상황
* 모든 메소드의 호출 시간을 측정하고 싶다면?
* 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern)
* 회원 가입 시간, 회원 조회 시간을 측정하고 싶다면?  
![image](https://user-images.githubusercontent.com/104348646/199873166-ca67af62-4867-4589-9855-2702eaed8782.png)  

* MemberService 회원 조회 시간 측정 추가
  ```
  ```
* 문제
  - 회원가입, 회원 조회에 시간을 측정하는 기능은 핵심 관심 사항이 아니다.
  - 시간을 측정하는 로직은 공통 관심 사항이다.
  - 시간을 측정하는 로직과 핵심 비즈니스의 로직이 섞여서 유지보수가 어렵다.
  - 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.
  - 시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다.

## AOP 적용
* AOP: Aspect Oriented Programming
* 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern) 분리  
  ![image](https://user-images.githubusercontent.com/104348646/199873387-67295576-ad13-4fbf-9e08-56201f8d6b07.png)  

* 시간 측정 AOP 등록
  ```
  ```
* 해결
  - 회원가입, 회원 조회등 핵심 관심사항과 시간을 측정하는 공통 관심 사항을 분리한다.
  - 시간을 측정하는 로직을 별도의 공통 로직으로 만들었다.
  - 핵심 관심 사항을 깔끔하게 유지할 수 있다.
  - 변경이 필요하면 이 로직만 변경하면 된다.
  - 원하는 적용 대상을 선택할 수 있다.

### 스프링의 AOP 동작 방식 설명
* AOP 적용 전 의존관계
![image](https://user-images.githubusercontent.com/104348646/199873530-d9290119-655a-49f9-9904-fc772de72f53.png)
* AOP 적용 후 의존관계  
![image](https://user-images.githubusercontent.com/104348646/199873602-9e7f2d2e-4a27-43c0-b35b-da23c690c395.png)  
* AOP 적용 전 전체 그림  
![image](https://user-images.githubusercontent.com/104348646/199873644-dc987663-3107-4079-96c4-4a2cf6837dea.png)  
* AOP 적용 후 전체 그림  
![image](https://user-images.githubusercontent.com/104348646/199873685-d97374ac-3f05-4bc7-ad1a-6b16e3d51c28.png)  
  - 실제 Proxy가 주입되는지 콘솔에 출력해서 확인하기
