### JPA Interface

```java
// JPA가 제공하는 find, save, update와 같은 함수 상속
// 왼쪽에는 연결한 Entitiy클래스, 오른쪽에는 PK 
public interface UserRepository extends JpaRepository<User, Long>{
}
```
1. findAll
```java
List<User> users = userReposritory.findAll(Sort.by(Direction.DESC, "name"));
users.forEach(System.out::println);
```
2. findAllById
```java
List<Long> ids;
ids.add(1L) 2L ...
List<User> users = userReposritory.findAllById(ids);
```
3. findById, getOne
```java
User user = userRepository.getOne(1L); //Lazy방식
Optional<User> user = userRepository.findById(1L);
User user = userRepository.findById(1L).orElse(null);
```
4. saveAll
```java
User user1 = new User(...)
User user2 = new User(...)
List<User> users = user1, user2...

userRepository.saveAll(users);
```

5. count
```java
long count = userRepository.count();
```

6. existsById
```java
boolean exist = userRepository.existsById(1L);
```

7. delete, deleteById
```java
userRepository.delete(userRepository.findById(1L).orElseThrow(RuntimeException::new));
userRepository.deleteById(1L);
```

8. deleteAll, deleteInBatch, deleteAllBatch(전체삭제)
```java
userRepository.deleteAll(userRepository.findAllById(list)); // deleteAll은 find->delete이기 때문에 부하발생
userRepository.deleteInBatch((userRepository.findAllById(list))) // find하지 않고 delete
userRepository.deleteAllBatch();
```

9. 페이징처리(size크기로 list개수 나눔, 페이지는 0부터)
```java
Page<User> users = userRepository.findAll(pageRequest.of(1,3)) //(page, size) 
```

10. QueryByExample
```java
ExampleMatcher matcher = ExampleMatcher.matching()
                    .withIgnoreePaths("name") // name컬럼은 무시
                    .withMatcher("email", endsWith()); // email 중에 OO으로 끝나는것만
Example<User> example = Example.of(new User("na", "naver.com"), matcher); // 네이버로 끝나는것만

userRepository.findAll(example);
```


```java
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Data
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper = true)
@Builder
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NonNull
    private String name;

    @NonNull
    private String email;

    private Gender gender;
```

