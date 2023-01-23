## 3장 - Validation

![자료](https://github.com/kps990515/Spring/blob/main/2%EC%9E%A5/%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C.png)

### Default Validation
```java
public class User {

    @NotBlank
    private String name;

    @Max(value = 90)
    private int age;

    @YearMonth
    private String yearMonth;
}   
```

### Custom Validation
- Annotation
```java
@Constraint(validatedBy = {YearMonthValidator.class})
@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
@Retention(RUNTIME)
public @interface YearMonth {

    String message() default "yyyyMM 형식에 맞지 않습니다.";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };

    String pattern() default "yyyyMMdd";
}
```

- 활용
1. @AssertTrue 활용(DTO에서)
```java
@AssertTrue(message = "yyyymm의 형식에 맞지 않습니다")
// 함수명에 is를 넣어줘야 작동함
public boolean isReqYearMonthValidation(){
    try{
        LocalDate localDate = LocalDate.parse(value+"01" , DateTimeFormatter.ofPattern(this.pattern));
    }catch (Exception e){
        return false;
    }
}
```

2. ConstraintValidator 활용
```java
// ConstraintValidator<어노테이션, 받을 값>
public class YearMonthValidator implements ConstraintValidator<YearMonth, String> {

    private String pattern;

    @Override
    public void initialize(YearMonth constraintAnnotation) {
        this.pattern = constraintAnnotation.pattern();
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        // yyyyMM
        try{
            LocalDate localDate = LocalDate.parse(value+"01" , DateTimeFormatter.ofPattern(this.pattern));
        }catch (Exception e){
            return false;
        }


        return true;
    }
}
```

```java
@PostMapping("/user")
public ResponseEntity user(@Valid @RequestBody User user, BindingResult bindingResult){

    if(bindingResult.hasErrors()){
        StringBuilder sb = new StringBuilder();
        bindingResult.getAllErrors().forEach(objectError -> {
            FieldError field = (FieldError) objectError;
            String message = objectError.getDefaultMessage();

            System.out.println("field : "+field.getField());
            System.out.println(message);

            sb.append("field : "+field.getField());
            sb.append("message : "+message);

        });

        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(sb.toString());
    }


    return ResponseEntity.ok(user);
}
```