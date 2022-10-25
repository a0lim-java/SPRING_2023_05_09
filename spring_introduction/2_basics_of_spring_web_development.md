* 개발 방법
  - 1 정적 컨텐츠: 파일을 그대로 보냄
  - 2 MVC와 템플릿 엔진: JSP, PHP 등의 http를 랜더링해서 보냄
  - 3 API: JSON 포맷으로 데이터를 전달
    + Vue, React 등 사용, 서버 간에 사용

## 정적 컨텐츠
* 스프링 부트 정적 컨텐츠 기능



* resources/static/hello-static.html 파일 생성
  ```
  <!DOCTYPE HTML>
  <html>
  <head>
      <title>static content</title>
      <meta http-equiv = "Content-Type" content = "text/html; charset = UTF-8" />
  </head>
  <body>
  정적 컨텐츠 입니다.
  </body>
  </html>
  ```  
* 실행
  ![image](https://user-images.githubusercontent.com/104348646/197739045-3b2cd85f-c322-4950-a982-e88cfd7fdab2.png)
  ![image](https://user-images.githubusercontent.com/104348646/197739252-e4c0238a-861e-47e2-8aba-41fc142dad1a.png)
    - 파일 코드와 동일함

* 정적 컨텐츠 이미지  
![image](https://user-images.githubusercontent.com/104348646/197740728-8ba02762-8473-4db0-93ac-c84176e86024.png)  
  - 1) hello-static 컨트롤러 검색함 -> 없음 -> 2) resources를 검색 후 반환

## MVC와 템플릿 엔진
* MVC: Model, View, Controller
* Controller
  -  java/hello.hellospring/controller/HelloController
  ```
  package hello.hellospring.controller;

  import org.springframework.stereotype.Controller;
  import org.springframework.ui.Model;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.RequestParam;

  @Controller
  public class HelloController {

      @GetMapping("hello")
      public String hello(Model model){
          model.addAttribute("data", "hello!!");
          return "hello";
      }

      @GetMapping("hell-mvc")
      public String helloMvc(@RequestParam("name") String name, Model model){ // name: 파라미터 값 -> 변환 가능
          model.addAttribute("name", name);
          return "hello-template";
      }
  }
  ```
* View
  - resources/templates/hello-template.html 파일 생성
  ```
  <html xmlns:th = "http://www.thymeleaf.org">
  <body>
  <p th:text="'hello ' + ${name}">hello! empty</p> // hello! empty: 서버 없이 파일을 읽을 때의 기본 값 <- 서버가 작동하면, "'hello ' + ${name}"로 치환됨
  </body>
  </html>
  ```
  - ERROR: Required String parameter 'name' is not present
    ![image](https://user-images.githubusercontent.com/104348646/197746158-814cb117-ab66-4250-a110-fd5f4a6b76f3.png)

* name 파라미터에 값 지정  
  + ex) name = spring!!!  
    ![image](https://user-images.githubusercontent.com/104348646/197758277-39eb94bc-f304-4f93-bbe3-d8251a552536.png)  ![image](https://user-images.githubusercontent.com/104348646/197758403-0bf94c7f-4a08-4c9c-add9-04dea0eeaa82.png)  
  
![image](https://user-images.githubusercontent.com/104348646/197758778-0f59d95d-a85c-47b9-8258-81735ce03cf7.png)  
 
## API
* @ResponseBody 문자 반환
  -  java/hello.hellospring/controller/HelloController
  ```
  ...
      @GetMapping("hello-string")
      @ResponseBody // http에서 body 부에 데이터를 직접 넣음
      public String helloString(@RequestParam("name") String name){
          return "hello " + name; // @@ "hello spring!!!"
      }
  ```
  - @ResponseBody를 사용하면, 뷰 리졸버(viewResolver)를 사용하지 않음
  - 대신에 HTTP의 BODY에 문자 내용을 직접 반환(HTML BODY TAG 아님)

* 실행
![image](https://user-images.githubusercontent.com/104348646/197760878-1fce4d74-b7d7-4034-be30-270cce9700e5.png)  ![image](https://user-images.githubusercontent.com/104348646/197760929-e0f44bb4-70e6-4c2f-b35a-da1b7df26181.png)
  - View 없이 데이터를 그대로 반환함

* @ResponseBody 객체 반환
    ```
    @GetMapping("hello-api")
    @ResponseBody // JSON 형태(기본)으로 반환함
    public Hello helloApi(@RequestParam("name") String name){
        Hello hello = new Hello();
        hello.setName(name);
        return hello;
    }

    static class Hello {
        private String name;

        // property 접근 방식
        public String getName(){
            return name;
        }

        public void setName(String name){
            this.name = name;
        }
    }
    ```  
  ![image](https://user-images.githubusercontent.com/104348646/197762806-26f41db6-97cf-4b26-b471-59425ab5bcfb.png)
    - JSON 형태로 반환: {key: value}

![image](https://user-images.githubusercontent.com/104348646/197763556-ffdbe814-1040-47a6-8dcb-2a402302318b.png)
* @ResponseBody 사용
  - viewResolver 대신에 HttpMessageConverter가 동작
  - 기본 문자처리: StringHttpMessageConverter
  - 기본 객체처리: MappingJackson2HttpMessageConverter
    + Jackson: 객체를 JSON으로 변환하는 대표적인 라이브러리
  - byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음
  - cf) 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서 HttpMessageConverter가 선택됨
    + xml 등으로 타입 변호나 가능








