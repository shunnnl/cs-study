> **📅 업로드 날짜**  
> 2025-08-29
> 
> **🗂 분류**  
> web  
>
> **🔗 노션 링크**  
> [노션에서 보기](https://important-marquess-d42.notion.site/JPA-25ba654e658a80039dbbd83a3deaae08)
>
# JPA

- **Java Persistence API**
- 자바 객체와 데이터베이스 테이블을 매핑(ORM)해주는 표준 인터페이스
- 구현체: Hibernate, EclipseLink 등 (Spring Boot는 Hibernate 기본 사용)

## **❓왜 쓰는가?**

- JDBC는 SQL을 직접 작성해야 함
- MyBatis는 SQL 작성은 편리하지만 여전히 SQL 중심
- JPA는 **객체 중심**으로 개발 → CRUD 단순화, 유지보수 ↑
- **예시**
    
    ### 🔹 JDBC 예시 (SQL 직접 작성 + ResultSet 매핑 필요)
    
    ```java
    String sql = "SELECT id, name FROM users WHERE id = ?";
    PreparedStatement ps = conn.prepareStatement(sql);
    ps.setLong(1, 1L);
    
    ResultSet rs = ps.executeQuery();
    User user = null;
    
    if (rs.next()) {
        user = new User();
        user.setId(rs.getLong("id"));
        user.setName(rs.getString("name"));
    }
    ```
    
    - SQL 직접 작성
    - ResultSet → 객체 변환을 수동으로 해야 함
    
    ### 🔹 MyBatis 예시 (SQL Mapper 기반, 매핑 자동)
    
    **UserMapper.xml**
    
    ```xml
    <select id="findById" parameterType="long" resultType="User">
        SELECT id, name FROM users WHERE id = #{id}
    </select>
    ```
    
    **UserMapper.java**
    
    ```java
    User user = userMapper.findById(1L);
    ```
    
    - SQL은 여전히 직접 작성해야 함
    - 객체 매핑은 프레임워크가 처리해 줌
    
    ### 🔹 JPA 예시 (객체 중심 개발)
    
    ```java
    User user = em.find(User.class, 1L);
    // 또는 Spring Data JPA 사용 시
    User user = userRepository.findById(1L).orElseThrow();
    ```
    
    - SQL 작성 불필요
    - `findById()` 같은 메서드 호출만으로 DB 접근
    - 객체 중심 개발 → 유지보수성 ↑

**장점**

- 생산성: `save()`, `findById()` 등 메서드 호출만으로 DB 작업
- 객체지향: 연관관계 매핑으로 객체 그래프 탐색 가능
- 캐싱: 영속성 컨텍스트로 성능 최적화

**단점**

- 러닝 커브 (매핑, 영속성 이해 필요)
- 성능 튜닝 필요 (N+1, 쿼리 최적화)

## 주요 개념

### 1) 엔티티 매핑

```java
@Entity
@Table(name = "users")
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 50)
    private String name;
}
```

> 
> 
> - `@Entity` : 이 클래스가 JPA에서 관리되는 **엔티티 객체**임을 표시
> - `@Table(name = "users")` : 이 엔티티가 DB의 **users 테이블**과 매핑됨
> - `@Id` : **기본 키(PK)** 지정
> - `@GeneratedValue(strategy = GenerationType.IDENTITY)` : 기본 키 생성 전략 (DB 자동 증가)
> - `@Column(nullable = false, length = 50)` : 컬럼 속성 지정 (NOT NULL, 길이 50 제한)

### 2)연관관계 매핑

```java
@Entity
public class Post {
    @Id @GeneratedValue
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)   // 다대일
    @JoinColumn(name = "user_id")
    private User user;
}
```

> 
> 
> - `@ManyToOne` : 다대일(N:1) 관계 설정 → Post 입장에서 User는 하나, User입장에서 Post는 여러 개
> - `fetch = FetchType.LAZY` : 지연 로딩 → User가 실제로 필요할 때 쿼리 실행
>     - **📌 LAZY vs EAGER 실행 흐름(게시글(Post)만 필요**하고, 작성자(User) 정보는 필요 없는 상황**)**
>         
>         
>         
>         **LAZY (지연 로딩) 흐름 → SQL 2번 (Post, User 따로)**
>         
>         ```mermaid
>         sequenceDiagram
>             participant App as 애플리케이션
>             participant DB as 데이터베이스
>         
>             App->>DB: SELECT * FROM post WHERE id=1
>             DB-->>App: Post 객체 반환
>             Note over App: User는 아직 안 불러옴 (Proxy 상태)
>         
>             App->>DB: (post.getUser() 호출 시) <br/>SELECT * FROM user WHERE id=?
>             DB-->>App: User 객체 반환
>             Note over App: 실제로 필요할 때 User 쿼리 실행
>         
>         ```
>         
>         **EAGER (즉시 로딩) 흐름 → → SQL 1번 (JOIN)**
>         
>         ```mermaid
>         sequenceDiagram
>             participant App as 애플리케이션
>             participant DB as 데이터베이스
>         
>             App->>DB: SELECT p.*, u.* <br/>FROM post p JOIN user u ON p.user_id = u.id
>             DB-->>App: Post + User 객체 반환
>             Note over App: User를 쓰든 안 쓰든 무조건 같이 가져옴
>         
>         ```
>         
>         
> - `@JoinColumn(name = "user_id")` : 외래 키(FK) 매핑, DB 컬럼명 `user_id`와 연결
> - **양방향 매핑 시 주인(owner)** : 외래 키(FK)를 가진 쪽(Post)이 주인
>     - **외래 키(FK)** : `post.user_id`
>     - **주인(owner)** : FK를 가진 `Post.user`
>     - **비주인(mappedBy)** : `User.posts` (조회 전용)
>     - `post.setUser(userB)` → DB에서 `post.user_id = userB.id` 로 UPDATE
>     
>     ```mermaid
>     erDiagram
>         USER ||--o{ POST : "1:N 관계"
>         POST {
>             BIGINT id PK
>             BIGINT user_id FK
>             STRING title
>         }
>         USER {
>             BIGINT id PK
>             STRING name
>         }
>     
>     ```
>     

### 3) 영속성 컨텍스트

- JPA가 엔티티를 1차 캐시에 보관 → 동일 트랜잭션 내에서는 같은 객체 반환
    
    <aside>
    
    - **트랜잭션** = DB 작업을 묶은 단위 (시작 ~ 끝)
        
        예: “게시글 저장하기”라는 기능 하나가 트랜잭션 하나
        
    - **영속성 컨텍스트**는 트랜잭션 안에서 살아있음
        
        → 같은 트랜잭션에서 같은 데이터를 다시 조회하면 DB 쿼리 안 나가고, **1차 캐시(영속성 컨텍스트)에서 바로 가져옴**
        
    
    ```java
    @Transactional
    public void example() {
        User u1 = em.find(User.class, 1L); // DB에서 조회
        User u2 = em.find(User.class, 1L); // 캐시에서 조회 (쿼리 안 나감)
        System.out.println(u1 == u2);      // true (같은 객체 반환)
    }
    ```
    
    ✔️ 정리:
    
    **“같은 트랜잭션 = 같은 영속성 컨텍스트를 공유”**
    
    → 캐시 덕분에 동일 객체 보장 & 쿼리 최소화
    
    </aside>
    
- Dirty Checking: 엔티티 변경 → flush 시점에 UPDATE SQL 자동 실행
    - JPA는 **관리 중(managed)인 엔티티**의 원본 상태를 1차 캐시에 저장해둠
    - 개발자가 엔티티 값을 바꾸면 JPA가 **원본 vs 변경된 값**을 비교
    - 트랜잭션 끝날 때 자동으로 UPDATE SQL 실행
    
    ```java
    @Transactional
    public void updateUser() {
        User user = em.find(User.class, 1L); // SELECT 실행
        user.setName("홍길동");              // 값만 변경
        // UPDATE는 직접 안 써도 됨
    } // 트랜잭션 종료 시점에 UPDATE SQL 실행
    ```
    
- 엔티티 생명주기: new → managed → detached → removed
    - **new (비영속)**: `new User()` 한 상태 (JPA랑 상관 없음)
    - **managed (영속)**: `em.persist(user)` 실행 → JPA가 관리 시작 (1차 캐시에 올라감)
    - **detached (준영속)**: 관리에서 분리된 상태 → `em.detach(user)`, `em.close()` 등
    - **removed (삭제)**: `em.remove(user)` 호출 → 삭제 예약
    
>    - **비영속(new)**: 아직 회원가입 안 한 상태
>    - **영속(managed)**: 회원가입 완료 → DB에 기록되고 관리됨
>    - **준영속(detached)**: 회원 탈퇴했지만 데이터는 남아있음, 더 이상 관리 안 됨
>    - **삭제(removed)**: 진짜로 DB에서 삭제됨
    

## 문제

### 1) N+1 문제

- 상황: Post 조회 시 연관된 User를 LAZY 로딩하면 User 조회 쿼리가 추가 실행 (ex. 학생 30명의 **성적표만 먼저 가져오고**각 학생의 **이름을 따로따로 DB에 물어보는 상황**→ 왕복이 너무 많아서 느려짐)
- 결과: `1번(전체 조회) + N번(연관 엔티티)` → 성능 저하

**해결 방법**

```java
// JPQL fetch join
@Query("SELECT p FROM Post p JOIN FETCH p.user")
List<Post> findAllWithUser();
```

- Fetch Join, `@EntityGraph`, `hibernate.default_batch_fetch_size`

### 2) EAGER 로딩 주의

- 즉시 로딩(EAGER)은 모든 연관 객체를 무조건 조인
- 불필요한 데이터까지 불러와 성능 저하
- 실무에서는 대부분 **LAZY**를 기본으로 사용

## Spring Data JPA 활용

- JPA를 더 편리하게 사용하는 모듈
- Repository 인터페이스만 정의하면 **구현체 자동 생성 →** DAO/Repository 클래스를 직접 작성할 필요가 없음

```java
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByName(String name);
}
```

### 1) `JpaRepository<User, Long>`

- `User` : 어떤 엔티티를 다룰지 지정
- `Long` : PK 타입 지정
- `JpaRepository`를 상속하면 기본 CRUD 메서드를 자동 제공
    - `findAll()`, `findById()`, `save()`, `delete()` …

👉 개발자가 따로 메서드 구현할 필요 없음

### 2) 메서드 이름 기반 쿼리

```java
List<User> findByName(String name);
```

- **메서드 이름을 해석해서 자동으로 쿼리 생성**
    - `findByName("홍길동")` →
        
        `SELECT * FROM users WHERE name = '홍길동'` 실행
        
- 다른 예시:
    - `findByAgeGreaterThan(int age)` → 나이 큰 사용자 조회
    - `findByEmailContaining(String keyword)` → 이메일 LIKE 검색

### 3) @Query (JPQL 작성)

```java
@Query("SELECT u FROM User u WHERE u.name = :name")
List<User> findUserByName(@Param("name") String name);
```

- 직접 JPQL을 작성해서 원하는 쿼리 실행 가능

### 4) Native Query

```java
@Query(value = "SELECT * FROM users WHERE name = :name", nativeQuery = true)
List<User> findNativeUserByName(@Param("name") String name);
```

- **실제 DB SQL**을 직접 작성할 수도 있음 (DB 종속적일 때 사용)
