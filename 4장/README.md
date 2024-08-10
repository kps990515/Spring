## 4장 - Exception

- @ControllerAdivce : Global예외처리 및 특정 package/controller예외처리
- @ExceptionHandler : 특정 Exception 예외처리

1. 전체 Exception 처리
```java
// basePackageClasses를 통해 클래스마다 지정가능(APiController 클래스안에서 예외를 처리)
@RestControllerAdvice(basePackageClasses = ApiController.class)
public class ApiControllerAdvice {
    // Exception클래스에 해당하는 exception이 뜨면 아래처럼 처리
    @ExceptionHandler(value = Exception.class)
    public ResponseEntity exception(Exception e){
        System.out.println(e.getClass().getName());
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
    }
}
```

2. 함수 Argument 잘못 들어왔을 시
```java
@ExceptionHandler(value = MethodArgumentNotValidException.class)
public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e, HttpServletRequest httpServletRequest){

    List<Error> errorList = new ArrayList<>();

    BindingResult bindingResult = e.getBindingResult();
    bindingResult.getAllErrors().forEach(error -> {
        FieldError field = (FieldError) error;

        String fieldName = field.getField();
        String message = field.getDefaultMessage();
        String value = field.getRejectedValue().toString();

        Error errorMessage = new Error();
        errorMessage.setField(fieldName);
        errorMessage.setMessage(message);
        errorMessage.setInvalidValue(value);

        errorList.add(errorMessage);
    });

    ErrorResponse errorResponse = new ErrorResponse();
    errorResponse.setErrorList(errorList);
    errorResponse.setMessage("");
    errorResponse.setRequestUrl(httpServletRequest.getRequestURI());
    errorResponse.setStatusCode(HttpStatus.BAD_REQUEST.toString());
    errorResponse.setResultCode("FAIL");

    return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
}
```

3. 제약조건 위배(controller단 파라미터 제약 위배)
```java
@ExceptionHandler(value = ConstraintViolationException.class)
public ResponseEntity constraintViolationException(ConstraintViolationException e, HttpServletRequest httpServletRequest){

    List<Error> errorList = new ArrayList<>();

    e.getConstraintViolations().forEach(error ->{
        Stream<Path.Node> stream = StreamSupport.stream(error.getPropertyPath().spliterator(), false);
        List<Path.Node> list = stream.collect(Collectors.toList());

        String field = list.get(list.size() -1).getName();
        String message = error.getMessage();
        String invalidValue = error.getInvalidValue().toString();

        Error errorMessage = new Error();
        errorMessage.setField(field);
        errorMessage.setMessage(message);
        errorMessage.setInvalidValue(invalidValue);

        errorList.add(errorMessage);

    });

    ErrorResponse errorResponse = new ErrorResponse();
    errorResponse.setErrorList(errorList);
    errorResponse.setMessage("");
    errorResponse.setRequestUrl(httpServletRequest.getRequestURI());
    errorResponse.setStatusCode(HttpStatus.BAD_REQUEST.toString());
    errorResponse.setResultCode("FAIL");

    return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
}
```

4. API요청 시 필요값 부재
```java
@ExceptionHandler(value = MissingServletRequestParameterException.class)
public ResponseEntity missingServletRequestParameterException(MissingServletRequestParameterException e, HttpServletRequest httpServletRequest){

    List<Error> errorList = new ArrayList<>();

    String fieldName = e.getParameterName();
    String invalidValue = e.getMessage();

    Error errorMessage = new Error();
    errorMessage.setField(fieldName);
    errorMessage.setMessage(e.getMessage());


    ErrorResponse errorResponse = new ErrorResponse();
    errorResponse.setErrorList(errorList);
    errorResponse.setMessage("");
    errorResponse.setRequestUrl(httpServletRequest.getRequestURI());
    errorResponse.setStatusCode(HttpStatus.BAD_REQUEST.toString());
    errorResponse.setResultCode("FAIL");

    return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
}
```
