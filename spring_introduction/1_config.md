## 프로젝트 생성
1. https://start.spring.io/  
![image](https://user-images.githubusercontent.com/104348646/197506827-43aec1ce-31c1-4f16-81a3-8f63212bbbf7.png)  
  * Project
    - 빌드 도구 기반 프로젝트 선택

  * Spring Boot
    - SNAPSHOT: 개발 중인 버전

  * Project Metadata
    - Group: 보통 회사명을 사용함
    - Artifact: 빌드 결과물

  * Dependency
    - 사용할 라이브러리 선택
    - Template Engine 사용 필요

2. [GENARATE] - 압축 풀기
3. IntelliJ에서 hello-spring/build.gradle로 프로젝트 생성

### 프로젝트 구조
![image](https://user-images.githubusercontent.com/104348646/197516789-edce9374-8dd1-4fc5-ad32-4aee1bb42b05.png)  
* .idea: IntelliJ가 사용하는 설정 파일
* gradle/wrapper: gradle 사용 폴더
* src  
    - main과 test로 나누어짐
    - main
        + main/java: 실제 패키지, 소스 파일들
        + main/resources: java code 파일을 제외한 html, xml, properties 등의 설정 파일들
    - test
        + test code와 관련된 소스 파일들
 * build.gradle
    - 버전 설정, library 불러오는 역할
    - spring boot에서 설정한 plugin 들이 저장되어 있음
    - sourceCompatibility: Java 버전
    - repositories
        + mavenCentral: 라이브러리를 다운받을 수 있는 사이트
    - dependencies: test 라이브러리가 기본으로 들어가 있음
* .gitignore: 소스 코드 관리

* scr/main/java/hello.hellospring/HelloSpringApplication
    ![image](https://user-images.githubusercontent.com/104348646/197516924-86d6ccf0-a3c4-4bc6-a687-e05e5846b6b2.png)  
    - line 9(main) 실행
    - Tomcat started on port(s): 8080 (http)
    - 웹에서 localhost:8080 검색
    - Whitelabel Error Page -> 환경설정 완료 됨
    
    
* 설정/Gradle
![image](https://user-images.githubusercontent.com/104348646/197514372-17424a64-16f9-46eb-89bc-5d9cf6c909a6.png)
    - 다음을 사용하여 빌드 및 실행, 다음을 사용하여 테스트 실행: IntelliJ IDEA로 설정

## 라이브러리 살펴보기
* Gradle은 의존관계가 있는 라이브러리를 함께 다운로드 함  
![image](https://user-images.githubusercontent.com/104348646/197664516-96aea3ef-d9f3-4aa0-9dba-137e0bbde00c.png)  

### 스프링 부트 라이브러리
* spring-boot-starter-web
  - spring-boot-starter-tomcat: 톰캣(웹서버)
  - spring-webmvc: 스프링 웹 MVC
* spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View)
* spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅
  - spring-boot
    + spring-core
  - spring-boot-starter-logging
    + logback, slf4j
    
### 테스트 라이브러리
* spring-boot-starter-test
  - junit: 테스트 프레임워크
  - mockito: 목 라이브러리
  - assertj: 테스트 코드를 더 편하게 작성하도록 도와주는 라이브러리
  - spring-test: 스프링 통합 테스트 지원

## View 환경설정
### Welcome Page 만들기(홈 화면)
1. resources/static/index.html 파일 생성
  ```
  <!DOCTYPE HTML>
  <html>
  <head>
      <title>Hello</title>
      <meta http-equiv="Content-Type" content="text/html; charset = UTF-8" />
  </head>
  <body>
  Hello
  <a href="/>hello">hello</a>
  </body>
  </html>
  ```
2. HelloSpringApplication.java 중지 및 재실행
3. web에서 localhost:8080 검색
* 스프링 부트가 제공하는 Welcome Page 기능
  - static/index.html을 올려두면 Welcome page 기능을 제공함
  - https://spring.io/ -> Projects -> Spring Boot -> LEARN -> 해당 버전의 Reference Doc -> Spring Boot Features -> 7.2.4 Welcome Page
* 정적(static) 성격: 파일을 그대로 출력함

### thymeleaf 템플릿 엔진
* 파일을 동적으로 변환함
* 스프링 공식 튜토리얼: https://spring.io/guides/gs/serving-web-content/
* 스프링부트 메뉴얼: [https://docs.spring.io](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-mvc-template-engines)

* java/hello.hellospring.controller package 생성 -> helloController 클래스 생성
  ```
  package hello.hellospring.controller;

  import org.springframework.stereotype.Controller;
  import org.springframework.ui.Model;
  import org.springframework.web.bind.annotation.GetMapping;

  @Controller
  public class HelloController {

      @GetMapping("hello") // Get 방식으로 넘어옴
      public String hello(Model model){ // spring이 model을 만들어서 넘겨줌
          model.addAttribute("data", "hello!!");
          return "hello"; // hello: resources/templates/hello.html를 찾아서 랜더링 함(실행)
      }
  }
  ```
* resources/templates/hello.html 파일 생성
  ```
  <!DOCTYPE HTML>
  <html xmlns:th = "http://www.thymeleaf.org"> // thymeleaf 선언됨
  <head>
      <title>Hello</title>
      <meta http-equiv = "Content-Type" content = "text/html; charset = UTF-8" />
  </head>
  <body>
  <p th:text = "'안녕하세요.  ' + ${data}" >안녕하세요. 손님</p> // th: thymeleaf 문법 가져옴 / ${data}: model.addAttribute의 value 호출해서 치환: hello!!
  </body>
  </html>
  ```

* thymeleaf 템플릿엔진 동작 확인
  ![image](https://user-images.githubusercontent.com/104348646/197689831-73fa840d-076e-4071-b358-c7f983251e5b.png)  
  ![image](https://user-images.githubusercontent.com/104348646/197690368-048e7510-a2ba-4136-bdf1-7bdf023e8b3f.png)  
  - 컨트롤러에서 리턴 값으로 문자를 반환하면 뷰 리졸버(viewResolver)가 화면을 찾아서 처리함
    + 스프링 부트 템플릿엔진 기본 viewName 매핑
    + ```resources:templates/``` +{ViewName}+```.html```
  
  - cf) ```spring-boot-devtools``` 라이브러리를 추가하면, html 파일을 컴파일만 해주면 서버 재시작 없이 View 파일 변경이 가능
    + IntelliJ: 메뉴 build -> Recompile

## 빌드하고 실행하기
* cmd로 이동  
![image](https://user-images.githubusercontent.com/104348646/197695687-7cb73446-4f96-414a-932a-fa721ee263fe.png)  
1. ./gradlew.bat
2. gradlew build
3. cd build/libs
4. java -jar hello-spring-0.0.1-SNAPSHOT.jar
5. 실행 확인



