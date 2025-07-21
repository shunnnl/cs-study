> **📅 업로드 날짜**  
> 2025-07-21 
>
> **🗂 분류**  
> Data Structure
>
> **🔗 노션 링크**  
> [노션에서 보기](https://important-marquess-d42.notion.site/Stack-Queue-233a654e658a80faac02d12ff2aa6e89?source=copy_link)


# 스택(Stack) & 큐(Queue)

## 스택(stack)
<img width="610" height="663" alt="image (2)" src="https://github.com/user-attachments/assets/6f8a7088-ebb3-43c7-a6bf-7c958057de60" />


- 스택은 한쪽 방향에서만 데이터의 삽입과 삭제가 가능한 구조이며, 가장 마지막에 저장된 데이터가 가장 먼저 삭제 되는 `후입선출(LIFO, Last In First Out)`구조
- 스택의 자료구조는 삽입과 삭제시에 O(1), 탐색에는 O(n)의 시간복잡도를 가짐

<aside>

📌 스택의 활용 예시


- 웹 브라우저의 방문기록(뒤로가기) : 가장 나중에 열린 페이지부터 다시 보여주기
- 실행 취소 (Ctrl + z)
</aside>

## 큐(Queue)
<img width="605" height="603" alt="image (3)" src="https://github.com/user-attachments/assets/3a55da33-fb61-4420-a9c5-936951433e93" />

- 큐는 위에서 설명한 스택과는 달리 한 쪽에서는 데이터 삽입, 다른 한 쪽에서는 데이터의 삭제만 가능한  `선입선출(FIFO, First In First Out) 구조`
- 큐는 스택과 마찬가지로 삽입과 삭제에는 O(1), 탐색에는 O(n)의 시간복잡도를 가짐

<aside>

📌 큐의 활용 예시

- BFS 알고리즘
- 콜센터 고객 대기시간
</aside>

## 큐의 다른 종류

## **우선순위 큐 (Priority Queue)**

<img width="617" height="635" alt="image (4)" src="https://github.com/user-attachments/assets/2716b157-c4b4-46db-a84f-faff079672cc" />

- 우선순위 큐의 각 요소는 값과 우선순위, 총 2개의 데이터를 가지고 있다
- 선형큐와는 달리 우선순위가 높은 요소일수록 먼저 삭제되는 특징을 가지고 있으며 우선순위가 같은 데이터일 경우 삽입순서를 따른다
- 삽입 및 삭제 시 우선순위에 따라 요소들을 정렬 해야하기 때문에 주로 `힙(Heap)`자료구조로 구현되며
- 자료의 중간에 새 요소를 삽입하는 일이 빈번하게 발생하는데, 배열로 구현할 경우 앞뒤로 밀어내고 당겨야 하는 연산이 추가적으로 필요하다 그래서 LinkedList를 이용하는게 더 유리함
- 운영체제의 CPU 스케줄러 등에 활용되고 있다

### 힙 자료구조

- **힙(Heap)**은 이진 트리를 기반의 형태로, **최대 값과 최소 값을 찾아낼 수 있는 자료구조**이다.
- 완전이진트리의 형태
- 힙의 종류로 최대 힙(Max Heap)과 최소 힙(Min Heap)이 존재
- 중복값을 허용
<img width="820" height="468" alt="image (5)" src="https://github.com/user-attachments/assets/64143631-5b08-4185-a478-66be6a5adfde" />


### 삽입 연산(heapify-up）

1.  마지막 위치에 노드 삽입
2. 부모 노드와 비교하면서 위로 올라감
3. 힙 조건 만족할 때까지 swap

### 삭제 연산(heapify-down)

1. 루트 노드 제거
2. 마지막 노드를 루트에 올림
3. 자식과 비교하며 아래로 내려감
4. 힙 조건 만족할 때까지 swap

### 배열 vs 힙

배열로 구현할때 최악의 경우 삽입해야 하는 위치를 찾기 위해 모든 인덱스를 탐색해야 합니다. 즉 이 때의 시간 복잡도는 자료가 n개라고 할 때 O(n) 이 됩니다. 

→ 배열로 구현 시 시간 복잡도 : 삭제는 O(1), 삽입은 O(n)

힙으로 구현할때 이진 트리의 높이가 하나 증가할 때마다 저장 가능한 자료의 갯수는 2배 증가하며, 비교 연산 횟수는 1회 증가합니다. 즉 삭제나 삽입 모두 최악의 경우에는 O(log2n) 의 시간 복잡도를 가집니다. 

→ 힙으로 구현 시 시간 복잡도 : 삭제는 O(log2n), 삽입은 O(log2n)

## **원형 큐 (= 환형 큐, Circular Queue, Ring Buffer)**

- 선형 큐의 삭제 후 앞부분에 빈 공간이 생겨도 재사용이 불가능해 메모리 낭비가 발생하는 구조적 단점을 보완하기 위해 나온 큐
- 원형 큐는 배열의 끝과 시작을 연결해 마치 원처럼 순환하며, front와 rear 포인터를 순환 방식(`% size`)으로 이동시켜 빈 공간을 재사용할 수 있도록 하여 공간 활용 효율을 높임
<img width="7055" height="956" alt="image (6)" src="https://github.com/user-attachments/assets/1009ab54-3c64-4224-a97f-80635e584fe4" />


그림[1]에서 첫번째 원소가 삽입되고 해당 위치는 rear 이다, [2] 처럼 계속 원소를 삽입해서 D가 rear이 되었다
<img width="7055" height="1049" alt="image (7)" src="https://github.com/user-attachments/assets/1cf2945d-f808-4897-afd4-b141054a82e6" />


[3]에서 보면 맨앞 요소인 A를 dequeue했다.  그러면서 front는 A를 가리키고

[4]에서 보면 한번도 dequeue를 해서 front는 B를 가리키게 된다.
<img width="7055" height="937" alt="image (8)" src="https://github.com/user-attachments/assets/e2c8bcdc-03e7-4408-b750-d97950eb28a0" />


만약 [5] 처럼 rear 배열에 원소를 전부다 넣었다고 할때, [6]처럼 배열의 처음으로 돌아와서 남은 공간에 순차적으로 삽입을 할 수 있게 된다 (가장큰 차이점이다)
