## 5장 - Filter Interceptor

![자료](https://github.com/kps990515/Spring/blob/main/5%EC%9E%A5/filter.png)

1. filter
- Client 요청/응답의 최초/최종단계 위치
- Web application에 등록
- Servelet Request, Response 객체 변환 가능

```java
@WebFilter(urlPatterns = "/api/*")
public class RequestFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        // request, reponse는 Controller에서 읽어야하기때문에 따로 캐싱 클래스 사용해서 캐싱 사용
        ContentCachingRequestWrapper wrappingRequest = new ContentCachingRequestWrapper((HttpServletRequest)request);
        ContentCachingResponseWrapper wrappingResponse = new ContentCachingResponseWrapper((HttpServletResponse) response);
        //filterChain에 
        chain.doFilter(wrappingRequest, wrappingResponse);

        System.out.println("---req ---");
        System.out.println(new String(wrappingRequest.getContentAsByteArray(),"UTF-8"));
        byte[] b = wrappingRequest.getContentAsByteArray();
        System.out.println("---req ---");

        System.out.println("---res ---");
        System.out.println(new String(wrappingResponse.getContentAsByteArray(),"UTF-8"));
        System.out.println("---res ---");
        //reponse내용을 읽어버려 reponse가 없기에 다시 response에 복사해주기
        wrappingResponse.copyBodyToResponse();
    }
}
```

2. Interceptor
- Filter, AOP 와 유사
- Spring Context에 등록
- 인증단계, 로깅

```java
@Component
public class AuthInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        boolean validAuthUser = checkValidAccessAnnotation(handler, AuthUser.class);
        System.out.println("annotation check : "+validAuthUser);
        if(validAuthUser){
            return true;
        }

        return false;
    }

    private boolean checkValidAccessAnnotation(Object handler, Class clazz) {

        if(handler instanceof ResourceHttpRequestHandler){
            System.out.println("리소스 요청 class "+clazz.getName());
            return true;
        }

        HandlerMethod handlerMethod = (HandlerMethod) handler;
        if(null != handlerMethod.getMethodAnnotation(clazz) || null != handlerMethod.getBeanType().getAnnotation(clazz)){
            System.out.println("어노테이션 체크 class "+clazz.getName());
            return true;
        }

        return false;
    }
}
```


```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ ElementType.TYPE, ElementType.METHOD })
public @interface AuthUser {
}
```

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {

    private static final String[] DEFAULT_EXCLUDE_PATH = {
            "/api/ping",
    };

    private final AuthInterceptor authInterceptor;

    public MvcConfig(AuthInterceptor authInterceptor) {
        this.authInterceptor = authInterceptor;
    }



    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(authInterceptor);
    }
}
```
