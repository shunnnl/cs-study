> **📅 업로드 날짜**  
> 2025-08-29
>  
> **🗂 분류**  
> web  
>
> **🔗 노션 링크**  
> [노션에서 보기](https://important-marquess-d42.notion.site/Spring-Boot-Test-Code-25ba654e658a80469894c63a16b7962d?source=copy_link)
> 


# Test Code

- 말그대로 테스트하는 코드

## TDD(Test-Driven Development)

- “**테스트 주도 개발”** 이라고 한다.
- 반복 테스트를 이용한 소프트웨어 방법론으로,
- 작은 단위의 테스트 케이스를 작성하고 이를 통과하는 코드를 추가하는 단계를 반복하여 구현한다.
- 보통 **Service** 단을 많이 쪼개서 ****유닛 테스트를 하는데, 진짜 DB에 접근하지 않고 Mock으로 처리한다
- TDD의 대표적인 Tool '**JUnit**'

### 왜 할까?

- 테스트 코드가 없다면
    
    1. 코드를 작성한 뒤 프로그램을 실행하여 서버를 킨다.
    2. API 프로그램(ex. Postman)으로 HTTP 요청 후 결과를 Print로 찍어서 확인한다.
    3. 결과가 예상과 다르면, 다시 프로그램을 종료한 뒤 코드를 수정하고 반복한다.
    
- 위와 같은 방식이 얼마나 반복될 지 모른다. 그리고 하나의 기능마다 저렇게 테스트를 하면 서버를 키고 끄는 작업 또한 너무 비효율적이다.

**테스트 코드가 없는 경우**

- A 기능 개발 후 → 정상 동작 확인
- B 기능을 수정하다가, 실수로 A 기능 관련 로직까지 건드려버림
- → Swagger나 수동 테스트로는 “B 기능만” 확인
- → A 기능이 깨졌는지 모르고 배포할 수 있음

**테스트 코드가 있는 경우**

- A 기능 테스트 코드, B 기능 테스트 코드 모두 존재
- B 기능을 수정 후 `./gradlew test` 실행
- → A 테스트, B 테스트가 동시에 실행
- → 만약 B 수정 중에 A가 깨졌다면 **A 테스트가 실패**해서 바로 알 수 있음

### TDD 개발절차
<img width="500" height="500" alt="image (25)" src="https://github.com/user-attachments/assets/8b656c95-0862-49cf-89ca-e0dc25c9df6c" />


1. **Red**단계에서는 **실패하는 테스트 코드**를 먼저 작성한다.
2. **Green**단계에서는 테스트 코드를 **성공시키기 위한 실제 코드**를 ****작성한다.
3. **Blue**단계에서는 중복 코드 제거, 일반화 등의 **리팩토링**을 ****수행한다.

- 중요한 것은 **실패하는 테스트 코드**를 작성할 때까지 **실제 코드를 작성하지 않는** 것과, 실패하는 테스트를 통과할 정도의 **최소 실제 코드를 작성**해야 하는 것이다.
- 이를 통해, 실제 코드에 대해 기대되는 바를 보다 명확하게 정의함으로써 **불필요한 설계를 피할 수있고**, **정확한 요구 사항에 집중**할 수 있다.

### Given–When–Then (GWT) 패턴

- **Given (준비)**: 테스트를 시작하기 위한 상태나 입력값을 준비
- **When (행위)**: 실제로 테스트 대상이 되는 동작을 실행
- **Then (검증)**: 실행 결과가 기대한 대로 나왔는지 확인

```java
//예외처리 테스트
@Test
void register_whenDuplicateNickname_thenThrowException() {
    // Given
    String nickname = "DONGUK";
    // 닉네임이 이미 존재한다고 가정 (Mock 설정)
    when(userRepository.existsByNickname(nickname)).thenReturn(true);

    // When
    Executable action = () -> userService.register(new UserSignupCommand(nickname, "pw1234"));

    // Then
    DuplicateNicknameException exception =
        assertThrows(DuplicateNicknameException.class, action);

//assertThrows는 action을 실행하면서, 해당 코드 블록에서 예외가 발생하는지 확인
    assertThat(exception.getMessage()).isEqualTo("이미 존재하는 닉네임입니다.");
}
 
// 정상처리 테스트
@Test
void register_whenNewNickname_thenSaveUser() {
    // Given
    String nickname = "newUser";
    String password = "pw1234";
    when(userRepository.existsByNickname(nickname)).thenReturn(false);
    when(userRepository.save(any(User.class)))
        .thenReturn(new User(1, nickname, password));

    // When
    Long userId = userService.register(new UserSignupCommand(nickname, password));

    // Then
    assertThat(userId).isEqualTo(1);              // ID가 정상적으로 반환되는지 검증
    verify(userRepository).save(any(User.class));  // userRepository.save()가 호출됐는지 검증
}
```

```jsx
when(userRepository.existsByNickname(nickname)).thenReturn(true);
// userRepository.existsByNickname("DONGUK") 호출이 오면 **진짜 DB 조회를 하지 않고
//** 대신 true를 반환하라.
```
