## Grouping

### 스트림 분할
1. 2분할 - partitioningBy()
```java
Map<Boolean, List<Student>> stuBySex = stuStream.collect(partitioningBy(Student::isMale))
List<Student> maleStudent = stuBysex.get(true);

Map<Boolean, List<Student>> stuBySex = stuStream.collect(partitioningBy(Student::isMale, counting()))
sysout(stuBySex.get(true)) // 남학생수

Map<Boolean, Optional<Student>> stuBySex = stuStream
                            .collect(partitioningBy(Student::isMale, maxBy(comparingint(Student::getScore))));
sysout(stuBySex.get(true)) // 남학생 1등 : Optional[[홍길동, 남, 1, 1, 300]]

Map<Boolean, Map<Boolean, List<Student>>> stuBySex = stuStream
                            .collect(partitioningBy(Student::isMale, 
                                        partitioningBy(s -> s.getScore() < 150)));
List<Student> failedMaleStu = stuBySex.get(true).get(true);
```

### 스트림의 그룹화
- groupingBy()

```java
Map<Integer, List<Student>> sutByClass = stuStream.collect(groupingBy(Student::getClass()));

Map<Integer, List<Student>> stuByGradeAndClass = stuStream.collect(groupingBy(Student::getGrade), groupingBy(Student.getClass));

Map<Integer, Map<Integer, Set<Student.Level>>> stuByGradeAndClass = stuStream.
        collect(
                groupingBy(Student::getGrade), groupingBy(Student.getClass,
                                                mapping(s -> {
                                                    if(s.getScore() > = 200) return High
                                                    else if(s.getScore() >= 100) return mid
                                                    else return low
                                                }, toSet())));

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