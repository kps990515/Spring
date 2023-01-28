## java.util.function 패키지

![자료](https://github.com/kps990515/Spring/blob/main/%EC%9E%90%EB%B0%94/javaUtilFunction/function.png)

```java
Supplier<Integer> s = () -> (int)(Math.random()*100) + 1
static <T> void makeRandomList(Supplier<T> s, List<T> list){
    for(int i=0;i<10;i++){
        list.add(s.get());
    }
}

makeRandomList(s, list);
```

```java
Consumer<Integer> c = i -> sysout(i)
Predicate<String> p = s -> s.length() == 0;

static <T> void printEvenNum(Predicate<T> p, Consumer<T> c, List<T> list){
    for(T i : list){
        if(p.test(i)) c.accept
    }
}

printEvenNum(p, c, list);
```

```java
Function<Integer, Integer> f = i -> i % 2 ==0;
static <T> List<T> doSomething(Function<T, T> f, List<T> list){
    List<T> newList = new ArrayList<T>(list.size());
    for(T i : List){
        newList.add(f.apply(i));
    }
    return newList
}

List<Integer> newList = doSomething(f, list);

```

```java
Predicate<String> isEmptyStr = s -> s.length() == 0;
String s = "";

if(isEmptyStr.test(s)){sysout(...)}
```

![자료](https://github.com/kps990515/Spring/blob/main/%EC%9E%90%EB%B0%94/javaUtilFunction/bifunction.png)
```java
@FunctionalInterface
interface TriFunction<T,U,V,R>{
    R apply(T t, U u, V v);
}
```

![자료](https://github.com/kps990515/Spring/blob/main/%EC%9E%90%EB%B0%94/javaUtilFunction/unaryfunction.png)
```java
@FunctionalInterface
interface UnaryOperator<T> extends Function<T,T>{
    static <T> UnaryOperator<T> identity(){
        return t -> t;
    }
}
```

