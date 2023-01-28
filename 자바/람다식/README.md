## 람다식

- 람다식은 익명 함수가 아닌 익명객체
```java
Object object = new Object(){
    int max(int a, int b){
        return a > b ? a : b;
    }
}

//하지만 이렇게 하면 에러
int max = obj.max(a,b);
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