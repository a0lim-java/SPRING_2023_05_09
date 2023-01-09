## Repository의 기본 메소드
  - findAll()
  - findById(id)
  - save(table)
  - saveAll(tableList)
  - delete(table)
  - deleteAll(tableList)
  - count()
  - exist(id): id에 해당하는 레코드가 있는지 확인하고 true/false를 리턴
  - flush(): 지금까지 테이블에 대한 데이터 변경 작업들을 모두 디스크에 기록


## 객체 생성 방법
* 공통 예시 Entity
```
@Entity

public class Student {
  @Id
  private Long id;
  
  @NotNull
  private String name}
  
  @ManyToOne(targetEntity = Teacher.class) // Teacher Entity와 연결된 상태
  @JoinColumn(name)
  private Teacher teacherName;
```

1. 생성자 사용
```
// Entity에 생성자 생성

public Student(Long id,
               String name,
               Teacher teacherName){
    this.id = id;
    this.name = name;
    this.teacherName = teacherName;
    }
   
public static Student of(
    Long id,
    String name,
    Teacher teacherName
){
    return new Student(
        id,
        name,
        teacherName
    );
      }
      
// Service에서 사용

Student [newVariable] = Student.of(
    [Student 타입의 변수].getId(),
    [Student 타입의 변수].getName()
);
    

```

2. new
```
// Service에서 사용: 변수의 타입을 찾아서 매핑해야 함

Student [newVariable] = new Student(
    studentRepository.getReferenceById([Student 타입의 변수].getId()),
    studentRepository.getReferenceById([Student 타입의 변수].getName())
);
```

## fetch 종류
* fetch()
  : 리스트로 결과를 반환
  - 데이터가 없는 경우, 빈 리스트를 반환
* fetchOne()
  : 단건 조회
  - 결과가 없는 경우, null을 반환
  - 결과가 두 개 이상인 경우, NonUniqueResultException Error
* fetchFirst()
  : 처음 한 건만 쿼리해서 가져옴
* fetchResults()
  : Paging
* fetchCount()
  : count 쿼리해서 가져옴
  
  
  
  
  
