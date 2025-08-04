> **📅 업로드 날짜**  
> 2025-08-04
> 
> **🗂 분류**  
> Database  
>
> **🔗 노션 링크**  
> [노션에서 보기](https://important-marquess-d42.notion.site/Transaction-243a654e658a80c5b976f011c08a2bfd?source=copy_link)

# 트랜잭션(Transaction)

- 트랜잭션(Transaction)의 사전적 의미는 거래이고,
- 컴퓨터 과학 분야에서의 트랜잭션(Transaction)은 "더이상 분할이 불가능한 업무처리의 단위"를 의미한다.
- 하나의 작업을 위해 더이상 분할될 수 없는 명령들의 모음,즉, 한꺼번에수행되어야 할 일련의 연산모음을 의미한다.

## 적절한 예시

계좌이체 행위를 풀어서 설명 해보면  **인출**과 **입금** 두 과정으로 이루어진다

인출에는 성공했는데, 입금이 실패하면 치명적인 결과가 나온다

따라서 이 두 과정은 동시에 성공하던지, 동시에 실패해야한다.

이 과정을 동시에 묶는 방법이 바로 트랜잭션이다

이처럼 데이터베이스와 어플리케이션의 데이터 거래에 있어서는 안전성을 확보하기 위한 방법이 트랜잭션이며, 처리과정 을 수행하던 중 오류가 발생하면, 결과를 재반영 하는 것이 아니라 모든 작업을 원상태로 복구하고, 처리 과정이 모두 성공했을때만 그 결과를 반영한다.

## **트랜잭션 특징(ACID)**

<table>
  <tr>
    <th width="180px">특성</th>
    <th>설명</th>
  </tr>
  <tr>
    <td><b>Atomicity (원자성)</b></td>
    <td>
      원자성은 트랜잭션이 데이터베이스에 모두 반영되던가, 아니면 전혀 반영되지 않아야 한다는 것이다.<br><br>
      트랜잭션은 사람이 설계한 논리적인 작업 단위로서, 일처리는 작업단위 별로 이루어져야 사람이 다루는데 무리가 없다.<br><br>
      만약 트랜잭션 단위로 데이터가 처리되지 않는다면, 설계한 사람은 데이터 처리 시스템을 이해하기 힘들 뿐만 아니라, 오작동 했을 시 원인을 찾기가 매우 힘들어질 것이다.
    </td>
  </tr>
  <tr>
    <td><b>Consistency (일관성)</b></td>
    <td>
      일관성은 트랜잭션의 작업 처리 결과가 항상 일관성이 있어야 한다는 것이다.<br><br>
      트랜잭션이 진행되는 동안 데이터베이스가 변경되더라도, 업데이트된 데이터베이스로 트랜잭션이 진행되는 것이 아니라,
      처음에 트랜잭션을 시작할 때 참조한 데이터베이스로 진행된다.<br><br>
      이렇게 함으로써 각 사용자는 일관성 있는 데이터를 볼 수 있다.
    </td>
  </tr>
  <tr>
    <td><b>Isolation (고립성)</b></td>
    <td>
      고립성은 둘 이상의 트랜잭션이 동시에 실행되고 있을 경우, 어떤 하나의 트랜잭션이라도 다른 트랜잭션의 연산에 끼어들 수 없다는 점을 가리킨다.<br><br>
      하나의 특정 트랜잭션이 완료될 때까지, 다른 트랜잭션이 그 트랜잭션의 결과를 참조할 수 없다.
    </td>
  </tr>
  <tr>
    <td><b>Durability (지속성)</b></td>
    <td>
      지속성은 트랜잭션이 성공적으로 완료됐을 경우, 결과는 영구적으로 반영되어야 한다는 점이다.
    </td>
  </tr>
</table>



## 트랜잭션의 상태

<img width="966" height="255" alt="image (17)" src="https://github.com/user-attachments/assets/170cd467-2b31-4c6b-bb57-728e4fc70ded" />

<br><br>

1-1. 활성(Active) : 트랜잭션이 정상적으로 실행중인 상태를 의미한다

**작업 성공시**

2-1. 부분 완료(Partially Committed) : 트랜잭션의 마지막까지 실행되었지만, Commit 연산이 실행되기 직전의 상태

2-2. 완료(Committed) : 트랜잭션이 성공이 종료되어 Commit 연산을 실행한 후의 상태

**작업 실패시**

2-1. 실패(Failed) : 트랜잭션 실행에 오류가 발생하여 중단된 상태.

2-2. 철회(Aborted) : 트랜잭션이 비정상적으로 종료되어 Rollback 연산을 수행한 상태. 

(트랜잭션 내부의 작업을 다시 수행 이전의 상태로 돌아감)

## 기본 용어

- **Commit 란?**
    - 작업들을 정상 처리하겠다고 확정하는 명령어로서, 해당 처리과정을 DB에 영구 저장을 의미하며 Commit 을 수행하면 하나의 트랜잭션 과정이 종료되는 것이다
- **Roll-back 란?**
    - 작업 중 문제가 발생되어 트랜잭션의 처리 과정에서발생한 변경사항을 취소하는 명령어이며 마지막 Commit을 완료한 시점으로 돌아간다는 의미이다
    

## 트랜잭션 예외

- DDL문(CREATE, DROP, ALTER, RENAME, TRUNCATE)은 transaction의 rollback 대상이 아니다.

## **MYSQL 트랜잭션 전역 설정**

<img width="300" height="154" alt="image (18)" src="https://github.com/user-attachments/assets/80fb26ec-d9d8-4292-9a70-d1e70cd31d42" />


- MYSQL 에서는 디폴트로 auto commit이 on으로 설정 되어 있다.

※ 0은 auto Commit Off
   1은 auto Commit On

- @Transactional을 이해하려면, Spring AOP에 대한 지식이 있어야 한다.
그래서 AOP에 대해서 더 파헤쳐 볼 예정이다 !

<aside>
💡

참고 자료

[https://inpa.tistory.com/entry/MYSQL-📚-트랜잭션Transaction-이란-💯-정리#트랜잭션transaction_이란?](https://inpa.tistory.com/entry/MYSQL-%F0%9F%93%9A-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98Transaction-%EC%9D%B4%EB%9E%80-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC#%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98transaction_%EC%9D%B4%EB%9E%80?)

[Inpa Dev 👨‍💻:티스토리]

</aside>
