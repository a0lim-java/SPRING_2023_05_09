## 비즈니스 요구사항 정리
* 데이터: 회원 ID, 이름
* 기능: 회원 등록, 조회
* 시나리오: 아직 데이터 저장소가 선정되지 않음
* 일반적인 웹 애플리케이션 계층 구조  
  ![image](https://user-images.githubusercontent.com/104348646/197909976-21478bdc-1e69-424f-857c-5035f423246b.png)  
  - 컨트롤러: 웹 MVC의 컨트롤러 역할
  - 서비스: 핵심 비즈니스 로직 구현
  - 리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
  - 도메인: 비즈니스 도메인 객체
    + ex) 회원, 주문, 쿠폰 등등 <- 주로 DB에 저장하고 관리됨  
  
  ![image](https://user-images.githubusercontent.com/104348646/197910181-10d2a987-e23d-4ef0-8369-c056cef6c2d5.png)  
  - 아직 DB가 선정되지 않음 -> 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계
  - DB는 RDB, NoSQL 등의 다양한 저장소를 고민중인 상황으로 가정
  - 개발을 진행하기 위해 초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 DB 사용
  - cf) 서비스: 비즈니스적 성격 / 리포지토리: 개발적 성격(in, out 등)

## 회원 도메인과 리포지토리 만들기
* 회원 객체
  - java/hello.hellospring/domain/Member 클래스 생성
    ```
    package hello.hellospring.domain;

    public class Member {

        private Long id;
        private String name;

        public Long getId(){
            return id;
        }

        public void setId(Long id){
            this.id = id;
        }

        public String getName(){
            return name;
        }

        public void setName(String name){
            this.name = name;
        }
    }
    ```
* 회원 리포지토리 인터페이스
  - java/hello.hellospring/repositoty/MemberRepository 인터페이스 생성
    ```
    package hello.hellospring.repository;

    import hello.hellospring.domain.Member;

    import java.util.List;
    import java.util.Optional;

    public interface MemberRepository {
        Member save(Member member); // 회원 저장
        Optional<Member> findById(Long id); // Id로 회원 검색 / Optional: null을 감싸는 방식
        Optional<Member> findByName(String name); // 이름으로 회원 검색
        List<Member> findAll(); // 모든 회원의 list를 반환
    }
    ```
* java/hello.hellospring/repository/MemoryMemberRepository 클래스 생성
    ```
    package hello.hellospring.repository;

    import hello.hellospring.domain.Member;

    import java.util.*;

    public class MemoryMemberRepository implements MemberRepository{

        private static Map<Long, Member> store = new HashMap<>(); // Map에 데이터 저장 / HashMap: 실무에서는 동시성 문제를 고려해서 ConcurrentHashMap을 사용
        private static long sequence = 0L; // 숫자 key 값을 생성 / long: 실무에서는 동시성 문제를 고려해서 AtomicLong을 사용

        @Override
        public Member save(Member member) {
            member.setId(++sequence); // member에 Id 값을 세팅 / 이름은 고객이 입력, Id는 프로그램에서 생성해야 함
            store.put(member.getId(), member); // store에 저장 -> Map에 저장됨
            return member;
        }

        @Override
        public Optional<Member> findById(Long id) {
            return Optional.ofNullable(store.get(id)); // Optional.ofNullable: Null이 반환되는 경우에 값을 감싸는 역할
        }

        @Override
        public Optional<Member> findByName(String name) {
            return store.values().stream()
                    .filter(member -> member.getName().equals(name))
                    .findAny(); // loof: member.getName()이 name과 같은지 필터 -> 같은 값만 반환
            // 모두 같지 않다면 -> null -> Optional로 감쌈
        }

        @Override
        public List<Member> findAll() {
            return new ArrayList<>(store.values()); // 멤버 반환
        }
    }
    ```

## 회원 리포지토리 테스트 케이스 작성  
개발한 기능을 실행해서 테스트 할 때 자바의 main 메서드를 통해서 실행하거나, 웹 애플리케이션의 컨트롤러를 통해서 해당 기능을 실행한다.  
이러한 방법은 준비/실행에 오래 걸리고, 반복 실행하기 어렵고, 여러 테스트를 한번에 실행하기 어렵다는 단점이 있다.  
JUnit이라는 프레임워크로 테스트를 실행해서 이러한 문제를 해결한다.  
  
* 회원 리포지토리 메모리 구현체 테스트
  - test/java/hello.hellospring/repository 패키지 생성
  - MemoryMemberRepositoryTest 클래스 생성
    ```
    package hello.hellospring.repository;

    import hello.hellospring.domain.Member;
    import org.junit.jupiter.api.AfterEach;
    import org.junit.jupiter.api.Assertions;
    import org.junit.jupiter.api.Test;

    import java.util.List;
    import java.util.Optional;

    import static org.assertj.core.api.Assertions.*;

    class MemoryMemberRepositoryTest {

        MemoryMemberRepository repository = new MemoryMemberRepository();

        @AfterEach
        public void afterEach(){
            repository.clearStore(); // 각 Test가 끝나면 저장소의 데이터를 삭제함 <- Test간의 데이터 충돌 방지
        }

        @Test
        public void save(){
            //given
            Member member = new Member();
            member.setName("spring");

            repository.save(member);

            Member result = repository.findById(member.getId()).get(); // get: Optional에서 꺼냄
            assertThat(member).isEqualTo(result); // Member == result인지 검증
            // 다른 검증 방법: Assertions.assertEquals(member, result);
            // 다른 검증 방법: System.out.println("result = " + (result == member));
        }

        @Test
        public void findByName(){
            // 멤버 인스턴스 생성
            Member member1 = new Member();
            member1.setName("spring1");
            repository.save(member1);

            Member member2 = new Member();
            member2.setName("spring2");
            repository.save(member2);

            Member result = repository.findByName("spring1").get();

            assertThat(result).isEqualTo(member1);
        }

        @Test
        public void findAll(){
            // 멤버 인스턴스 생성
            Member member1 = new Member();
            member1.setName("spring1");
            repository.save(member1);

            Member member2 = new Member();
            member2.setName("spring2");
            repository.save(member2);

            List<Member> result = repository.findAll();

            assertThat(result.size()).isEqualTo(2); // findAll()이 2개를 출력하는지 확인
        }
    }
    ```
    + MemoryMemberRepositoryTest 실행 -> 안에 있는 두 개의 test 동시 실행
    + test/java/hello.hellospring 실행 -> 전체 class 실행
  - main/java/hello.hellospring/repository/MemoryMemberRepository 클래스 업데이트
    ```
    public void clearStore(){ // 각 Test가 완료되면 clear함 <- Test간의 데이터가 중복될 수 있음
        store.clear();
    }
    ```
    + test는 순서와 상관 없이(의존도 없이) 설계됨 <- 하나의 test가 끝날 때마다 공용 데이터를 저장소에서 삭제 필요
    
## 회원 서비스 개발
* main/java/hello.hellospring/service 패키지 생성
* main/java/hello.hellospring/service/MemberService 클래스 생성
    ```
    package hello.hellospring.service;

    import hello.hellospring.domain.Member;
    import hello.hellospring.repository.MemberRepository;
    import hello.hellospring.repository.MemoryMemberRepository;

    import java.util.List;
    import java.util.Optional;

    public class MemberService {

        private final MemberRepository memberRepository = new MemoryMemberRepository(); // 저장할 repository 생성

        /**
         * 회원가입
         */
        public Long join(Member member){
            // 중복 회원 검증: 같은 이름 X
            validateDuplicateMember(member);
            memberRepository.save(member); // 호출
            return member.getId(); // Id만 반환
        }

        private void validateDuplicateMember(Member member) {
            memberRepository.findByName(member.getName()) // 이름으로 회원 찾음(Optional 형태임)
                    .ifPresent(m -> {
                        throw new IllegalStateException("이미 존재하는 회원입니다.");
                    }); // 중복 회원이 있는 경우 처리
        }

        /**
         * 전체 회원 조회
         */
        public List<Member> findMembers(){
            return memberRepository.findAll(); // list<Member>형으로 반환
        }

        public Optional<Member> findOne(Long memberId){
            return memberRepository.findById(memberId);
        }
    }
    ```

## 회원 서비스 테스트
* ctrl + shift + T: class에서 바로 Test 생성  
  ![image](https://user-images.githubusercontent.com/104348646/197942401-5d488b1a-06ac-4f94-a5a1-92a1a4cd57ae.png)  
* test/java/hello.hellospring/repository/MemoryMemberRepositoryTest
  ```
  package hello.hellospring.service;

  import hello.hellospring.domain.Member;
  import hello.hellospring.repository.MemoryMemberRepository;
  import org.junit.jupiter.api.AfterEach;
  import org.junit.jupiter.api.Assertions;
  import org.junit.jupiter.api.Test;

  import static org.assertj.core.api.Assertions.*;
  import static org.junit.jupiter.api.Assertions.assertThrows;

  class MemberServiceTest {

      MemberService memberService = new MemberService();
      // 저장소 clear 서비스 생성
      MemoryMemberRepository memberRepository = new MemoryMemberRepository(); // MemoryMemberRepository(): 새로 생성한 객체(이전과 다른 인스턴스)

      @AfterEach
      public void afterEach(){
          memberRepository.clearStore();
      }

      @Test
      void 회원가입() {
          // given: 상황, 데이터
          Member member = new Member();
          member.setName("hello");

          // when: 검증 조건
          Long saveId = memberService.join(member);

          // then: 검증
          Member findMember = memberService.findOne(saveId).get();
          assertThat(member.getName()).isEqualTo(findMember.getName());
      }

      @Test
      public void 중복_회원_예외(){
          // given: 중복된 데이터
          Member member1 = new Member();
          member1.setName("spring");

          Member member2 = new Member();
          member2.setName("spring");

          // when
          memberService.join(member1);
          /// assertThrows로 오류 처리
          IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2));

          assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다."); // 메세지 검증

          /// try-catch로 오류 처리
          /*
          try {
              memberService.join(member2);
              fail("예외가 발생해야 합니다."); // 실행 안됨
          } catch (IllegalStateException e){ // 예외가 작동됨
              assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다."); // 메세지 검증
          }
           */


          // then
      }

      @Test
      void findMembers() {
      }

      @Test
      void findOne() {
      }
  }
  ```
* MemoryMemberRepository memberRepository = new MemoryMemberRepository(); // MemoryMemberRepository(): MemberService.java의 객체와 이름이 동일함
  - MemberService.java에서 repository 이름을 받도록 설정
  - MemberService.java
    ```
    public class MemberService {

        private final MemberRepository memberRepository;

        public MemberService(MemberRepository memberRepository){
            this.memberRepository = memberRepository;
        }
        ...
    ```
  - MemberServiceTest.java
    ```
    class MemberServiceTest {

        MemberService memberService;
        MemoryMemberRepository memberRepository;

        // MemoryMemberRepository 이후 memberService에 memberRepository로 생성해 넣음
        @BeforeEach
        public void beforeEach(){
            memberRepository = new MemoryMemberRepository();
            memberService = new MemberService(memberRepository);
        }
        ... @AfterEach
    ```
