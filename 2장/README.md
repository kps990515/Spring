## 2장 - IOC,DI / AOP

IOC
 - 객체의 생성과 의존성 관리의 제어 권한을 프레임워크나 컨테이너에 위임함으로써, 코드의 유연성, 재사용성, 테스트 용이성을 높이는 소프트웨어 설계 원칙
 - 자바 객체를 new가 아닌 SpringContainer가 관리하는 것 = Inversion of Control
 - DI을 통해 구현(생성자, Setter, 필드 주입)
 - 객체 간 결합도를 낮춰 의존성 관리, 코드유연성 향상

### IOC
```java
@SpringBootApplication
public class IocApplication {

    public static void main(String[] args) {
        SpringApplication.run(IocApplication.class, args);
        ApplicationContext context = ApplicationContextProvider.getContext();

        //Base64Encoder base64Encoder = context.getBean(Base64Encoder.class);
        //UrlEncoder urlEncoder = context.getBean(UrlEncoder.class);

        Encoder encoder = context.getBean("urlEncode", Encoder.class);
        String url = "www.naver.com/books/it?page=10&size=20&name=spring-boot";
        String result = encoder.encode(url);
        System.out.println(result);
    }

}

@Configuration
class AppConfig{

    @Bean("base64Encode")
    public Encoder encoder(Base64Encoder base64Encoder){
        return new Encoder(base64Encoder);
    }

    @Bean("urlEncode")
    public Encoder encoder(UrlEncoder urlEncoder){
        return new Encoder(urlEncoder);
    }
}
```

### AOP
- 핵심 비즈니스 로직과는 별개의 횡단 관심사(로깅, 보안, 트랜잭션 처리 등)를 모듈화
- 코드의 중복을 줄이고, 유지보수성을 높이는 중요한 프로그래밍 패러다임

1. 목적
- 로깅, 보안, 트랜잭션과 같은 횡단관심사를 비즈니스 로직에서 분리
- 모듈화해서 중복 코드 줄임
- 유지보수성 향상

2. 주요개념
- Aspect : 모듈화한 코드
- Join Point : Aspect가 적용되는 지점(메서드 호출시, 객체 생성 시)
- Advice : Join Point에서 실행되는 코드
- Point Cut : 어느 Joinpoint에 어느 Advice를 적용시킬지 설정하는 표현식

![자료](https://github.com/kps990515/Spring/blob/main/2%EC%9E%A5/%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C.png)

- Parameter AOP
```java
@Aspect
@Component
public class ParameterAop {

    @Pointcut("execution(* com.example.aop.controller..*.*(..))")
    private void cut(){}
    
    //실행 할 함수 가져오기
    @Before("cut()")
    public void before(JoinPoint joinPoint){
        MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();
        Method method = methodSignature.getMethod();
        System.out.println(method.getName());
        Object[] args = joinPoint.getArgs();
        for(Object obj : args){
            System.out.println("type : "+obj.getClass().getSimpleName());
            System.out.println("value : "+obj);
        }
    }

    @AfterReturning(value = "cut()", returning = "returnObj")
    public void afterReturn(JoinPoint joinPoint, Object returnObj){
        System.out.println("return obj");
        System.out.println(returnObj);
    }
}
```

- Timer AOP
```java
@Aspect
@Component
public class TimerAop {
    //controller하위 메소드 전부 실행
    @Pointcut("execution(* com.example.aop.controller..*.*(..))")
    private void cut(){}
    //Timer어노테이션이 걸린 것만 실행
    @Pointcut("@annotation(com.example.aop.annotation.Timer)")
    private void enableTimer(){}

    @Around("cut() && enableTimer()")
    public void around(ProceedingJoinPoint joinPoint) throws Throwable {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();

        Object result = joinPoint.proceed();

        stopWatch.stop();
        System.out.println("total time : "+stopWatch.getTotalTimeSeconds());
    }
}
```

- Decode AOP
```java
@Aspect
@Component
public class DecodeAop {

    @Pointcut("execution(* com.example.aop.controller..*.*(..))")
    private void cut(){}

    @Pointcut("@annotation(com.example.aop.annotation.Decode)")
    private void enableDecode(){}


    @Before("cut() && enableDecode()")
    public void before(JoinPoint joinPoint) throws UnsupportedEncodingException {
        Object[] args = joinPoint.getArgs();
        for(Object arg : args){
            if(arg instanceof User){
                User user = User.class.cast(arg);
                String base64Email = user.getEmail();
                String email = new String(Base64.getDecoder().decode(base64Email),"UTF-8");
                user.setEmail(email);
            }
        }
    }

    @AfterReturning(value = "cut() && enableDecode()", returning = "returnObj")
    public void afterReturn(JoinPoint joinPoint, Object returnObj){
        if(returnObj instanceof  User){
            User user = User.class.cast(returnObj);
            String email = user.getEmail();
            String base64Email = Base64.getEncoder().encodeToString(email.getBytes());
            user.setEmail(base64Email);
        }
    }
}
```

```java
@RestController
@RequestMapping("/api")
public class RestApiController {

    @GetMapping("/get/{id}")
    public String get(@PathVariable Long id, @RequestParam String name){
        // TODO

        return id+ " "+ name;
    }

    @PostMapping("/post")
    public User post(@RequestBody User user){


        return user;
    }

    @Timer
    @DeleteMapping("/delete")
    public void delete() throws InterruptedException {

        // db logic
        Thread.sleep(1000 * 2);

    }

    @Decode
    @PutMapping("/put")
    public User put(@RequestBody User user){
        System.out.println("put");
        System.out.println(user);
        return user;
    }

}
```

### ObjectMapper
```java
String json = objectMapper.writeValueAsString(user);
System.out.println(json);

User parsing = objectMapper.readValue(json, User.class);
System.out.println(parsing);

// node parsing

JsonNode jsonNode = objectMapper.readTree(json);
String name = jsonNode.get("name").asText();
int age = jsonNode.get("age").asInt();
System.out.println(name);
System.out.println(age);

JsonNode cars = jsonNode.get("car");
ArrayNode arrayNode = (ArrayNode)cars;
List<Car> _car = objectMapper.convertValue(arrayNode, new TypeReference<List<Car>>() {});
System.out.println(_car);


ObjectNode objectNode = (ObjectNode)jsonNode;
objectNode.put("name","abcd");
System.out.println(objectNode.toPrettyString());
```