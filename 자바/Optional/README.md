## Optional

- T타입 객체의 래퍼클래스 : Optional<T>
- 간접적으로 Null을 다루기위한 용도
- 래퍼클래스는 항상 주소가 있기 때문에 NPE가 발생하지 않는다

### 생성하기
```java
String str = "abc";
Optional<String> optVal = Optional.of(str);
Optional<String> optVal = Optional.of(null); // NPE 발생
Optional<String> optVal = Optional.ofNullable(null); // OK

// null 대신 빈 Optional 객체 사용하기
Optional<String> optVal = Optional.empty();
```

### 값 가져오기
```java
Optional<String> optVal = Optional.of(str);
String str1 = optVal.get();
String str2 = optVal.orElse("") //optVal에 저장된 값이 null 일때 ""반환
String str3 = optVal.orElseGet(String::new) 
String str4 = optVal.orElseThrow(NPE::new) // null이면 예외 발생

// isPresent() : null이면 false 아니면 true
if(Optional.ofNullable(str).isPresent()){
    sysout(str);
}
```

### 기본형 래퍼클래스
```java
OptionalInt, OptionalLong, OptionalDouble;
```