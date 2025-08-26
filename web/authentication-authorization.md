> **📅 업로드 날짜**  
> 2025-08-26
> 
> **🗂 분류**  
> web  
>
> **🔗 노션 링크**  
> [노션에서 보기](https://important-marquess-d42.notion.site/Spring-Security-250a654e658a80b1ad6fdd98372cae1d?source=copy_link)
>
# [Spring Security] 인증 및 권한 부여

> 인증, 인가 및 일반적인 공격에 대한 보호를 제공하는 프레임워크
> 


💡**보안 3요소(+권한)**

- 접근 주체(Principal): 보호된 대상에 접근하는 사용자
- 인증(Authentication): 해당 사용자가 **본인이 맞는지 확인하는 과정**. 일반적으로 아이디/암호로 인증.
- 인가(Authorization): 인증된 사용자가 자원(url, 기능 등)에 **접근이 가능한지 결정**하는 절차
- 권한(Role): 특정 리소스에 대한 접근 권한을 그룹화한 것

ex. 회사에서의 직급(사원, 대리, 과장 등)

💡
1. Authentication -> Principal 생성
(인증 성공 시 Principal 정보가 생성됨)
2. Principal -> Role 부여
(인증된 사용자에게 역할이 부여됨)
3. Role -> Authorization 결정
(부여된 역할에 따라 권한이 결정됨)


✅ SecurityContextHolder: 인증된 사용자 정보 저장하는 곳

## 요청 흐름

<img width="1280" height="886" alt="image" src="https://github.com/user-attachments/assets/767f9113-bd8b-4dd7-8616-a8f3cb9d74c8" />


**1) Http Request → AuthenticationFilter**

- 사용자가 로그인 요청(예: `/login` POST)을 보냅니다.
- `AuthenticationFilter`(대표적으로 `UsernamePasswordAuthenticationFilter` 혹은 커스텀 필터)가 이 요청을 **가로채** 인증 절차를 시작합니다.

**2. 필터에서 ‘미인증 토큰’ 생성 (UsernamePasswordAuthenticationToken)**

- 요청 바디에서 **username, password**를 읽어
    
    ```java
    new UsernamePasswordAuthenticationToken(username, password);
    // 기본 형태: new UsernamePasswordAuthenticationToken(principal, credentials);
    ```
    
    를 생성.
    
- 이 토큰은 **미인증(authenticated=false)** 상태이며,
    - `principal = username(문자열)`
    - `credentials = 평문 비밀번호`
    - `authorities = null/빈` 상태.


>  💡
>
>  `Authentication` 구성
>
>  : Spring Security에서 **“인증 정보(사용자가 누구인지 증명)”를 담는 객체**
>
>  - 인증 과정의 **입력과 출력 모두** 이 객체로 다뤄집니다.
>  - **입력 시점**: 사용자가 보낸 자격 증명(username, password 등)을 담아 `AuthenticationManager`에게 전달.
>  - **출력 시점**: 인증이 성공하면 **Principal/Authorities가 채워진 인증 완료 객체**로 반환됩니다.

### 주요 속성 (간단 정리)

- **Principal**
    
    → 사용자 식별 정보 (username, UserDetails 등)
    
- **Credentials**
    
    → 자격 증명 (비밀번호, 토큰 등 / 성공 후 null 처리)
    
- **Authorities**
    
    → 권한 목록 (예: ROLE_USER, ROLE_ADMIN)
    
- **Details**
    
    → 부가 정보 (세션ID, IP 등)
    
- **Authenticated**
    
    → 인증 여부 (true/false)
    
</aside>

1. **필터 → AuthenticationManager.authenticate(token)**
- 필터는 토큰을 **AuthenticationManager**에 넘겨 **인증 위임**.

```java
// 2. Manager에 위임
Authentication result = authenticationManager.authenticate(token);
```

>  💡
>  - 즉, “**이 토큰 진짜 맞는 사용자냐? 확인 좀 해줘**” 하고 **검증 위임**을 하는 과정
>  - Manager 자체가 직접 검증하지 않고, 내부적으로 여러 `AuthenticationProvider`에게 이 토큰을 돌려서 검증을 시킵니다.

**4. Manager → AuthenticationProvider(s) 선택**

- `AuthenticationManager`(보통 `ProviderManager`)가 **여러 `AuthenticationProvider`** 목록을 **순서대로** 돌면서, 각 Provider의 `supports(Class<?> tokenType)`를 호출해 **자기가 처리 가능한 토큰인지** 확인합니다.
- `supports(...) == true`인 Provider에게만 `authenticate(token)`을 호출합니다.

>  💡
>  **왜 이런 구조인가요?**

>  - 한 시스템에 **여러 인증 방식**(폼 로그인, OTP, JWT, OAuth2, SAML…)이 공존할 수 있기 때문.
>  - 토큰 타입별로 책임을 분리해 **확장 가능**하고 **결합도 낮은** 구조를 유지합니다.

**5. Provider → UserDetailsService.loadUserByUsername()**

(가장 흔한 케이스인 **`DaoAuthenticationProvider`** 기준)

- `DaoAuthenticationProvider`는 **사용자 계정 조회 + 자격 증명 검증**을 담당합니다.
- 계정 조회는 `UserDetailsService.loadUserByUsername(username)`로 수행하고,
    
    검증은 **`PasswordEncoder.matches(raw, encoded)`** 등으로 합니다.
    
- (예: `DaoAuthenticationProvider`) Provider는 `UserDetailsService`를 통해 **계정 로딩**:
    
    ```java
    UserDetails ud = userDetailsService.loadUserByUsername(username);
    ```
    
    ```java
    // [Filter] 미인증 토큰 생성
    Authentication token =
        new UsernamePasswordAuthenticationToken(username, rawPassword);
    
    // [Manager] 위임 시작
    Authentication result = providerManager.authenticate(token);
    
    // [Provider] 선택 로직 (개념)
    for (AuthenticationProvider p : providers) {
      if (p.supports(token.getClass())) {
        return p.authenticate(token); // 성공 시 즉시 반환
      }
    }
    throw new ProviderNotFoundException("No provider found");
    
    // [DaoAuthenticationProvider] 내부 개념
    UserDetails ud = userDetailsService.loadUserByUsername(username);
    if (!passwordEncoder.matches(rawPassword, ud.getPassword())) {
      throw new BadCredentialsException("Bad credentials");
    }
    checkAccountFlags(ud); // locked/disabled/expired ...
    return new UsernamePasswordAuthenticationToken(ud, null, ud.getAuthorities());
    
    ```
    

**6. UserDetailsService → UserDetails 반환 (User implements UserDetails)**

- 도메인 `User`가 `UserDetails`를 구현했거나, 매핑해서 반환.
- 예) `username="alice"`, `password="$2a$..."(BCrypt)`, `authorities=[ROLE_USER]`, `enabled=true` 등. → 이 객체에는 **인코딩된 비밀번호, 권한, 상태 플래그** 등이 들어 있습니다.

**7. Provider의 실제 검증**

> "비밀번호·계정 상태 검증 후, 인증 토큰 생성"
> 
- **무엇을 하는 단계?**
    - `UserDetailsService`로 가져온 `UserDetails` 데이터를 활용해 비밀번호와 계정 상태를 **실제 검증**하는 단계.
- **검증 포인트**
    1. 비밀번호 비교 → `PasswordEncoder.matches(raw, encoded)`
    2. 계정 만료 여부 → `isAccountNonExpired`
    3. 계정 잠금 여부 → `isAccountNonLocked`
    4. 활성화 여부 → `isEnabled`
- **성공 시**
    - 새 **인증 완료 토큰** 생성:
        
        ```java
        new UsernamePasswordAuthenticationToken(userDetails, null, authorities);
        ```
        
    - principal = UserDetails
    - credentials = null (**보안상 비움**)
    - authorities = 권한 목록
    - authenticated = true
- **실패 시**
    - 예외 발생:
        
        `BadCredentialsException`(비밀번호 오류)
        
        `LockedException`(계정 잠금)
        
        `DisabledException`(비활성 계정)
        

**8. Provider → Manager로 인증 결과 반환**

> "Provider가 인증 성공 시 결과를 Manager에게 전달"
> 
- 여러 개의 `AuthenticationProvider`가 등록되어 있으면 **순서대로 검사**.
- **첫 번째로 성공한 Provider**가 만든 인증 객체를 바로 반환 → **다음 Provider들은 호출하지 않음**.
- 만약 처리할 Provider가 없다면 → **`ProviderNotFoundException`**
- 모든 Provider가 시도 후 실패하면 → **예외 전파**

**9. Manager → Filter로 결과/예외 전달**

> "Manager의 결과를 Filter로 돌려줌"
> 
- 인증 **성공** 시 → **인증된 Authentication 객체**를 필터로 반환.
- 인증 **실패** 시 → 예외(`AuthenticationException`)를 필터로 던짐.
- 필터는 이 결과를 보고 **성공/실패 처리 분기**:
    - 성공 → `AuthenticationSuccessHandler` 호출
    - 실패 → `AuthenticationFailureHandler` 호출

**10. SecurityContextHolder 저장 & 응답 처리**

> "인증 성공 시 컨텍스트 저장, 실패 시 초기화"
> 

**성공 시**

- 인증된 객체를 **SecurityContextHolder**에 저장:
    
    ```java
    SecurityContextHolder.getContext().setAuthentication(authenticated);
    ```
    
- 이후 컨트롤러/서비스에서 현재 사용자 정보 조회 가능:
    
    ```java
    Authentication auth = SecurityContextHolder.getContext().getAuthentication();
    ```
    
- **세션 전략**
    - `STATEFUL` → SecurityContext를 세션에 저장(다음 요청 재사용)
    - `STATELESS` → 저장하지 않고 매 요청마다 토큰으로 인증 필요(JWT, API 서버 등)
- 성공 핸들러 실행: 리다이렉트 또는 JSON 응답

**실패 시**

- `SecurityContextHolder.clearContext()`로 초기화
- 실패 핸들러(`AuthenticationFailureHandler`) 또는
    
    `AuthenticationEntryPoint`로 401 Unauthorized 응답
    

## **Spring Security 필터 구조란?**

>  위쪽: **DelegatingFilterProxy → FilterChainProxy → SecurityFilterChain**
>
>  SecurityFilterChain 안쪽: **AuthenticationFilter → AuthenticationManager → Provider → UserDetailsService → SecurityContextHolder**

Spring Security는 **Servlet Filter 기반**으로 동작해요.

즉, 사용자의 요청이 컨트롤러(Controller)까지 도착하기 전에 **필터 체인(Filter Chain)**을 지나면서

- 인증(Authentication)과 **인가(Authorization)** 과정을 수행합니다.

---

### **필요한 이유**

- 우리가 만든 컨트롤러에서 직접 “로그인 됐는지?”, “권한이 있는지?”를 매번 검사하면 코드가 복잡해져요.
- Spring Security는 이 과정을 **필터 단계에서 자동 처리**해줍니다.
- 즉, 컨트롤러에 도달하기 전에 **요청을 가로채서 필요한 보안 검증**을 해주는 거예요.

---

### **필터 구조 동작 흐름**

**1. DelegatingFilterProxy (입구 다리 역할)**

- 서블릿 컨테이너의 필터 체인과 Spring Security의 필터들을 연결해주는 **다리** 역할.
- 요청이 들어오면 Spring Context 안에서 Spring Security의 핵심 필터를 찾아서 넘겨줍니다.

**2. FilterChainProxy (보안 필터 관리자)**

- Spring Security에서 사용하는 **실제 보안 필터 체인을 관리하는 필터**.
- 어떤 요청 URL에 어떤 보안 필터들을 적용할지 결정하고, 해당 체인을 실행합니다.

**3. SecurityFilterChain (보안 필터들의 집합)**

- 로그인, 세션, JWT, CSRF 등 **다양한 보안 기능을 수행하는 필터들의 묶음**.
- 설정한 규칙에 따라 요청을 인증 또는 인가하고, 필요 시 차단하기도 합니다.

---

### **코드에서 하는 역할**

```java
@Bean
protected SecurityFilterChain config(HttpSecurity http) throws Exception {
    return http
        .httpBasic(AbstractHttpConfigurer::disable)       // 기본 인증 비활성화
        .csrf(AbstractHttpConfigurer::disable)            // CSRF 보호 비활성화
        .authorizeHttpRequests(requests -> {              // 요청별 인가 정책 설정
            requests
                .requestMatchers("/h2-console/**").permitAll()
                .requestMatchers("/api/account/**").permitAll()
                .requestMatchers("/login", "/api/account/signup", "/api/member/login").permitAll()
                .anyRequest().authenticated();
        })
        .sessionManagement(session ->
            session.sessionCreationPolicy(SessionCreationPolicy.STATELESS) // 세션 대신 JWT 사용
        )
        .addFilterBefore(jwtFilter(), UsernamePasswordAuthenticationFilter.class) // JWT 필터 등록
        .exceptionHandling(exception -> {
            exception.defaultAuthenticationEntryPointFor(
                new HttpStatusEntryPoint(HttpStatus.UNAUTHORIZED),
                new AntPathRequestMatcher("/api/**")
            );
        })
        .build();
}

```

- 위 설정에서 `SecurityFilterChain`을 정의하는데, 이 안에서 어떤 필터들이 어떤 순서로 동작할지 Spring Security가 결정합니다.
- 예를 들어 `addFilterBefore(jwtFilter(), UsernamePasswordAuthenticationFilter.class)`는
    
    **JWT 인증 필터**를 **아이디/비밀번호 인증 필터 앞에 추가**하는 설정이에요.
    

---

## **한눈 요약**

| 역할 | 이름 | 설명 |
| --- | --- | --- |
| **입구 다리** | DelegatingFilterProxy | 서블릿 필터 체인 ↔ Spring Security 필터 연결 |
| **관리자** | FilterChainProxy | 어떤 요청에 어떤 보안 필터 체인을 적용할지 결정 |
| **필터 모음집** | SecurityFilterChain | 로그인, 세션, JWT, CSRF 등 보안 필터들의 묶음 |
