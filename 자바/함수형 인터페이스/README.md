## 함수형 인터페이스

- 단 하나의 추상 메서드만 선언된 인터페이스
- 람다식을 다루기 위한 참조변수 타입은 함수형 인터페이스

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

