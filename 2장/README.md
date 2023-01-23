## 2장 - IOC,DI / AOP

IOC
 - 자바 객체를 new가 아닌 SpringContainer가 관리하는 것 = Inversion of Control

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
![자료](.\다운로드.png)

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