## 메서드 참조

- 하나의 메서드만 호출하는 람다식은 메서드 참조로 간단히 할 수 있다

1. static메서드 참조
```java
//람다
(x) -> ClassName.method(x)  
//메서드 참조
ClassName::method
```
```java
//static 메서드
Integer method(String s){
    return Intger.parseInt(s);
}
// 람다식
Function<String, Integer> f = (String s) -> Integer.parseInt(s);

//메서드 참조
Function<String, Integer> f = Integer::parseInt;
```

2. 생성자의 메서드 참조

```java
//람다식
Supplier<MyClass> s = () -> new MyClass();
//메서드 참조
Supplier<MyClass> s = MyClass::new;
Myclass mc = s.get();
```
```java
//람다식
Function<Integer, MyClass> s = (i) -> new Myclass(i);
//메서드 참조
Function<Integer, MyClass> s = MyClass::new;
Myclass mc = s.apply(100);
```

3. 배열과 메서드 참조
```java
//람다식
Function<Integer, int[]> f = (x) -> new int[x];
//메서드 참조
Function<Integer, int[]> f2 = int[]::new;
```



1. 메서드 이름과 반환타입을 제거하고 -> 블록 앞에 추가
2. 반환값이 있는 경우 식, 값만 적고 return문 생략가능(; 안붙임)
3. 매개변수 타입 추론가능하면 생략 가능
4. 매개변수가 하나인 경우 () 생략가능
5. 블록 내 문장이 하나인 경우 {} 생략가능
```java
int max(int a, int b){
        return a > b ? a : b;
}

(a, b) -> a > b ? a: b
a -> sysout(a)
```