> **📅 업로드 날짜**  
> 2025-07-31
> 
> **🗂 분류**  
> Database  
>
> **🔗 노션 링크**  
> [노션에서 보기](https://important-marquess-d42.notion.site/SQL-Injection-23fa654e658a80189d10fe0d420441df?source=copy_link)

# SQL Injection

- 사용자가 입력한 값이 `SQL 쿼리`에 그대로 삽입되면서, DB 조작이 가능한 보안 취약점

## 공격방법

기본원리

<div style="display: flex; gap: 10px;">
  <img width="400" height="400" alt="image (14)" src="https://github.com/user-attachments/assets/66998d03-aabf-4917-8e68-dce0da3547de" />
  <img width="400" height="400" alt="image (15)" src="https://github.com/user-attachments/assets/90d6cc2f-9284-4e61-94ba-a25ba0794ba5" />
</div>


- 평소에 ID,PW가 틀리면 죄측과 같은 에러가 발생할거임
- **그런데 !!  ID에  `‘`  ←** 이친구를 붙이고 로그인을 하면 다른에러가 발생함 (우측)
    - 왜냐? `‘` 얘는 SQL문법이고,  이상한 SQL문법이라는 에러를 발생시킨거임
    

즉 로그인하기 위해 적은 ID가 그대로 SQL문에 들어가게 되고, 아래와 같은 SQL문이 되어 에러가 발생함

→ SQL문으로 뭔들 할 수 있다는 소리 = 해킹 당하기 쉽다

```jsx
SELECT * FROM users

WHERE id = ‘AAAA‘ ‘  AND  pw = “1234” 
```

## 강제 로그인
<img width="500" height="317" alt="image (16)" src="https://github.com/user-attachments/assets/103ccf1d-6414-4dc0-9e9b-8e9b7296504e" />


### ID만 알고 PW를 모를경우

```sql
SELECT * FROM users
WHERE id = 'tuser' --' AND pw = '1234'
```

- ID 입력을  tuser’ --  라고 입력했을 경우, SQL문에서 -- 이건 주석처리를 해주는 문법이기 때문에 AND 이후의 SQL문들이 주석처리가 된다.
    - 즉 ID만 입력하고도 로그인이 가능해지는것 !

### ID도 모를때

```sql
SELECT * FROM users
WHERE id = 'tuser' OR 1==1  --' AND pw = '1234'
```

- 입력을 'tuser' OR 1==1 라고 입력했을 경우 1 == 1은 항상 TRUE이기 때문에
- accounts 테이블의 모든 레코드 중에서 첫 번째 레코드를 반환하여 로그인이 된다.

### UNION SELECT를 사용할때

```sql
//일반적인 SQL문
SELECT * FROM account WHERE artist = -1 

// SQL Injection한 SQL문
SELECT * FROM account WHERE artist = -1 
UNION
SELECT id, pw From users WHERE name = 'test'
```

- union → select문 두개를 이어 붙여달라는 쿼리문법이며, 두개의 select문 결과를 출력해주는데,
- 결과적으로 account테이블의  결과 + users테이블의 ID, PW  둘다 조회가 되버림
- 주로 로그인/검색창 입력값을 통해 삽입
- ※   select문 두개의 컬럼개수와 타입 일치해야 한다.

## SQL Injection 예방

### parameterzerd query 사용

- 쿼리 구조와 사용자 입력값을 명확히 분리해서, 입력값이 쿼리 구조를 절대 변경할 수 없도록 만든 안전한 SQL 작성 방법

### **파라미터화된 쿼리 구조**

```sql
String query = "SELECT * FROM users WHERE id = ? AND pw = ?";
PreparedStatement pstmt = conn.prepareStatement(query);
pstmt.setString(1, userInput); // 첫 번째 ? 자리에 userInput 삽입
pstmt.setString(2, pwInput);   // 두 번째 ? 자리에 pwInput 삽입
```

- ?   는 입력값(파라미터)을 넣을 자리를 의미
- 이 쿼리는 구조가 고정된 템플릿으로 먼저 DB에 전달됨
- 이후에 값을 따로 바인딩(escape 해서 넣어줌)

### SQL Injection 의 경우

```sql
userInput = "admin' --"
```

- 보통이라면 이런 문자열은 쿼리를 깨트리지만,
PreparedStatement 객체는 이걸 자동으로 아래처럼 `escape`해서 처리해줌

### Escape 된 SQL문

```sql
SELECT * FROM users WHERE id = 'admin'' --' AND pw = '1234';
' → '' (작은 따옴표 두 개로 이스케이프)
```

- 이 때문에 문자 그대로 'admin' --  라는 id를 가진 사용자를 찾는 의미가 됨
    
    즉, ', ", ;, --   이런 기호들이 SQL 문법으로 인식되지 않음 → 안전함
    

※ Escape 처리란?

특수 문자나 위험한 문자를 안전하게 다루는 과정

입력값에 ', ", ;, -- 등의 SQL 제어 문자가 포함되어 있어도,

그냥 문자 그대로 인식되도록 DB 드라이버가 자동으로 처리해주는 과정

## JPA에서는?

- JPA Repository의 save() 메소드 같은 API를 사용한다면 내부적으로 Parameter Binding을 해주기 때문에 안전하다 !
- 하지만 쿼리를 문자열로 직접 만들게 되면 SQL Injection의 위험성이 있다.
