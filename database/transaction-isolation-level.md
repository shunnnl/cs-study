> **📅 업로드 날짜**  
> 2025-08-02  
>
> **🗂 분류**  
> Database  
>
> **🔗 노션 링크**  
> [노션에서 보기](https://important-marquess-d42.notion.site/Transaction-Isolation-Level-243a654e658a80979b89ed3a86963757)

# 트랜잭션 격리 수준(Transaction Isolation Level)
- 다른 트랜잭션에서 일관성이 없는 데이터를 허용하도록 하는 수준을 결정할 수 있다.

>만약에 A 트랜잭션이 상품 C의 가격을 1000원에서 2000원으로 업데이트를 하였고, 아직 커밋하지 않은 상태였다. 이때 동시에 B 트랜잭션이 동일한 상품 C 데이터를 읽으려고 할때 B 트랜잭션이 1000원을 읽게 해야 할까, 2000원을 읽게 해야 할까?
>이렇게 **동시 접근하는 데이터를 어느 정도까지 다른 트랜잭션에 격리시킬지의 단계**를 설정할 수 있다.

- **READ UNCOMMITTED**, **READ COMMITTED**, **REPEATABLE READ**, **SERIALIZABLE** 
(뒤로 갈수록, 트랜잭션 간 고립 정도가 높아지며 성능이 떨어진다.)
- 일반적으로, READ COMMITTED(oracle), REPEATABLE READ(mysql) 중 하나를 사용한다.

>✅ **용어**
>- 커밋(commit): 트랜잭션 처리가 정상적으로 완료된 경우
>- 롤백(rollback): 오류가 발생할 경우 원래 상태대로 되돌리는 경우

## 다수의 트랜잭션이 경쟁하는 상황에서 발생할 수 있는 데이터 불일치 문제

### 1) Dirty Read
선발주자 트랜잭션이 데이터를 업데이트 후, 아직 커밋하지 않은 상황에서 **다른 트랜잭션에게 아직 커밋하지 않은 데이터에 대한 접근을 허용할 경우 발생할 수 있는 데이터 불일치**이다.

트랜잭션의 격리 레벨 중 **READ_UNCOMMITED**에서 발생하는 문제이며, **READ_COMMITTED**에서 문제점이 해결된다.

<img width="1024" height="560" alt="image" src="https://github.com/user-attachments/assets/58516ea3-9550-4bec-ad89-d498620a657f" />


> 위 이미지의 상황은 아래와 같다.
> 
> 
> 1. Alice 트랜잭션이 post 테이블의 id가 1인 데이터를 업데이트 후 아직 커밋하지 않은 상황이다.
> 
> 2. Bob 트랜잭션이 Alice 트랜잭션이 아직 커밋하지 않은 데이터를 읽어서 title이 'ACID'로 업데이트 의 값을 읽어온다.
> 
> 3. 예상치 못한 오류로 Alice 트랜잭션의 Update 작업이 롤백되어 데이터는 다시 title이 'Transaction'값으로 복원되었다.
> 
> 4. 롤백 후 Bob 트랜잭션이 데이터를 다시 읽으면 title이 'Transaction'을 바뀌어있다. 동일한 데이터의 값이 2번에서와 다른 불일치 문제가 발생!
> 

### 2) **Non-Repeatable-Read**
한 트랜잭션에서 **읽은 데이터를 다른 트랜잭션에서 수정 및 삭제가 가능할 때 생기는 데이터 불일치** 문제이다. 즉, 한 트랜잭션에서 읽은 데이터를 다른 트랜잭션에서 수정 및 삭제가 가능하다.

**Dirty Read**에 비해서는 발생 확률이 적으며, 트랜잭션의 격리 레벨중 **READ_UNCOMMITED, READ_COMMITTED**에서 발생하며, **REPEATABLE_READ**에서 해결된다.

<img width="1024" height="646" alt="image" src="https://github.com/user-attachments/assets/75d2e444-0c92-42d8-8b42-4bc57b0ef2e8" />


> 위 이미지의 상황은 아래와 같다.
> 
> 
> 1.  Alice의 트랜잭션이 시작되고, post 테이블의 id가 1번인 레코드에 접근하려고 한다.
> 
> 2. Bob의 트랜잭션도 시작되고, post 테이블의 id가 1번인 동일한 레코드를 읽는다.
> 
> 3. Alice의 트랜잭션에서 해당 레코드의 title 데이터를 'ACID'로 업데이트 후 커밋한다.
> 
> 4. Bob의 트랜잭션이 해당 데이터를 다시 읽었을 땐, Alice 트랜잭션이 해당 데이터를 수정했기 때문에, 2번에서 읽은 데이터가 다른 불일치가 발생한다.
> 


### 3) **Phantom-Read**
한 트랜잭션에서 **일정 범위의 레코드를 두 번 이상 읽을 때 발생할 수 있는 데이터 불일치** 상황이다.
하나의 트랜잭션에서 같은 쿼리를 두 번 실행했을 경우, 첫 번째 쿼리에서는 없던 유령(Phantom) 레코드가 추가되어 있거나, 삭제 되어 있을 수 있다.

Phantom Read는 **READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ** 격리 레벨에서 발생 가능하며, **SERIALIZABLE** 격리 레벨에서 해결된다.

<img width="1280" height="839" alt="image" src="https://github.com/user-attachments/assets/75e16ccc-28c5-4cf1-afc4-59e21314317a" />


> 위 이미지의 상황은 아래와 같다.
> 
> 
> 1. Bob 트랜잭션은 post_comment 테이블에서 post_id가 1인 N개의 레코드를 읽고 있었다.
> 
> 2. Alice 트랜잭션은 post_id가 1인 새로운 데이터를 Write 한 후 커밋하였다.
> 
> 3. Bob 트랜잭션이 1번에서처럼 동일하게 post 테이블에서 post_id가 1인 레코드를 읽으면 2번에서 새로 추가된 레코드가 추가로 조회되는 데이터 불일치가 발생할 수 있다.
> 

## **1) READ UNCOMMITTED**

- **다른 트랜잭션에서는 "아직 커밋되지 않은 데이터"를 읽을 수 있으므로** 트랜잭션 기능을 거의 수행하지 않는다. (가장 낮은 레벨)
- **Dirty Read (+ Non-Repeatable-Read, Phantom-Read)** 현상이 발생한다.

## **2) READ COMMITTED**

- 다른 트랜잭션이 **커밋하지 않은 데이터는 읽을 수 없지만, 특정 데이터를 읽은 상태에서 다른 트랜잭션이 해당 데이터를 수정후 커밋할 수 있다.**
- READ_UNCOMMITTED에서 발생한 **DIRTY_READ**는 해결되었지만, 여전히 **Non-Repeatable-Read, PHANTOM_READ**가 발생할 수 있는 격리 레벨이다.
- **Oracle, H2 등 MySQL을 제외한 대부분의 DB에 설정된 기본 격리 수준**

## **3) REPEATABLE READ**

- 한 트랜잭션에서 읽은 데이터를 다른 트랜잭션이 수정할 수는 없지만 **새로운 데이터를 삽입하는 경우, 여러 번 데이터를 읽을 때 발생한다.**
- 새롭게 삽입된 데이터는 다른 트랜잭션에서도 읽을 수 있으므로, **PHANTOM READ**가 발생할 수 있다.
- **MySQL에 설정된 기본 격리 수준**

## **4) SERIALIZABLE**

- **모든 트랜잭션은 하나씩 차례대로 실행**되는 것처럼 처리된다.(PHANTOM READ 발생x)
- **동시 처리 능력**이 다른 격리 레벨보다 떨어지고 성능 저하가 발생할 수 있어 데이터베이스에서 거의 사용되지 않는다.

<img width="1280" height="563" alt="image" src="https://github.com/user-attachments/assets/b2b6a5ed-2f0e-4a56-83ee-0c3d6ae60ee0" />
