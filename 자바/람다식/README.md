## 람다식

1. 람다식의 정의
 - 람다식은 자바 8에서 도입된 기능으로, 익명객체를 생성해서 익명 함수를 표현하는 간결한 방식
 - 람다식을 사용하면 불필요한 보일러플레이트 코드를 줄이고, 함수형 프로그래밍 스타일 사용 가능
 
2. 람다식의 특징
 - 람다식은 메서드 이름이나 반환 타입을 명시하지 않고도, 함수를 표현가능.
 - 함수형 인터페이스를 구현할 때 주로 사용
```java
Comparator<Integer> comparator = (a, b) -> a.compareTo(b);
```

3. 람다식의 장점
 - 코드가 간결, 가독성이 향상
 - 컬렉션 API에서 Stream을 사용해 데이터 처리를 할 때, 람다식은 매우 유용하게 사용(리스트를 필터링하거나 매핑)

4. 람다식의 예제와 활용
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
numbers.stream()
       .filter(n -> n % 2 == 0)
       .forEach(System.out::println);
```       
5. 람다식의 한계 및 주의사항
 - 너무 복잡한 로직을 람다식으로 표현하면 오히려 가독성이 떨어질 수 있습니다
 - 익명 클래스와 달리 상태를 가지지 않기 때문에, 복잡한 상태 관리가 필요한 경우에는 적합하지 않을 수 있습니다.

## 익명 클래스, 객체

1. 익명 클래스
 - 이름이 없는 클래스
 - 클래스를 정의하고 그 인스턴스를 동시에 생성
 - 인터페이스를 구현하거나, 추상 클래스를 상속받을 때 사용
```java
Runnable runnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running in an anonymous class");
    }
};
```

2. 익명객체
 - 익명 클래스의 인스턴스(runnable)
 - 보통 익명 클래스를 통해 생성
