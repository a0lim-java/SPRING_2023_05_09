### java: warning: source release 11 requires target release 11
* 원인: 프로젝트의 Java 버전이 일관되지 않음
  - ex) 본 프로젝트: Java 8 / Java 11 버전을 참조 시도
* 해결
  1 Lanuage Level 설정
  - File -> Project Structure
  - Project Settings -> Modules -> Language level 버전 변경 -> 적용 -> 확인 클릭
    + Project Settings -> Project -> Project language level과 동일하게
  2 Java Compiler 설정
  - shift + shift -> 검색창 -> Java Compiler 검색 -> Setting 항목을 클릭
  - Settings -> Java Compiler -> Per-module bytecode version -> + 버튼 클릭
  - 현재 프로젝트를 선택 -> 확인 클릭
  - 추가한 모듈의 Target Bytecode version 변경 -> 적용 -> 확인 클릭
    + Project Settings -> Project -> Project language level과 동일하게
  - Build -> Rebuild Project 클릭
  
### ERROR: JAVA_HOME is set to an invalid directory: C:\Program Files (x86)\Java\jdk-11.0.2+9;
* 원인: 시스템 환경 변수 경로
* 해결
  - Window -> 시스템 환경 변수 편집 -> 환경변수
  - 시스템 변수 -> JAVA_HOME -> 편집 버튼 -> 뒤의 ;, bin 등을 제거
  - ex) C:\Program Files\Java\jdk1.8.0_341

### Execution failed for task ':compileJava'. > invalid source release: 11
* 원인: JDK 버전과 project language level이 다름
* 해결
  - File -> Project Structure
  - Project -> Project language level: SDK default로 변환 -> 확인
  - build.gradle 파일 -> sourceCompatibility를 SDK default 버전에 맞게 수정
  - Load Grandle Changes -> build, run
