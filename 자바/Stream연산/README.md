## 스트림연산

### 중간연산
```java
String[] strArr = {"a","b","c"};
Stream<String> stream = Stream.of(strArr); // 문자열 배열이 소스인 스트림
Stream<String> filterdStream = Stream.filter(); // 걸러내기
Stream<String> distinctedStream = Stream.distinct(); // 중복제거
Stream<String> sortedStream = Stream.sort(); // 정렬
Stream<String> limitedStream = Stream.limit(5); // 자르기
int total = stream.count();
```

1. distinct : 중복제거
2. filter : 조건안맞는 요소 제외
3. limit : 스트림 일부 자르기
4. skip : 스트림 일부 건너뛰기
5. peek : 스트림 요소를 소비하지 않고 보기
```java
//파일스트림에서 파일 확장자(대문자)를 중복없이 뽑아내기
fileStream.map(File::getName)
    .filter(s->s.indexOf('.')!=-1) // 확장자 없는 것 제외
    .peek(s->sysout("filename=%s%n", s)) // 파일명 출력
    .map(s->s.substring(s.indexOf('.')+1)) // 확장자만 뽑아내기
    .peek(s->sysout("extension=%s%n", s)) // 확장자 출력
    .map(String::toUpperCase) //대문자 변환
    .distinct(); // 중복제거
```
```
6. sorted : 정렬 / Comparator(Comparable)을 조건으로 넣어서 사용 / 여러개할때는 .thenComparing
```java
// 기본정렬
strStream.sorted();
strStream.sorted(Comparator.naturalOrder());
strStream.sorted((s1, s2) -> s1.compareTo(s2));
strStream.sorted(String::compareTo);

//역순 정렬
strStream.sorted(Comparator.reverseOrder());
strStream.sorted(Comparator.<String>naturalOrder.reverse());

//길이순 정렬
strStream.sorted(Comparator.comparing(String::length)));
strStream.sorted(Comparator.comparingInt(String::length))); //no 오토박싱

//클래스 요소 정렬(클래스에 Comparable 구현 필요)
studentStream.sorted(Comparator.comparing(Student::getClass)) // 반별로 정렬
                .thenComparing(Student::getScore)
```
7. map, mapToInt, flatMap, flatMapToDouble... : 스트림 요소 변환
```java
Stream<String> fileNameStream = fileStream.map(File::getName); //파일이름들 스트링으로 변환

//파일스트림에서 파일 확장자(대문자)를 중복없이 뽑아내기
fileStream.map(File::getName)
    .filter(s->s.indexOf('.')!=-1) // 확장자 없는 것 제외
    .map(s->s.substring(s.indexOf('.')+1)) // 확장자만 뽑아내기
    .map(String::toUpperCase) //대문자 변환
    .distinct(); // 중복제거
```

![자료](https://github.com/kps990515/Spring/blob/main/%EC%9E%90%EB%B0%94/predicate%EA%B2%B0%ED%95%A9/collection.png)

### 최종연산

1. forEach, forEachOrdered : 각 요소 지정된 작업 수행
2. count : 개수 반환
3. max, min
4. findAny(병렬 + filter), findFirst
5. allMatch, anyMatch(하나라도 만족), noneMatch
6. toArray
7. reduce : 요소를 줄여가면서(하나씩 꺼내면서) 계산
8. collect : 요소 수집 > 그룹화, 컬렉션담기

