## CONTROLLER

```
private final Service service; // service 호출

@ApiOperation(value = “이름”) 	// swagger 할 때만 사용
@Getmapping(“api 주소”) 	// @Postmapping // Putmapping // DeleteMapping

public type method (@RequestBody type Input_변수) {  
    return  service.method(Input_변수)	 // Input_변수: dto, String 등  
}
```


## DTO

```
@Getter
@Builder
@NoArgsConstructor
@AllArgsConstructor

public class Dto {
@ApiModelProperty(value = “한글이름”  
    private type Input_field  
}
```


## ENTITY

```
@Entity
@NoArgsConstructor
@Getter
@DynamicInsert
@DynamicUpdate
public class entity {
@Id
@NotNull
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "fiscal_year_seq")	 // 자동 번호 부여
    @Column(name = "Input_field", unique = true)  
    private type Input_field;  
}



// 추가 함수
public void crud(
type field){

    this. field = field  
}
```


## REPOSITORY

```
public interface Repository extends JpaRepository<entity, primary_key>{
	@Query(“query”)
	Optional<output_변수> repository_method();

```

## SERVICE

```
@Service
@RequireddArgsConstructor
public class Service {
}
```
