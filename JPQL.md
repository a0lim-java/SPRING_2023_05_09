## 기본 문법
* SQL과 유사
  - SELECT, FROM, WHERE, GROUP BY, HABING, JOIN 지원
* 쿼리 검색 시, 테이블이 아닌 엔티티 객체를 대상으로 검색
  - ex) select * from ENTITY
* 대소문자 구별
  - 구별 O: 엔티티, 속성
  - 구별 X: JPQL 키워드(SELECT 등)
* 별칭 필수
  - ex) AS 생략: SELECT M FROM MEMBER M
  - ex) AS 포함: SELECT M FROM MEMBER AS M

* 참고: https://velog.io/@guswns3371/%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-%EC%BF%BC%EB%A6%AC-%EC%96%B8%EC%96%B41-%EA%B8%B0%EB%B3%B8-%EB%AC%B8%EB%B2%95#jpql---%EA%B8%B0%EB%B3%B8-%EB%AC%B8%EB%B2%95%EA%B3%BC-%EA%B8%B0%EB%8A%A5

## JOIN FETCH
: 연관된 엔티티나 컬렉션을 한 번에 같이 조회하는 기능

* 한계
  - 페치 조인 대상에 별칭을 줄 수 없는 경우(가능하지만, 데이터 무결성이 깨질 가능성 존재)
  - 둘 이상의 컬렉션을 페치할 수 없는 경우(구현체에 따라 가능할 수 있음)
  - 페이징 API 사용할 수 없는 경우
    + 페이징 API 사용 가능: 단일 값 연관 필드(일대일, 다대일)
    + 페이징 API 사용 불가: 컬렉션(일대다)
* 참고: https://kihwan95.tistory.com/12
