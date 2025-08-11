> **📅 업로드 날짜**  
> 2025-08-11
> 
> **🗂 분류**  
> web  
>
> **🔗 노션 링크**  
> [노션에서 보기](https://important-marquess-d42.notion.site/CSRF-XSS-246a654e658a8033896ef667ab6fe9e2?source=copy_link)
>
# CSRF & XSS

## XSS(Cross-Site Scripting, 사이트 간 스크립팅)

![](https://blog.kakaocdn.net/dna/dLc6hO/btsObK9znMI/AAAAAAAAAAAAAAAAAAAAADZuYwtmLFvUq7-2D5aWZOiauY4xbsr20y7QSXeHWbjp/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1756652399&allow_ip=&allow_referer=&signature=L3kSs4NRLvNWG%2BND0bCQAtq0HUQ%3D)

- 공격자가 상대방의 브라우저에 악성 스크립트를 삽입해 **의도하지 않은 명령을 실행시키거나 세션 등을 탈취**할 수 있는 취약점
- 웹 서버 사용자에 대한 (1)입력값 검증이 미흡할 때 발생할 수도 있고, 주로 (2)여러 사용자가 보는 게시판이나 메일 등을 통해 악성 스크립트를 삽입하는 공격 기법
- XSS 공격 유형 : Stored XSS, Reflected XSS, DOM-based XSS

### **1. Stored XSS (저장형 크로스사이트 스크립트)**

> 악성 스크립트가 서버에 저장
> 

![](https://blog.kakaocdn.net/dna/l4XuV/btsObnf1Psl/AAAAAAAAAAAAAAAAAAAAAJJ9nZCLHWf37SKjbPJsAa1M155XuQHuhq013Uc_zd5T/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1756652399&allow_ip=&allow_referer=&signature=Q3xq9qMmPvdO896MqO%2BCAbXj8mk%3D)

- 개념
    - **공격자의 악성스크립트가 데이터베이스에 저장**되고 이 값을 출력하는 페이지에서 피해가 발생하는 취약점
    - 악성 스크립트를 서버에 저장시킨 다음 클라이언트의 요청 / 응답 과정을 통해 공격하는 방식
    - 공격자의 악성스크립트가 **서버에 저장되어 불특정 다수를 대상으로 공격**에 이용될 수 있어, URL을 클릭한 사용자만 피해를 입는 Reflected XSS 보다 공격 대상의 범위가 훨씬 크다.
- 공격 과정
    - 공격자는 악성스크립트가 포함된 게시물을 작성하여 게시판 등 사용자가 접근할 수 있는 페이지에 업로드한다.
    - 이때 사용자가 악성스크립트가 포함된 게시물을 요청하면, 공격자가 삽입한 악성 스크립트가 사용자 측에서 동작하게 된다.

### **2. Reflected XSS (반사형 크로스사이트 스크립팅)**

> URL 등에 포함된 악성 스크립트가 응답에 반영
> 

![](https://blog.kakaocdn.net/dna/mVLZr/btsOcoyjfoR/AAAAAAAAAAAAAAAAAAAAAK0LSFTbHGWXMxfy5Thwu-30iV5v_z3_qkdGgUdB1aGG/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1756652399&allow_ip=&allow_referer=&signature=whgzF3kdFdcQaHgGPfLxORP5kOE%3D)

- 개념
    - 사용자가 요청한 악성스크립트가 사용자 측에서 반사(Reflected)되어 동작하는 취약점
    - 공격자의 악성스크립트가 데이터베이스와 같은 저장소에 별도로 저장되지 않고, 사용자 화면에 즉시 출력되면서 피해 발생
    - 공격자는 악성스크립트가 포함된 URL을 이메일, 메신저 등을 통해 사용자가 클릭할 수 있도록 유도
    - 사용자가 **악성스크립트가 삽입된 URL을 클릭하거나 공격자에 의해 악의적으로 조작된 게시물을 클릭**했을 때, **사용자의 브라우저에 악성 스크립트가 실행**
- 공격 과정
1. 공격자는 검색 결과를 응답하는 파라미터 확인

```url
http://www.example.com/search?query=도서
```

2. 공격자는 검색 결과를 응답하는 파라미터에 악성스크립트 삽입

```url
http://www.example.com/search?query="><script>alert('악성스크립트');</script>
```

3. 서버는 공격자가 삽입한 악성 스크립트 응답

### **3. DOM-based XSS (DOM 기반 크로스사이트 스크립팅)**

> 클라이언트 측 자바스크립트 처리 로직에 의해 발생
> 

![](https://blog.kakaocdn.net/dna/b8UxJB/btsOcz0GdeD/AAAAAAAAAAAAAAAAAAAAAH5rtJt9LH-FpyEFx3SqBq3fAvu5enA2TGLNsEF4ihrl/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1756652399&allow_ip=&allow_referer=&signature=Plj7FHdjPIvJfPRMounbO%2FW0pSM%3D)

> DOM (Document Object Model)
> 
> 
> ```html
> <html>
>   <body>
>     <h1>안녕하세요</h1>
>   </body>
> </html>
> ```
> 
> ```html
> - html
>   └─ body
>       └─ h1 ("안녕하세요")
> ```
> 
- 개념
    - 공격자의 악성스크립트가 DOM 영역에서 실행됨으로써 서버와의 상호작용 없이 브라우저 자체에서 악성스크립트가 실행되는 취약점
    - 피해자의 브라우저가 html 페이지를 분석하여 DOM을 생성할 때 **악성 스크립트가 DOM의 일부로 구성**되어 생성되는 공격 방식
- 특징
    - DOM 영역에 변화가 생기면 브라우저는 서버로 패킷을 보내지 않고, DOM 영역에서 페이지를 변환시키기 때문에 **DOM의 일부로 실행되어 브라우저 자체에서 악성스크립트가 실행**된다.
    - DOM Based XSS는 서버와 상호작용 없이 브라우저에서 바로 악성스크립트가 실행되고 공격이 이루어지기 때문에 취약점을 쉽게 발견할 수 없다.(Stored XSS와 Reflected XSS는 서버에서 악성스크립트가 실행되고 공격이 이루어지기 때문에 위험 징후를 발견할 수 있다.)

### **위험성**

1. 쿠키 정보 및 세션 ID 획득
2. 시스템 관리자 권한 획득
3. 악성 코드 다운로드
4. 거짓 페이지 노출

### **보안 대책**

> **script 문자 필터링 (특수 문자 치환)**
> 

![](https://blog.kakaocdn.net/dna/bCTlDh/btsObsO7uiX/AAAAAAAAAAAAAAAAAAAAAJM2RAJ586wDGrLjDKQcOJzTWDvTJbmVL5oan7sZGOM3/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1756652399&allow_ip=&allow_referer=&signature=NvyA6ejFV0mEeQcdjOpkg6QPpS0%3D)

- XSS 취약점은 공격자가 삽입한 악성스크립트로 인해 발생하기 때문에 (1)입력값 검증을 통해 악성스크립트가 삽입되는 것을 방지해야 한다. 또한, 악성스크립트가 입력되어도 동작하지 않도록 (2)출력값을 무효화해야 한다.
- 입력값 필터링의 경우 데이터베이스에 악성스크립트가 저장되는 것을 원천적으로 차단해야 한다. 악성스크립트를 검증하여 스크립트에 적용되는 특수문자를 HTML Entity 로 치환하여 응답하도록 한다.

```java
String searchWord = request.getParameter("searchWord");
if (searchWord != null) {
    searchWord = searchWord.replaceAll("<","&lt;");
    searchWord = searchWord.replaceAll(">","&gt;");
    searchWord = searchWord.replaceAll("(","&lpar;");
    searchWord = searchWord.replaceAll(")","&rpar;");
}
```

## CSRF (Cross-Site Request Forgery, 사이트 간 요청 위조)

- **사용자가 로그인된 상태**를 악용해, **사용자의 의도(의지)와 무관하게 공격자가 원하는 요청을 서버에 보내게 만드는 공격**
- 공격자는 **브라우저가 자동으로 쿠키/세션을 포함시킨다는 점**을 이용함.

### **공격 조건**

1. 사용자가 **취약한 서버에 로그인**한 상태여야 함.
2. **쿠키 기반 세션**을 사용하는 상태여야 함.
3. 공격자가 해당 서버의 요청 방식(파라미터 등)을 알고 있어야 함.

### **동작 원리**

![](https://blog.kakaocdn.net/dna/cb4mTI/btsObLnv7nu/AAAAAAAAAAAAAAAAAAAAALIqKVgigjZCH_tsQcQekrvObWW3IDaylrCzEVFIsRNL/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1756652399&allow_ip=&allow_referer=&signature=aRiDyLMCzpm261lYxJvhSWvxe0w%3D)

1. 피해자(사용자)가 서버에 로그인 → 세션 ID가 브라우저 쿠키에 저장됨.
2. 공격자는 서버에 인증된 브라우저의 사용자가 악성 스크립트 페이지를 누르도록 유도
    - 게시판에 악성 스크립트를 게시글로 작성하여, 관리자 혹은 다른 사용자들이 게시글을 클릭하도록 유도
    - 메일 등으로 악성 스크립트를 직접 전달하거나, 악성 스크립트가 적힌 페이지 링크를 전달
3. 피해자가 해당 페이지를 열면, 브라우저는 자동으로 쿠키를 포함해 서버에 요청.
4. 서버는 쿠키를 보고 **정상 로그인 사용자 요청**으로 착각해 처리.

> 예시:
> 
> 
> 비밀번호 변경 요청을 자동으로 전송하는 페이지를 만들어 피해자가 클릭하게 함 → 서버가 비밀번호를 공격자가 지정한 값으로 변경.
> 
> 
>     💡 공격 예시
> 
>     1. 공격자는 피해자가 접속할 수 있는 게시판에 악성스크립트가 포함된 게시물을 작성한다.
> 
>     - 예를 들어, 패스워크 변경 페이지에서 패스워드 변경 요청을 보내면, 다음과 같은 파라미터를 전송하여 패스워드가 변경된다고 하자.
> 
> ```url
> http://www.example.com/changepassword/modAction.jsp?new_pw1="변경할패스워드"&new_pw2="패스워드확인"&action=change
> ```
> 
>     공격자는 자신이 원하는 패스워드로 변경을 유도하는 CSRF 스크립트를 작성하여 게시글을 등록한다.
> 
>     2. 피해자는 공격자가 등록헌 CSRF 스크립트가 삽입된 게시글에 접근하면, 피해자 측에서 패스워드 변경 스크립트가 동작하여 피해자의 패스워드가 변경된다.
> 
>     3. 이후 공격자는 피해자의 패스워드를 알고 있으므로 피해자의 계정을 탈취할 수 있다.
> 


### **보안 대책**

- **Referrer 검증**
    - 요청 헤더의 `Referrer` 값이 허용 도메인과 일치하는지 확인.
    
>     💡 왜 사용하나?
>     
>     CSRF는 “다른 사이트(evil.com)에서 우리 사이트(abc.com)로 자동 요청을 보내도, 브라우저가 쿠키를 자동으로 붙여준다”는 점을 악용합니다.
>    
>     서버가 “이 요청이 우리 사이트에서 온 건가?” 를 확인하면 많은 CSRF를 걸러낼 수 있습니다.
>     
>     💡 어떻게 동작하나?
>     
>     - 사용자가 abc.com에서 폼을 제출하면 요청 헤더에 보통
>         
>         `Origin: https://abc.com` 또는 `Referer: https://abc.com/page`가 들어옵니다.
>         
>     - 반대로 evil.com에서 자동으로 abc.com으로 요청을 보내면
>         
>         `Origin: https://evil.com` 이거나 `Referer: https://evil.com/...` 가 들어옵니다.
>         
>     
>     → 서버는 이 헤더의 프로토콜/호스트/포트가 우리 도메인과 일치하는지 확인해 외부 도메인이면 거절합니다.
    
- **CSRF 토큰 사용**
    - 세션에 난수 토큰 저장 → 요청 시 함께 전송 → 서버에서 토큰 일치 여부 검증.
    
>      💡 왜 사용하나?
>    
>      외부 사이트는 우리 페이지의 DOM을 읽을 수 없어서 서버가 생성해 둔 난수 토큰 값을 알 수 없습니다.
>    
>      이 토큰을 “정상 페이지에서만 얻을 수 있게” 만들고, 요청마다 제출하도록 요구하면 위조 요청을 막을 수 있어요.
>    
>      💡어떻게 동작하나? (세션 기반)
>   
>      1. 사용자가 우리 사이트 폼 페이지를 정상 경로로 열 때, 서버가 난수 토큰을 만들고 세션에 저장합니다. (예: `CSRF_TOKEN=abc123`)
>      2. 서버는 이 토큰을 폼의 hidden 필드나 메타 태그/JS 변수로 같이 내려줍니다.
>      3. 사용자가 폼을 제출할 때, 브라우저는 세션 쿠키 + 토큰 값을 함께 보냅니다.
>      4. 서버는 세션에 저장된 토큰과 요청에 실린 토큰을 비교해 일치하면 통과, 아니면 403.
    
- **SameSite 쿠키 설정**
    - 외부 사이트에서 요청 시 쿠키 전송 제한.
- **중요 기능에 추가 인증**
    - 비밀번호 변경, 결제 등 민감 요청 시 2차 인증 절차 적용.

## **XSS와 CSRF의 차이점**

| 구분 | XSS | CSRF |
| --- | --- | --- |
| 발생 원인 | **클라이언트가 서버를 신뢰** | **서버가 브라우저를 신뢰** |
| 공격 대상 | 사용자(브라우저) | 서버 |
| 목적 | 브라우저에서 악성 스크립트 실행 
→ 쿠키 탈취, 화면 변조 | 인증된 사용자의 권한으로 서버에 원치 않는 요청 전송 |
| 실행 방식 | 페이지 내 악성 JS 실행 | 자동 요청 발생 |
| 방어 방법 | 입력값 필터링, 출력 이스케이프 | CSRF 토큰, Referrer 체크, SameSite 쿠키 |
