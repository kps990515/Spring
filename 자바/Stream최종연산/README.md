## Stream최종연산

- forEach()
```java
IntStream.range(1,10).sequential().forEach(sysout);
IntStream.range(1,10).sequential().forEachOrdered(sysout);
IntStream.range(1,10).parallel().forEach(sysout); // 순서보장안됨
IntStream.range(1,10).parallel().forEachOrdered(sysout); //순서보장됨
```

- 조건검사
1. allMatch() : 모든 조건 만족
2. anyMatch() : 하나라도 만족
3. noneMatach() : 모든 조건 불만족
```java
boolean haFailed = stuStream.anyMatch(s -> s.getTotalScore()<=100);
```

- 조건일치요소찾기
1. findFirst() - 직렬
2. findAny() - 병렬
```java
Optional<Student> result = stuStream.filter(s -> s.getTotalScore()<=100).findFirst();
Optional<Student> result = stuStream.filter(s -> s.getTotalScore()<=100).findAny();
```

- reduce() : 요소 사용하면서 누적연산 수행
```java
// (초기값, 수행할 연산)
int count = intStream.reduce(0, (a,b) -> a+1);
int sum = intStream.reduce(0, (a,b) -> a+b);
int max = intStream.reduce(Integer.MIN_VALUE, (a,b) -> a > b ? a : b);
int min = intStream.reduce(Integer.MAX_VALUE, (a,b) -> a < b ? a : b);
```