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

