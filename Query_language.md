# Query 언어의 종류
| | Selct 검색 조건 지정 | 2개 이상의 join|
|-------|--------------|--------------|
QuerryDsl | O | X |
JPQL | X | X |
JPQL(native Query) | O | O |
JPAQuery, JPAQueryFactory(Q class) | O | O |

* 단, 해당 Entity와의 관계가 다대일인 Attribute는 2개 이상이 되는 경우, JPAQuery, JPAQueryFactory 방법만 사용 가능함  
  
![image](https://user-images.githubusercontent.com/104348646/211984875-543f41da-5613-4960-8f8f-e3f6bb3624c0.png)  

## Querydsl - JPAQueryFactory

* 이미 테이블이 존재하는 경우 이를 바탕으로 쿼리타입을 생성하여 질의할 때 이용하는 방식

* 결과를 DTO로 조회할 때, 빈을 생성하는 방법
```
    // setter를 이용하여 주입
    @Test
    public void findDtoBySetter() {
        List<MemberDto> result = queryFactory
                .select(Projections.bean(MemberDto.class, 
                            member.username,
                            member.age))
                .from(member)
                .fetch();
    }

    // 필드에 바로 값을 꽂음
    @Test
    public void findDtoByField() {
        List<MemberDto> result = queryFactory
                .select(Projections.fields(MemberDto.class, 
                            member.username,
                            member.age))
                .from(member)
                .fetch();
    }

    // 생성자를 이용하여 주입
    @Test
    public void findDtoByConstructor() {
        List<MemberDto> result = queryFactory
                .select(Projections.constructor(MemberDto.class, 
                            member.username,
                            member.age))
                .from(member)
                .fetch();
    }
```

* 참고
  - Querydsl의 종류: http://ojc.asia/bbs/board.php?bo_table=LecJpa&wr_id=341
  - 빈 생성 방법: https://ykh6242.tistory.com/entry/QueryDSL3-%ED%94%84%EB%A1%9C%EC%A0%9D%EC%85%98-distinct-%EB%8F%99%EC%A0%81-%EC%BF%BC%EB%A6%AC-%EB%B2%8C%ED%81%AC-%EC%97%B0%EC%82%B0

## Querydsl - JPASqlQuery

* @Query 어노테이션을 사용함
* JPQL 형식으로 작성

## JPQL

### 기본 문법
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

### JOIN FETCH
: 연관된 엔티티나 컬렉션을 한 번에 같이 조회하는 기능

* 한계
  - 페치 조인 대상에 별칭을 줄 수 없는 경우(가능하지만, 데이터 무결성이 깨질 가능성 존재)
  - 둘 이상의 컬렉션을 페치할 수 없는 경우(구현체에 따라 가능할 수 있음)
  - 페이징 API 사용할 수 없는 경우
    + 페이징 API 사용 가능: 단일 값 연관 필드(일대일, 다대일)
    + 페이징 API 사용 불가: 컬렉션(일대다)
* 참고: https://kihwan95.tistory.com/12

### 예시

#### 1. 기본
* Entity
  ```
  @Entity
  
  public class Member{
  @Id
  @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "member_id"}
  private Long id;
  
  @Column(name = "member_name")
  private String name;
  ```
* Repository
  ```
  @Repository
  
  public interface MemberRepository{
  @Query("select m from member m where id = :id}
  Optional<Member> findById(@Param("id" Long id)
  ```
  
#### 2. 매개변수의 타입이 복합된 경우
* Entity
  ```
  @Entity
  
  public class Member{
  @Id
  @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "member_id"}
  private IdInfo id;
  
  @Column(name = "member_name")
  private String name;
  ```
  ```
  @Entity
  
  public class IdInfo{
  @Id
  @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "member_id"}
  private Long id;
  
  @Column(name = "seq")
  private Long seq;
  ```
* Repository
  ```
  @Repository
  
  public interface MemberRepository{
  @Query("select m from member m where m.IdInfo.id = :id} // IdInfo Entity의 id 값(타입)을 의미
  Optional<Member> findById(@Param("id" Long id)
  ```
