## 인터페이스 결합, Collection 함수형인터페이스

- 인터페이스의 메소드
 1. default메소드 - 하위호환성, 유연성위함(새로운 인터페이스 기능 추가 시 default사용하면 재정의안해줘도됨)
 ```java
    default int multiple(int i, int j){
        return i*j;
    }
 ```
 2. static메소드 - 인터페이스 new안해도 인터페이스명.함수명으로 사용가능
 ```java
    public static int multiple(int i, int j){
        return i * j;
    }
 ```
 3. 추상메소드

### 두개 predicate를 결합(default메소드)
```java
Predicate<Integer> p = i -> i< 100;
Predicate<Integer> q = i -> i< 200;
Predicate<Integer> r = i -> i%2 == 0;

Predicate<Integer> notP = p.negate(); // i >= 100
Predicate<Integer> all = notP.and(q).or(r); // i >= 100 && i < 200 || i%2==0
Predicate<Integer> all2 = notP.and(q.or(r)); // i >= 100 && (i < 200 || i%2==0)
```

### isEqual비교를 위해서는 isEqual()사용 후 test 사용(static메소드)

```java
Predicate<Integer> p = Predicate.isEqual(str1);
Boolean result = p.test(str2);
```

### 인터페이스 결합
```java
Function<String, Integer> f = (s) -> Integer.parseInt(s, 16);
Function<Integer, String> g = (i) -> Intger.toBinaryString(i);

// f 다음에 g
Function<String, String> h = f.andThen(g)
// g 다음에 f
Function<Integer, Integer> h2 = f.compose(g)
```

### 함수형 인터페이스 사용한 Collenction
![자료](https://github.com/kps990515/Spring/blob/main/%EC%9E%90%EB%B0%94/predicate%EA%B2%B0%ED%95%A9/collection.png)

```java
list.forEach(i -> sysout(i));
map.forEach((k,v) -> sysout(k + v));
list.removeIf(x -> x%2==0 || x%3==0) // 2,3 배수 리스트에서 제거
list.replaceAll(i -> i*10); // 모든 요소에 10 곱하기
```