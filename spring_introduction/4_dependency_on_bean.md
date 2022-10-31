## 용어 및 개념 정리
* DAO: DB data에 접근하기 위한 객체
* DTO: 계층 간 데이터 교환
  - 데이터 전송: 유저가 DTO에 데이터를 넣어서 전송함 -> 받은 서버가 DB로 데이터를 넣음
  - 로직 없음
* VO(Value Object): read-only
* IoC(Inversion of Control)  
  : 제어의 역전
  - 메소드/객체의 호출작업을 외부에서 결정함(개발자가 결정 X)

![image](https://user-images.githubusercontent.com/104348646/198982527-2b9403fc-956a-45ee-9fd2-f09c3754049a.png)  

* 객체를 생성하는 두 가지 방법  
![image](https://user-images.githubusercontent.com/104348646/198982654-b7f5c8ef-0b2e-4863-baad-038d27041150.png)  


## 스프링 빈을 등록하고, 의존관계 설정하기
회원 컨트롤러가 회원서비스와 회원 리포지토리를 사용할 수 있게 의존관계를 준비하자
* 회원 컨트롤러에 의존관계 추가
  - main/java/hello.hellospring/controller에 MemberController 클래스 생성
    ```
    package hello.hellospring.controller;

    import hello.hellospring.service.MemberService;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Controller;

    @Controller // 스프링 컨테이너에서 스프링 빈이 관리됨: 스프링이 뜰 때, 스프링 컨테이너가 MemberController 객체를 생성해서 들고있음
    public class MemberController {

        private final MemberService memberService; // new 생성 X: 한 개만 생성하고 공용으로 쓸 용도임

        @Autowired // 스프링 컨테이너에 있는 MemberController를 가져와서 연결함
        public MemberController(MemberService memberService){
            this.memberService = memberService;
        }
    }
    ```
  - 의존성 주입(DI: Dependency Injection)  
    : 생성자에 @Autowired가 있으면, 스픅링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어준다.
  - 이전 테스트에서는 개발자가 직접 주입했고, 여기서는 @Autowired에 의해 스프링이 의존성을 주입함

* 오류 발생
  ```
  Consider defining a bean of type 'hello.hellospring.service.MemberService' in your configuration
  ```
  -> memberService가 스프링 빈으로 등록되어 있지 않음  
    - memberService는 순수 자바 코드임 -> 스프링에서 인식하지 못함
    ![image](https://user-images.githubusercontent.com/104348646/198172915-caf462ec-8a27-48ad-920e-0ae03c468579.png)  
      - cf) helloController는 스프링이 제공하는 컨트롤러 -> 스프링 빈으로 자동 등록됨
  
* 스프링 빈을 등록하는 2가지 방법
  - 컴포넌트 스캔과 자동 의존관계 설정
  - 자바 코드로 직접 스프링 빈 등록하기

### 컴포넌트 스캔과 자동 의존관계 설정
* @Component 애노테이션이 있으면, 스프링 빈으로 자동 등록 됨
* @Controller 컨트롤러가 스트링 빈으로 자동 등록된 이유도 컴포넌트 스캔 때문이다

* @Component를 포함하는 다음 애노테이션도 스프링 빈으로 자동 등록된다
  - @Controller
  - @Service
  - @Repository

* 회원 서비스 스프링 빈 등록
    - MemberService에서 @controller를 입력해 자동 등록함
        ```
        @Service
        public class MemberService {

            private final MemberRepository memberRepository;
            
            @Autowired
            public MemberService(MemberRepository memberRepository){
                this.memberRepository = memberRepository; }
                ...
        ```

  - cf) 생성자에 @Autowired를 사용하면, 객체 생성 시점에 스프링 컨테이너에서 해당 스프링빈을 찾아서 주입한다
  - 생성자가 1개만 있으면 @Autowired는 생략 가능

* 회원 리포지토리 스프링 빈 등록
  -   MemoryMemberRepository에서 @Repository를 입력해 자동 등록함
      ```
      @Repository
      public class MemoryMemberRepository implements MemberRepository{
      ```
      
* 스프링 빈 등록 이미지  
  ![image](https://user-images.githubusercontent.com/104348646/198269872-8d4e19a1-5f80-41c6-b2f4-a5e0d0ee2667.png)  
  - memberService와 memberRepository가 스프링 컨테이너에 스프링 빈으로 등록됨
  - cf) 스프링은 스프링 컨테이너에 스프링 빈을 등록할 때, 기본으로 싱글톤으로 등록한다(유일하게 하나만 등록해서 공유) -> 같은 스프링 빈이면, 모두 같은 인스턴스임
  - 싱글톤이 아니게 설정 가능하지만, 특별 케이스를 제외하면 대부분 싱글톤을 사용

### 자바 코드로 직접 스프링 빈 등록하기
* @Service, @Repository 애노테이션을 제거하고 진행
  ```
  package hello.hellospring.service;

  import hello.hellospring.repository.MemberRepository;
  import hello.hellospring.repository.MemoryMemberRepository;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;

  @Configuration
  public class SpringConfig {

      @Bean // MemberService를 스프링 빈에 등록(생성자를 통해서 넣어야 함)
      public MemberService memberService(){
          return new MemberService(memberRepository());
      }

      @Bean // MemberService와 연결 -> MemberRepository를 스프링 빈에 등록
      public MemberRepository memberRepository(){
          return new MemoryMemberRepository();
      }
  }
  // controller는 스프링이 관리함 -> @Controller 그대로 사용
  ```  
  - 향후 메모리 리포지토리를 다른 리포지토리로 변경할 예정
  - 컴포넌트 스캔 방식 대신, 자바 코드로 스프링 빈을 설정함
 
* XML로 설정하는 방식도 존재 -> 최근 사용 빈도 낮음
* DI는 필드 주입, setter 주입, 생성자 주입으로 3가지 방법이 존재 -> 생성자 주입을 권장
* 실무에서는 주로 정형화된 컨트롤러, 서비스, 리포지토리 등의 코드는 컴포넌트 스캔을 사용
* 실무에서 비정형화/구현 클래스 변경이 필요한 경우, 설정을 통해 스프링 빈으로 등록 -> 코드 수정이 쉬움
* @Autowired를 통한 DI: helloController, memberService 등과 같이 스프링이 관리하는 객체에서만 동작
  - 스프링 빈 대신 직접 생성한 객체에서는 동작하지 않음

