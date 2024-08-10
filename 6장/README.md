## 6장 - 비동기

1. @Async : 메서드를 비동기로 실행
- 반환타입
    - void
    - Future<T> : 메서드의 결과를 나중에 비동기적으로 수신
    - CompletableFuture<T> : Future 확장 버전
    - ListenableFuture<T> : 완료 후 콜백이 가능한 Future

```java
@Service
public class TaskService {

    @Resource(name = "asyncThread")
    private Executor executor;

    private final Logger logger = LoggerFactory.getLogger(this.getClass());
    //활용할 config Thread이름 넣기
    @Async("asyncThread")
    public void run(int i) {
        logger.info("count : {}",i);
    }

    @Async("asyncThread")
    public ListenableFuture<Integer> listenableFuture(int i) throws InterruptedException {
        Thread.sleep(1000);
        logger.info("----------------");
        return new AsyncResult<>(i);
    }

    @Async("asyncThread")
    public CompletableFuture<String> completableFuture(int i) throws InterruptedException {
        /*CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(()-> hello(),executor);
        completableFuture.thenApplyAsync(s -> {
            try {
                Thread.sleep(4000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            logger.info("completable : {}",s);
            return "completable";
        },executor);*/

        return new AsyncResult<String>(hello()).completable();
    }

    public String hello() {
        logger.info("hello Method Call");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "hellosss";
    }
}
```

```java
@RestController
@RequestMapping("/api")
public class ApiController {

    private final Logger logger = LoggerFactory.getLogger(this.getClass());

    @Autowired
    private TaskService taskService;

    @GetMapping("/hello")
    public String hello() {
        logger.info("Thread Start");

        for(int i = 0 ; i < 10; i ++){
            //async(i);
            taskService.run(i);
        }

        logger.info("Thread end");
        return "response hello";
    }

    @GetMapping("/future")
    public ListenableFuture<Integer> listenableFuture() throws InterruptedException, ExecutionException {
        logger.info("future Start");
        logger.info("future end");
        return taskService.listenableFuture(10000);
    }

    @GetMapping("/completableFuture")
    public CompletableFuture<String> completableFuture() throws Exception {
        logger.info("completableFuture Start");
        logger.info("completableFuture end");
        return taskService.completableFuture(1000);
    }

    @Async
    public void async(int i){
        logger.info("in method : {}",i);

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