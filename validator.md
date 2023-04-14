* 일반적으로 다양한 종류의 데이터, 문서, 또는 코드를 검증하거나 확인하는 역할을 수행하는 소프트웨어, 시스템, 또는 개체를 의미함
  - 프로그래밍: 코드의 문법, 구조, 논리적 일관성을 확인하기 위한 검증 도구로 사용 가능
  - 데이터베이스: 데이터의 정확성, 일관성, 유효성을 검증

* 예시: 매개변수에 정규표현식을 적용
  - CompanyForm
    ```
    package com.tmax.fi.fa.common.company.validator;

    import javax.validation.Constraint;
    import javax.validation.Payload;
    import java.lang.annotation.ElementType;
    import java.lang.annotation.Retention;
    import java.lang.annotation.RetentionPolicy;
    import java.lang.annotation.Target;

    @Target(ElementType.FIELD) // 적용 대상: field
    @Retention(RetentionPolicy.RUNTIME) // 어노테이션이 런타임 시점까지 메타데이터를 유지해야 함을 명시. 컴파일 이후에도 런타임에도 해당 어노테이션 정보를 사용 가능
    @Constraint(validatedBy = CompanyFormValidator.class) // CompanyFormValidator와 연결됨
    public @interface CompanyForm {
        // 정규표현식에 맞지 않은 경우, 출력되는 에러 메세지
        String compNameMessage() default "한글, 영문, 특수문자 외에는 사용이 불가합니다";
        String regNumMessage() default "'6자리 숫자 - 6자리 숫자' 형식으로 입력해야 합니다";
        String callNumMessage() default "'(2~3자리)숫자 - (3~4자리)숫자 - (4자리)숫자' 형식으로 입력해야 합니다";

        Class<?>[] groups() default {}; // 특정 그룹들에만 적용
        Class<? extends Payload>[] payload() default{}; // 특정 페이로드(Payload)를 전달
    }
    ```
    
  - CompanyFormValidator
    ```
    package com.tmax.fi.fa.common.company.validator;

    import com.tmax.fi.fa.common.company.dto.CompanyRequestDto;

    import javax.validation.ConstraintValidator;
    import javax.validation.ConstraintValidatorContext;
    import java.util.regex.Pattern;

    public class CompanyFormValidator implements ConstraintValidator<CompanyForm, CompanyRequestDto> {

        private String compNameMessage;
        private String regNumMessage;
        private String callNumMessage;

        public void buildContext(ConstraintValidatorContext c){
            c.disableDefaultConstraintViolation();
            c.buildConstraintViolationWithTemplate(this.compNameMessage)
                    .addPropertyNode(regNumMessage)
                    .addPropertyNode(callNumMessage)
                    .addConstraintViolation();
        }

        @Override
        public void initialize(CompanyForm constraintAnnotation){
            this.compNameMessage = constraintAnnotation.compNameMessage();
            this.regNumMessage = constraintAnnotation.regNumMessage();
            this.callNumMessage = constraintAnnotation.callNumMessage();
        }

        @Override
        public boolean isValid(CompanyRequestDto dto, ConstraintValidatorContext context){
            // 상호
            String namePattern = "^[a-zA-Z][가-힣]*$";
            boolean nameMatcher = Pattern.matches(namePattern, dto.getCompanyName());

            if(nameMatcher == false){
                return false;
            }

            // 등록번호
            String regNumPattern = "^[0-9]{6}-[0-9]{6}$";
            boolean regNumMatcher = Pattern.matches(regNumPattern, dto.getCompRegistrationNum());

            if(regNumMatcher == false){
                return false;
            }

            // 본점 전화번호
            String callNumPattern = "^[0-9]{2,3}-[0-9]{3,4}-[0-9]{4}$";
            boolean callNumMatcher = Pattern.matches(callNumPattern, dto.getCompCallNum());

            if(callNumMatcher == false){
                return false;
            }

            return true;
        }
    }
    ```
