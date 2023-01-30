## 스트림

- 다양한 데이터 소스(컬렉션, 배열)를 표준화된 방법으로 다루기 위한 것
- Collection, 배열을 받아서 Stream생성 -> 중간 연산 -> 최종연산 후 결과 리턴
- Stream은 데이터 소스로부터 읽기만 할 뿐 변경하지 않는다
- Stream은 일회용(= iterator)
- 최종 연산 전까지 중간연산은 실행되지 않는다(지연연산)
- 스트림은 작업을 내부 반복으로 처리한다(클래스 내부에 숨김)

```java
List<Integer> list = Arrays.asList(1,2,3,4,5);
Stream<Integer> intStream = list.stream(); // 컬렉션
List<Integer> sortedList = intStream.sorted() // List 정렬해서
                                .collect(Collectors.toList()); // 새로운 List에 저장

Stream<String> strStream = Stream.of(new String[]{"a","b","c"}); // 배열
Stream<Integer> evenStream = Stream.iterate(0, n->n+2); //0,2,4,6
Stream<Double> randomStream = Stream.generate(Math::random) // 람다식
IntStream intstream = new Random().ints(5); // 난수 5 스트림
```

- 중간연산
```java
String[] strArr = {"a","b","c"};
Stream<String> stream = Stream.of(strArr); // 문자열 배열이 소스인 스트림
Stream<String> filterdStream = Stream.filter(); // 걸러내기
Stream<String> distinctedStream = Stream.distinct(); // 중복제거
Stream<String> sortedStream = Stream.sort(); // 정렬
Stream<String> limitedStream = Stream.limit(5); // 자르기
int total = stream.count();
```

- 스트림은 병렬 처리
```java
Stream<String> strStream = Stream.of{"a","b","c"};
int sum = strStream.parallel() // 병렬스트림으로 전환
            .mapToInt(s -> s.length()).sum(); // 모든 문자열의 합
```

- 기본형 스트림(오토박싱&언박싱이 없어 속도 향상) : IntStream, LongStream, DoubleStream
- int -> Stream<Integer> -> int 가 없어짐
