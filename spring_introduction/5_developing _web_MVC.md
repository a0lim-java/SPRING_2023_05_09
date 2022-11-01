# 회원 관리 예제
## 회원 웹 기능 - 홈화면 추가
* 홈 컨트롤러 추가
  - main/java/hello.hellospring/HomeController 생성
  ```
  package hello.hellospring.controller;

  import org.springframework.stereotype.Controller;
  import org.springframework.web.bind.annotation.GetMapping;

  @Controller
  public class HomeController {

      @GetMapping("/") // /: 도메인의 첫 페이지(localhost:8080)에서 "home"을 호출
      public String home(){
          return "home";
      }
  }
  ```
* 회원 관리용 홈
  ```
  <!DOCTYPE HTML>
  <html xmlns:th="http://www.thymeleaf.org">
  <body>
  <div class="container">
      <div>
          <h1>Hello Spring</h1>
          <p>회원 기능</p>
          <p>
              <a href="/members/new">회원 가입</a> <!--회원 가입 화면으로 이동-->
              <a href="/members">회원 목록</a> <!--회원 목록 화면으로 이동-->
          </p>
      </div>
  </div> <!-- /container -->

  </body>
  </html>
  ```  
  ![image](https://user-images.githubusercontent.com/104348646/199139450-83a3c679-2721-4445-9c7e-3b6fc7724ef4.png)  
  - cf) 우선순위: 컨트롤러 > 정적 파일
    + HomeController - resources/templates/home.html > resources/static/index.html

## 회원 웹 기능 - 등록
### 회원 등록 폼 개발
* 회원 등록 폼 컨트롤러
  - main/java/hello.hellospring/MemberController에서 @GetMapping 코드 추가
  ```
  @Controller // 스프링 컨테이너에서 스프링 빈이 관리됨: 스프링이 뜰 때, 스프링 컨테이너가 MemberController 객체를 생성해서 들고있음
  public class MemberController {

      private final MemberService memberService; // new 생성 X: 한 개만 생성하고 공용으로 쓸 용도임

      @Autowired // 스프링 컨테이너에서 MemberController를 가져와서 연결함
      public MemberController(MemberService memberService){
          this.memberService = memberService;
      }
      @GetMapping("/members/new") // createMemberForm으로 이동(연결) -> View Resolver -> thymeleaf가 생성
      public String createForm(){
          return "members/createMemberForm";
      }
  ```
* 회원 등록 폼 HTML
  - main/resources/templates/members 디렉토리(경로) 생성
  - 위의 디렉토리 밑에 createMemberForm.html 생성
  ```
  <!DOCTYPE HTML>
  <html xmlns:th = "http://www.thymeleaf.org">
  <body>

  <div class="container">

      <form action="/members/new" method="post"> <!-- form: 값 입력하는 창 / post: post 방식으로 불러옴 -> MemberController에서 @PostMapping으로 연결 -->
          <div class="form-group">
              <label for="name">이름</label>
              <input type="text" id="name" name="name" placeholder="이름을 입력하세요"> <!-- type: text를 적을 칸이 생성됨 -->
          </div>
          <button type="submit">등록</button>
      </form>
  </div> <!-- /container -->

  </body>
  </html>
  ```
## 화면 등록 컨트롤러
* 웹 등록 화면에서 데이터를 전달 받을 폼 객체
  - main/java/hello.hellospring/controller/MemberForm 클래스 생성
  ```
  package hello.hellospring.controller;

  public class MemberForm {
      private String name;

      public String getName() {
          return name;
      }

      public void setName(String name) {
          this.name = name;
      }
  }
  ```
* 회원 컨트롤러에서 회원을 실제 등록하는 기능
  - main/java/hello.hellospring/controller/MemberController 클래스에 @PostMapping 코드 추가
  ```
    @PostMapping("/members/new") // createMemberForm에서 연결함
    public String create(MemberForm form){ // MemberForm을 통해 reateMemberForm.html에서의 name으로 연결
        Member member = new Member();
        member.setName(form.getName());

        memberService.join(member);

        return "redirect:/"; // 회원 가입이 끝나면, 홈 화면으로 돌아감
    }
  ```
  - PostMapping: 주로 조회용으로 사용
  - GetMapping: form에 직접 입력하고 값을 넘기는 경우에 주로 사용
  
## 회원 웹 기능 - 조회
* 회원 컨트롤러에서 조회 기능
  - main/java/hello.hellospring/controller/MemberController 클래스에 @GetMapping 코드 추가
  ```
    @GetMapping(value = "/members")
    public String list(Model model){
        List<Member> members = memberService.findMembers(); // 모든 회원 가져옴
        model.addAttribute("members", members); // members 안에 회원 리스트 자체를 모델에 담아서 View로 넘김
        return "members/memberList";
    }
  ```
* 회원 리스트 HTML
  - java/resources/templates/members/memberList.html 생성
  ```
  <!DOCTYPE HTML>
  <html xmlns:th="http://www.thymeleaf.org">
  <body>
  <div class="container">
    <div>
      <table>
        <thead>
        <tr>
          <th>#</th>
          <th>이름</th>
        </tr>
        </thead>
        <tbody>
        <tr th:each="member : ${members}">  <!-- members 객체 호출 / $: 모델 안의 값을 가져옴 / th: loop -->
          <td th:text="${member.id}"></td> <!-- @@ 1 -->
          <td th:text="${member.name}"></td> <!-- @@ spring1 -->
        </tr>
        </tbody>
      </table>
    </div>

  </div> <!-- /container -->

  </body>
  </html>
  ```  
  - spring1, spring2로 회원 등록한 결과  
    ![image](https://user-images.githubusercontent.com/104348646/199215896-43261960-56c4-4673-81a4-b6b2f26d5ea4.png)
  - 스프링을 재시작하는 경우, 회원 리스트 데이터가 모두 사라짐 -> DB 등에 데이터 저장이 필요
