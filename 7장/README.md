## 7장 - RestTemplate

### ClientSide
```java
@RestController
@RequestMapping("/api/user")
@RequiredArgsConstructor
public class UserApiController {

    private final UserService userService;

    @GetMapping("")
    public void get(){
        userService.naver();
    }

}
```

```java
@Slf4j
@Service
public class UserService {
// 1. 원하는 object형으로 받기
    public void getForObject(){
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")
                .path("/api")
                .queryParam("name","steve")
                .queryParam("age",10)
                .encode()
                .build()
                .toUri();
        log.info("uri : {}",uri);

        RestTemplate restTemplate = new RestTemplate();
        User user = restTemplate.getForObject(uri, User.class);
        log.info("user : {}", user);
    }
// 2. Entity로 받기(getForObject보다 받은 값 포함 : header, status, body...)
    public void getForEntity(){
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")
                .path("/api/{path}")
                .queryParam("name","steve")
                .queryParam("age",10)
                .encode()
                .build()
                .expand("user") //{path}에 들어갈 값
                .toUri();
        log.info("uri : {}", uri);

        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<User> response = restTemplate.getForEntity(uri, User.class);
        log.info("{}",response.getStatusCode());
        log.info("{}",response.getHeaders());
        log.info("{}",response.getBody());
    }

    public void postForObject(){
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")
                .path("/api/{path}")
                .encode()
                .build()
                .expand("user")
                .toUri();
        log.info("uri : {}", uri);

        RestTemplate restTemplate = new RestTemplate();

        User user = new User();
        user.setName("홍길동");
        user.setAge(10);
        User response = restTemplate.postForObject(uri, user, User.class);
        log.info("response : {}", response);
    }

    public void postForEntity(){
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")
                .path("/api/{path}")
                .encode()
                .build()
                .expand("user")
                .toUri();
        log.info("uri : {}", uri);

        User user = new User();
        user.setName("홍길동");
        user.setAge(10);

        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<User> response = restTemplate.postForEntity(uri, user, User.class);
        log.info("{}",response.getStatusCode());
        log.info("{}",response.getHeaders());
        log.info("{}",response.getBody());
    }
// 실무에서 쓰이는 방식(header, contenttype 추가)
    public void exchange(){
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")
                .path("/api/{path}/header")
                .encode()
                .build()
                .expand("user")
                .toUri();
        log.info("uri : {}", uri);

        User user = new User();
        user.setName("홍길동");
        user.setAge(10);

        RequestEntity<User> req = RequestEntity
                .post(uri)
                .contentType(MediaType.APPLICATION_JSON)
                .header("x-authorization","my-header")
                .body(user);

        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<User> response = restTemplate.exchange(req, new ParameterizedTypeReference<>(){});
        log.info("{}",response.getStatusCode());
        log.info("{}",response.getHeaders());
        log.info("{}",response.getBody());
    }



    public void naver(){
        URI uri = UriComponentsBuilder
                .fromUriString("https://openapi.naver.com")
                .path("/v1/search/local.json")
                .queryParam("query","%EC%A3%BC%EC%8B%9D")
                .queryParam("display","10")
                .queryParam("start","1")
                .queryParam("sort","random")
                .encode()
                .build()
                .toUri();
        log.info("uri : {}", uri);

        RequestEntity<Void> req = RequestEntity
                .get(uri)
                .header("X-Naver-Client-Id","Zi3o1uQftp59zuIqEAz4")
                .header("X-Naver-Client-Secret","iy6YKSWpLM")
                .build();

        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<String> response = restTemplate.exchange(req, new ParameterizedTypeReference<>(){});
        log.info("{}",response.getStatusCode());
        log.info("{}",response.getHeaders());
        log.info("{}",response.getBody());
    }
}
```

```java
@Configuration
public class AppConfig {

    @Bean("asyncThread")
    public Executor asyncThread(){
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10);
        executor.setMaxPoolSize(100);
        executor.setQueueCapacity(10);
        executor.setThreadNamePrefix("Async-");
        return executor;
    }
}
```