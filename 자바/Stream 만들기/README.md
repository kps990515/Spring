## 스트림만들기

- Collection
```java
List<Integer> list = Arrays.asList(1,2,3,4,5);
Stream<Integer> intStream = list.stream(); // 컬렉션
intStream.foreach(람다식);
```

- 배열
```java
Stream<String> stream = Stream.of("a","b","c"); 
Stream<String> stream = Stream.of(new String[]{"a","b","c"}); 
Stream<String> stream = Arrays.stream(new String[]{"a","b","c"}); 
Stream<String> stream = Arrays.stream(new String[]{"a","b","c"}, 0, 3); // 0<= <3
```

- 난수 스트림
```java
IntStream intstream = new Random().ints(5); // 난수 5 스트림
```

- 특정 범위 정수 
```java
IntStream intstream = IntStream.range(1,5); //1,2,3,4
IntStream intStream = IntStream.rangeClosed(1,5) //1,2,3,4,5
```

- 람다 iterate(), generate()
```java
Stream<Integer> evenStream = Stream.iterate(0, n->n+2);
DoubleStream doubleStream = Stream.generate(Math::random);
IntegerStream intStream = Stream.generate(() -> 1); // 1 1 1 1...
```

- 파일 읽기
```java
Stream<Path> fileListStream = Files.list(Path dir);
Stream<String> readFileByLine = Files.lines(Path path);
Stream<String> bufferedReaderStream = lines();
```

- 빈 스트림
```java
Stream emptyStream = Stream.empty();
```
