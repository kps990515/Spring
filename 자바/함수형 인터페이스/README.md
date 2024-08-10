## 함수형 인터페이스

1. 정의
- 자바 8에서 도입된 개념으로, **단일 추상 메서드(Single Abstract Method, SAM)**만을 가지는 인터페이스를 의미
- 람다식을 사용해 인터페이스 구현 가능

2. 종류

| **함수형 인터페이스** | **메서드 형태** | **API 활용**                                      | **매개변수** | **반환값** |
|------------------------|-----------------|---------------------------------------------------|--------------|------------|
| **Runnable**            | `void run()`     | 매개 변수를 사용 안하고 리턴을 하지 않는 함수 형태로 이용, 대표적으로 쓰레드의 매개 변수로 이용 | X            | X          |
| **Consumer<T>**         | `void accept(T t)`| 매개 변수를 사용만 하고 리턴을 하지 않는 함수 형태로 이용 | O            | X          |
| **Supplier<T>**         | `T get()`        | 매개 변수를 사용 안하고 리턴만 하는 함수 형태로 이용 | X            | O          |
| **Function<T, R>**      | `R apply(T t)`   | 매개값을 매핑(=타입변환)해서 리턴하기               | O            | O          |
| **Predicate<T>**        | `boolean test(T t)` | 매개값이 조건에 맞는지 단정해서 boolean 리턴       | O            | O          |
| **Operator**            | `R apply(T t)`   | 매개값을 연산해서 결과 리턴하기                    | O            | O          |

3. 예시
```java
public class LambdaRunnableExample {

    public static void main(String[] args) {
        // Runnable을 람다식으로 구현
        Runnable runnable = () -> System.out.println("Running in a lambda expression");

        // Runnable을 사용하는 스레드 생성 및 실행
        Thread thread = new Thread(runnable); // Thread(Ruunable Target)으로 클래스 정의가 되어있어 람다로 구현된 함수형 인터페이스 사용 가능
        thread.start();

        // 메인 스레드의 종료 메시지
        System.out.println("Main thread finished");
    }
}
```


```java
@FunctionalInterface
interface MyFunction{
    public abstract int max(int a, int b);
}
```

```java
MyFunction f = new MyFunction(){
    public int max(int a, int b){
        return a > b ? a: b;
    }
}
// 위와 같은거
MyFunction f = (a,b) -> a > b ? a:b

// 에러 안남
int max = f.max(a,b)
```
