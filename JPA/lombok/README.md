1. @NorgsConstructor : 파라미터가 없는 기본 생성자 생성

2. @AllArgsConstructor : 모든 필드 값을 파라미터로 받는 생성자를 만들어 준다.

3. @RequiredArgsConstructor : final이나 @NonNull 인 필드 값만 파라미터로 받는 생성자를 만들어 준다.

4. @EqualsAndHashCode 
   - equals : 객체 주소 비교
   - hashCode : 두 객체의 내부값이 같은지 비교

5. @Data : @Getter, @Setter, @RequiredArgsConstructor, @ToString, @EqualsAndHashCode를 한번에 설정

6. @Builder : 생성자를 builder형태로 생성

7. @Transactional : 클래스나 메서드에 붙여 해당 범위 내 메서드가 처음부터 끝까지 원자성 보장

```java
@Test
	void test() {
		// setter 사용
		User u1 = new User();
		u1.setName("마리아");
		u1.setEmail("maria@example.com");
		u1.setCreatedAt(LocalDateTime.now());
		u1.setUpdatedAt(LocalDateTime.now());
		System.out.println("@ test u1: "+u1);
		
		// @AllArgsConstructor 생성자로 생성
		User u2 = new User("가나다", "ganada@example.com", LocalDateTime.now(), LocalDateTime.now());
		System.out.println("@ test u2: "+u2);
		
		// @NoArgsConstructor 생성자로 생성
		User u3 = new User();
		System.out.println("@ test u3: "+u3);
		
		// @Builder 로 생성
		User u4 = User.builder()
				.name("마바사")
				.email("mabasa")
				.updatedAt(LocalDateTime.now())
				.build();
		System.out.println("@ test u4: "+u4);
	}
```