## Collect
- collect()는 Collector를 매개변수로 하는 스트림의 최종연산

### 스트림 컬렉션, 배열로 변환
- toList(), toSet(), toMap(), toCollection()
```java
List<String> names = stuStream.map(Student::getName).collect(Collectors.toList());
ArrayList<String> list = names.stream().collect(Collectors.toCollection(ArrayList::New));
Map<String, Person> map = personStream.collect(Collectors.toMap(p->p.getRegId(), p->p));
Student[] stuNames = studentStream.toArray(Student[]::new);
Object[] stuNames = studentStream.toArray();
```

### 스트림의 통계정보 제공
- counting(), summingInt(), maxBy(), minBy()...
```java
long count = stuStream.count(); // 전체 카운트
long count = stuStream.collect(counting()); //그룹별 카운트 가능

long totalScore = stuStream.mapToInt(Student::getTotalScore).sum(); // 전체 합계
long totalScore = stuStream.collect(summingInt(Student::getTotalScore)); // 그룹별 합계 가능

OptionalInt topScore = stuStream.mapToInt(Student::getTotalScore).max();
Optional<Student> topStudent = stuStream.max(Comparator.comparingInt(Student::getTotalScore));
Optional<Student> topStudent = stuStream.collect(maxBy(Comparator.comparingInt(Student::getTotalScore)));
```

### 스트림 리듀싱(reducing())
```java
OptionalInt max = intStream.reduce(Integer::max);
Optional<Integer> max = intStream.boxed().collect(reducing(Integer::max));
```

### 문자열 스트림 연결(joining())
```java
String stuNames = stuStream.map(Student::getName).collect(joining(","));
```