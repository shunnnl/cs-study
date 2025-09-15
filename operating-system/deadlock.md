> **📅 업로드 날짜**  
> 2025-09-15 
>
> **🗂 분류**  
> Operating System
>
> **🔗 노션 링크**  
> [노션에서 보기](https://important-marquess-d42.notion.site/DeadLock-262a654e658a80818365f8c42d9696ee?source=copy_link)

# 데드락(DeadLock)

> 두 개 이상의 프로세스(또는 트랜잭션)가 자원을 서로 점유한 채, 상대방 자원을 기다리며 **무한 대기 상태**에 빠지는 현상
> 

<img width="946" height="813" alt="image" src="https://github.com/user-attachments/assets/f9e867ab-82b9-4683-865f-108ac39b5ec0" />


→ 시스템 전체 성능 저하 및 서비스 장애 원인

## 발생 조건

- 다음 **4가지 조건**이 동시에 성립할 때 발생

>  - 자원: 프로그램(또는 프로세스)이 실행되면서 필요로 하는 **도구나 재료**
>  - 프로세스: 실행 중인 프로그램
>    
>   ex. 프로세스(학생), 자원(도서관의 프린터)
>
>- 상황:
>    - 학생 A(프로세스)는 프린터를 점유하고 책상도 필요함
>    - 학생 B(프로세스)는 책상을 점유하고 프린터도 필요함
>    - 서로 상대방이 가진 자원을 기다리며 아무도 작업을 못 함 → **데드락**

1. **상호 배제 (Mutual Exclusion)**
    - 자원은 동시에 한 프로세스만 사용 가능
    - 사용 중인 자원을 다른 프로세스가 사용하려면 요청한 자원이 해제될 때까지 기다려야 한다.
2. **점유와 대기 (Hold and Wait)**
    - 자원을 가진 상태에서 다른 자원을 요청하며 대기
3. **비선점 (No Preemption)**
    - 강제로 자원을 빼앗을 수 없음
4. **환형 대기 (Circular Wait)**
    - 프로세스들이 원형으로 자원을 기다리는 관계 형성
    
    ```mermaid
    graph TD
        P1 --> R1
        R1 --> P2
        P2 --> R2
        R2 --> P1
    
    ```
    

⇒ 혼자만 쓸 수 있고(상호 배제) → 이미 사용 중이면서(점유와 대기) → 빼앗을 수 없고(비선점) → 서로가 서로를 기다리는 원형 구조(환형 대기)가 동시에 만족하면 교착 상태가 된다.

## 처리 방법

- 운영체제(OS)나 DBMS에서는 크게 **4가지 전략**으로 처리
1. 예방 (Prevention)
2. 회피 (Avoidance)
3. 탐지 (Detection)
4. 회복 (Recovery)

### 예방 (Prevention)

**: 데드락 발생 조건(4가지)** 중 하나를 깨뜨려서 교착상태 자체를 원천 차단

1. **상호 배제 부정**
    - 원래: 자원은 한 번에 하나만 사용 가능
    - 예방: 동시에 여러 프로세스가 자원을 공유하게 만들기
2. **점유와 대기 부정**
    - 원래: 자원을 이미 점유하면서 다른 자원도 기다림
    - 예방: 실행 전에 필요한 자원을 **한 번에 모두 할당**
3. **비선점 부정**
    - 원래: 이미 점유한 자원은 빼앗을 수 없음
    - 예방: 필요하면 자원을 **강제로 회수(선점)**
4. **환형 대기 부정**
    - 원래: 프로세스들이 원형 고리처럼 서로 자원을 기다림
    - 예방: **자원에 번호를 붙여** 항상 작은 번호 → 큰 번호 순서로만 요청

단점

- 예방은 강력하지만, **자원 활용 효율성이 떨어짐**
- ex. “모든 자원을 한 번에 다 줘야 한다” → 자원 낭비 발생

### 회피 (Avoidance)

: 데드락이 일어날 수 있는 위험한 상황(Unsafe state)으로 가지 않도록 미리 피하는 방식
>- **Safe State (안전 상태)**
>    - 모든 프로세스가 결국 정상적으로 종료할 수 있는 상태
>- **Unsafe State (불안전 상태)**
>    - 당장은 괜찮아 보여도, 진행하다 보면 데드락이 생길 수도 있는 상태

**은행원 알고리즘 (Banker’s Algorithm)**

- ***"CPU는 최소한 하나의 프로세스에게 할당해줄 만큼의 자원을 항상 보유하고 있어야 한다"***

### 예시 (총 자원 12개)

| 프로세스 | 최대 요청량 | 현재 할당 | 남은 필요 |
| --- | --- | --- | --- |
| P0 | 10 | 5 | 5 |
| P1 | 4 | 2 | 2 |
| P2 | 9 | 2 | 7 |
- 현재 사용 중: `5+2+2=9` → 가용 자원: `12-9=3`
- 전략:
    - 여기서 가용자원을 어느 프로세스에게 할당해주느냐에 따라서 자원을 효율적으로 이용할 수 있게 됩니다.
        - 남은 가용자원 3개를 P1에게 할당 => **가용자원은 3 - 2 = 1**
        - P1의 작업이 끝나고 할당되어 있던 자원 4개 반납 => **가용자원은 1 + 4 = 5**
        - 남은 가용자원 5개를 P0에게 할당 => **가용자원은 5 - 5 = 0**
        - P0의 작업이 끝나고 할당되어 있던 자원 10개 반납 => **가용자원은 0 + 10 = 10**
        - 남은 가용자원 10개 중 7개를 P2에게 할당 => **가용자원은 10 - 7 = 3**
        - P2의 작업이 끝나고 할당되어 있던 자원 9개 반납 => **가용자원은 3 + 9 = 12**
    
    ⇒ 이렇게 P1 - P0 - P2 순서로 자원 할당 시 Safe sequence를 만족하게 됩니다.
    
    - 실행 순서: **P1 → P0 → P2** (Safe sequence)

즉, 은행원 알고리즘은 **항상 Safe sequence가 존재하도록 자원 배분**

>**Safe sequence**
>
>: 이 순서대로 자원 요청을 처리하면 교착상태가 절대 생기지 않는다”는 보장된 실행 순서

### 단점

- 프로세스 수, 자원 수, 최대 필요량을 **미리 알아야 함**
- 현실의 복잡한 시스템(DB, 멀티 클라우드 등)에서는 적용이 어려움

### 탐지 (Detection)

: 데드락 발생을 허용하되, **주기적으로 검사해서 사이클이 생겼는지 탐지**

- 자원 할당 그래프에서 **사이클 검사**
- OS: 탐지 루틴을 두고 주기적으로 확인
- DB: “Deadlock detected” 오류를 발생시키고 문제 트랜잭션 종료

### 회복 (Recovery)

: 데드락이 실제로 발생했을 때 해결하는 방법

1. **프로세스 종료**
    - 데드락에 연관된 프로세스를 하나 또는 여러 개 종료
2. **자원 선점**
    - 특정 프로세스로부터 자원을 빼앗아 다른 프로세스에게 할당
3. **DB 예시**
    - MySQL(InnoDB), PostgreSQL: 데드락 감지 시 **트랜잭션 하나를 강제로 롤백(**데이터베이스 트랜잭션에서 **지금까지 실행된 작업을 모두 취소하고, 이전 상태로 되돌리는 것)**
    - 애플리케이션 개발자는 **재시도 로직**을 작성해야 함
        - `@EnableRetry` 켜기
        - `@Retryable` + `@Transactional` 메서드에 적용
            - **@Transactional**
                
                → 메서드 안의 DB 작업을 **하나의 트랜잭션**으로 실행
                
                → 실패 시 **롤백**, 성공 시 **커밋**
                
            - **@Retryable**
                
                → 지정한 예외(예: Deadlock) 발생 시 **같은 메서드 재실행**
                
                → 횟수, 간격(backoff) 조절 가능
                
            
        - `@Recover`에서 최종 실패 처리
    
    ```java
    @EnableRetry // Application 클래스에 추가
    @SpringBootApplication
    public class App {}
    ```
    
    ```java
    @Service
    public class TransferService {
    
        private final AccountRepository accountRepository;
    
        public TransferService(AccountRepository accountRepository) {
            this.accountRepository = accountRepository;
        }
    
        @Retryable(
            value = { org.springframework.dao.DeadlockLoserDataAccessException.class },
            maxAttempts = 3,
            backoff = @Backoff(delay = 200, multiplier = 2)
        )
        @Transactional
        public void transfer(Long fromId, Long toId, int amount) {
            Account from = accountRepository.findById(fromId).orElseThrow();
            Account to = accountRepository.findById(toId).orElseThrow();
    
            from.withdraw(amount);
            to.deposit(amount);
    
            accountRepository.save(from);
            accountRepository.save(to);
        }
    
        @Recover
        public void recoverFromDeadlock(Exception ex, Long fromId, Long toId, int amount) {
            throw new RuntimeException("Deadlock 재시도 실패. 잠시 후 다시 시도해주세요.");
        }
    }
    ```
