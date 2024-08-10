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

### 익명객체를 람다식으로 대체
```java
Collections.sort(list, new Comparator<String>(){
                            public int compare(String s1, String s2){
                                return s2.compareTo(s1);
                            }    
                        })

//람다식으로하면
Collections.sort(list, (s1, s2) -> s2.compareTo(s1));                        
```

함수형 인터페이스 타입의 매개변수, 반환타입
```java
void aMethod(MyFunction f){
    f.myMethod();
}

MyFunction f = () -> sysout("myMethod()");
aMethod(f);

// 줄여쓰면
aMethod(() -> sysout("myMethod()"))
```

### 함수형 인터페이스 타입의 반환타입
```java
MyFunction myMethod(){
    MyFunction f = () -> {};
    return f;
}

//줄여쓰면
MyFunction myMethod() {
    return () -> {};
}
```

