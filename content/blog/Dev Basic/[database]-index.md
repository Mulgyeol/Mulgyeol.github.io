---
title: '[Database] Index'
date: 2021-05-10 19:05:15
category: Dev Basic
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/Database.jpg)

### [Refernece]

- [MySQL Full Text Search Index 사용하기](https://interconnection.tistory.com/95)
- [[MySQL] B-tree, B+tree란? (인덱스와 연관지어서)](https://zorba91.tistory.com/293)
- [[Database] 인덱스(index)란?](https://mangkyu.tistory.com/96)
- [[DataBase] DB 성능을 위한 Index](https://brunch.co.kr/@skeks463/25)
- [인덱스(Index)란 무엇인가?](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Database#index)

## 💡 인덱스(Index)란 무엇인가?

인덱스는 말 그대로 책의 맨 처음 또는 맨 마지막에 있는 색인이라고 할 수 있다. 이 비유를 그대로 가져와서 인덱스를 살펴본다면 데이터는 책의 내용이고 데이터가 저장된 레코드의 주소는 인덱스 목록에 있는 페이지 번호가 될 것이다. DBMS 도 데이터베이스 테이블의 모든 데이터를 검색해서 원하는 결과를 가져 오려면 시간이 오래 걸린다. 그래서 칼럼의 값과 해당 레코드가 저장된 주소를 키와 값의 쌍으로 인덱스를 만들어 두는 것이다.

DBMS 의 인덱스는 항상 정렬된 상태를 유지하기 때문에 원하는 값을 탐색하는데는 빠르지만 **새로운 값을 추가하거나 삭제, 수정하는 경우에는 쿼리문 실행 속도가 느려진다.**

**결론적으로 DBMS 에서 인덱스는 데이터의 저장 성능을 희생하고 그 대신 데이터의 읽기 속도를 높이는 기능이다.** SELECT 쿼리 문장의 WHERE 조건절에 사용되는 칼럼이라고 전부 인덱스로 생성하면 데이터 저장 성능이 떨어지고 인덱스의 크기가 비대해져서 오히려 역효과만 불러올 수 있다.

## 💡 Index의 자료구조는 어떤 것을 사용하나요?

그렇다면 DBMS 는 인덱스를 어떻게 관리하고 있는가

### 해시 테이블(Hash Table)

해시 테이블은 (Key, Value)로 데이터를 저장하는 자료구조 중 하나로 빠른 데이터 검색이 필요할 때 유용하다. 해시 테이블은 Key값을 이용해 고유한 index를 생성하여 그 index에 저장된 값을 꺼내오는 구조이다.

![](https://images.velog.io/images/mulgyeol/post/d19f0a37-3e2c-4bbd-b01b-9b46dd5d6f2f/image.png)

해시 테이블 기반의 DB 인덱스는 (데이터=컬럼의 값, 데이터의 위치)를 (Key, Value)로 사용하여 컬럼의 값으로 생성된 해시를 통해 인덱스를 구현했다. 해시 테이블의 시간복잡도는 O(1)이며 매우 빠른 검색을 지원한다.

하지만 DB 인덱스에서 해시 테이블이 사용되는 경우는 제한적인데, 그러한 이유는 **해시가 등호(=) 연산에만 특화되었기 때문이다.** 해시 함수는 값이 1이라도 달라지면 완전히 다른 해시 값을 생성하는데, 이러한 특성에 의해 부등호 연산(>, <)이 자주 사용되는 데이터베이스 검색을 위해서는 해시 테이블이 적합하지 않다.

즉, 예를 들면 "나는"으로 시작하는 모든 데이터를 검색하기 위한 쿼리문은 인덱스의 혜택을 전혀 받지 못하게 된다. 이러한 이유로 데이터베이스의 인덱스에서는 B+Tree가 일반적으로 사용된다.

### B+Tree

B+Tree는 DB의 인덱스를 위해 자식 노드가 2개 이상인 B-Tree를 개선시킨 자료구조이다. B+Tree는 모든 노드에 데이터(Value)를 저장했던 BTree와 다른 특성을 가지고 있다.

[더 자세히 알아보기](https://zorba91.tistory.com/293)

리프노드(데이터노드)만 인덱스와 함께 데이터(Value)를 가지고 있고, 나머지 노드(인덱스노드)들은 데이터를 위한 인덱스(Key)만을 갖는다.
리프노드들은 LinkedList로 연결되어 있다.
데이터 노드 크기는 인덱스 노드의 크기와 같지 않아도 된다.
데이터베이스의 인덱스 컬럼은 부등호를 이용한 순차 검색 연산이 자주 발생될 수 있다. 이러한 이유로 BTree의 리프노드들을 LinkedList로 연결하여 순차검색을 용이하게 하는 등 BTree를 인덱스에 맞게 최적화했다. (물론 Best Case에 대해 리프노드까지 가지 않아도 탐색할 수 있는 BTree에 비해 무조건 리프노드까지 가야한다는 단점도 있다.)

이러한 이유로 비록 B+Tree는 O(log2n) 의 시간복잡도를 갖지만 해시테이블보다 인덱싱에 더욱 적합한 자료구조가 되었다.

아래의 그림은 InnoDB에서 사용된 B+Tree의 구조이다.

![](https://images.velog.io/images/mulgyeol/post/55aba99c-7062-4d00-aab0-5d7f17688f75/image.png)

InnoDB에서의 B+Tree는 일반적인 구조보다 더욱 복잡하게 구현이 되었다. InnoDB에서는 같은 레벨의 노드들끼리는 Linked List가 아닌 Double Linked List로 연결되었으며, 자식 노드들은 Single Linked List로 연결되어 있다.

## 💡 모든 칼럼에 Index를 사용하면 좋나요?

SELECT 쿼리의 성능을 월등히 향상시키는 INDEX 항상 좋은 것일까? 쿼리문의 성능을 향상시킨다는데, 모든 컬럼에 INDEX 를 생성해두면 빨라지지 않을까? 결론부터 말하자면 **그렇지 않다.**

우선, 첫번째 이유는 INDEX 를 생성하게 되면 INSERT, DELETE, UPDATE 쿼리문을 실행할 때 별도의 과정이 추가적으로 발생한다.

- INSERT 의 경우 INDEX 에 대한 데이터도 추가해야 하므로 그만큼 성능에 손실이 따른다.
- DELETE 의 경우 INDEX 에 존재하는 값은 삭제하지 않고 사용 안한다는 표시로 남게 된다. 즉 row 의 수는 그대로인 것이다. 이 작업이 반복되면 어떻게 될까? 실제 데이터는 10 만건인데 데이터가 100 만건 있는 결과를 낳을 수도 있는 것이다. 이렇게 되면 인덱스는 더 이상 제 역할을 못하게 되는 것이다.
- UPDATE 의 경우는 INSERT 의 경우, DELETE 의 경우의 문제점을 동시에 수반한다. 이전 데이터가 삭제되고 그 자리에 새 데이터가 들어오는 개념이기 때문이다. 즉 변경 전 데이터는 삭제되지 않고 insert 로 인한 split 도 발생하게 된다.

하지만 더 중요한 것은 컬럼을 이루고 있는 데이터의 형식에 따라서 인덱스의 성능이 악영향을 미칠 수 있다는 것이다. 즉, 데이터의 형식에 따라 인덱스를 만들면 효율적이고 만들면 비효율적은 데이터의 형식이 존재한다는 것이다. 어떤 경우에 그럴까?

이름, 나이, 성별 세 가지의 필드를 갖고 있는 테이블을 생각해보자. 이름은 온갖 경우의 수가 존재할 것이며 나이는 INT 타입을 갖을 것이고, 성별은 남, 녀 두 가지 경우에 대해서만 데이터가 존재할 것임을 쉽게 예측할 수 있다. 이 경우 어떤 컬럼에 대해서 인덱스를 생성하는 것이 효율적일까? **결론부터 말하자면 이름에 대해서만 인덱스를 생성하면 효율적이다.**

왜 성별이나 나이는 인덱스를 생성하면 비효율적일까? 10000 레코드에 해당하는 테이블에 대해서 2000 단위로 성별에 인덱스를 생성했다고 가정하자. 값의 range 가 적은 성별은 인덱스를 읽고 다시 한 번 디스크 I/O 가 발생하기 때문에 그 만큼 비효율적인 것이다.

## 💡 Index 사용 시 장단점과 사용하면 좋은 경우와 나쁜 경우에 대해 설명하시오.

### 인덱스(index)의 장점과 단점

**장점**

- 테이블을 조회하는 속도와 그에 따른 성능을 향상시킬 수 있다.
- 전반적인 시스템의 부하를 줄일 수 있다.

**단점**

- 인덱스를 관리하기 위해 DB의 약 10%에 해당하는 저장공간이 필요하다.
- 인덱스를 관리하기 위해 추가 작업이 필요하다.
- 인덱스를 잘못 사용할 경우 오히려 성능이 저하되는 역효과가 발생할 수 있다.
  - 만약 CREATE, DELETE, UPDATE가 빈번한 속성에 인덱스를 걸게 되면 인덱스의 크기가 비대해져서 성능이 오히려 저하되는 역효과가 발생할 수 있다. 그러한 이유 중 하나는 DELETE와 UPDATE 연산 때문이다. 앞에서 설명한대로, UPDATE와 DELETE는 기존의 인덱스를 삭제하지 않고 **'사용하지 않음'** 처리를 해준다고 했다. 만약 어떤 테이블에 UPDATE와 DELETE가 빈번하게 발생된다면 실제 데이터는 10만건이지만 인덱스는 100만 건이 넘어가게 되어, SQL문 처리 시 비대해진 인덱스에 의해 오히려 성능이 떨어지게 될 것이다.

### 인덱스(index)를 사용하면 좋은 경우

- 규모가 작지 않은 테이블
- INSERT, UPDATE, DELETE가 자주 발생하지 않는 컬럼
- JOIN이나 WHERE 또는 ORDER BY에 자주 사용되는 컬럼
- 데이터의 중복도가 낮은 컬럼

## 💡 FULLTEXT INDEX

MySQL은 % 연산자를 사용하여 LIKE 패턴 연산을 지원할 뿐만 아니라, 단어 또는 구문에 대한 검색을 지원하며 이를 **풀 텍스트(FULLTEXT) 검색** 이라고 부른다. `FULLTEXT INDEX`는 일반적인 인덱스와는 달리 매우 빠르게 테이블의 모든 텍스트 필드를 검색한다.

이 인덱스는 검색 엔진과 유사한 방법으로 자연어를 이용하여 데이터를 검색할 수 있도록 모든 데이터의 문자열 단어를 저장한다.

### MySQL의 Full-text Index

: Full-text Index의 경우에는 한 컬럼 안에서 많은 형태의 데이터가 담겨있어서 효율적으로 데이터를 찾는 경우 사용하는 것이 일반적이다.

예를 들면,

컨텐츠 내용과 같이 사용자가 쓰기 나름인 데이터를 토큰 단위로 쪼개서 검색에 용이하게 한다.

컨텐츠 내용 : "나는 오늘 seoul의 sky를 바라보면서 베스킨라빈스31에서 맛있는 요리를 먹었다! 정말 기분 좋았다." 와 같이 한글, 영어, 숫자가 섞여 있거나 긴 내용을 검색할 때 사용된다.

### Full-text Index의 데이터를 인덱싱하는 기법

: 데이터를 인덱싱하는 이유는 사용자가 검색하게 될 키워드를 빠르게 검색할 수 있게 하기 위해서 다음 두 가지 종류의 parser를 이용해서 인덱스를 구축한다.

1. Built-in parser 또는 Stop-word parser

   - Stop-word 기법을 사용하는 기본 내장 파서(built-in parser)를 이용한다.
   - Stop-word는 토큰을 나눌때 해당하는 Stop-word가 나타났을 때 기준으로 토큰을 나눈다.
   - 예를 들면,
     Input : "나는 매일이 지옥 같았습니다.”
     Stop-word : “ ”(공백) => 공백은 디폴트 값이다.
     Token : 나는 | 매일이 | 지옥 | 같았습니다.
     공백을 기준으로 4개의 토큰이 인덱싱 된다.

2. N-gram parser
   - n-gram 기법을 사용하여 할당한 토큰의 크기 n만큼씩 데이터를 인덱스로 파싱해두었다가 사용하는 파서이다.
   - 예를 들면,
     Input : “나는 김치찌개가 좋습니다.”
     N-gram : n=2
     Token : 나는 | 는 | 김 | 김치 | 치찌 | 찌개 | 개가 | 개 | 좋 | 좋습 | 습니 | 니다 | 다. => 중에서 Token 2가 아닌 “는 “,” 김”, “개 “, “ 좋” 은 제거된다.
     나는 | 김치 | 치찌 | 찌개 | 개가 | 좋습 | 습니 | 니다 | 다. => 로 인덱싱 된다.

### N-gram Full-text Index 생성 과정

인덱스 대상 문서
=> 1) n글자로 구성된 서브 그룹
=> 2) 백엔드 인덱스
=> 3) 인덱스 대상 서브 그룹
=> 4) 2-Gram 서브 그룹
=> 5) 프론트엔드 인덱스 : 생성

1. 인덱싱 대상 문서를 n-gram 의 n보다 큰 값의 토큰으로 서브 그룹을 나눈다. 아래 그림에서는 4크기의 토큰으로 정하고 서브그룹을 구성한다.

![](https://images.velog.io/images/mulgyeol/post/5b2a2a18-e2bb-4e9f-9de4-bec1c5ac256f/image.png)

- 서브그룹 생성 방식은 해당 문서 데이터의 왼쪽을 시작으로 4글자 토큰을 만들고 토큰의 마지막 글자부터 다시 4글자 토큰을 만들어서 데이터의 끝까지 생성.
- 각 문서에 대한 4글자 토큰 서브그룹을 생성 해준다.

2. 위 1) 단계에서 추출된 서브그룹 중에서 중복되는 서브그룹별로 토큰 분류하고, 백엔드 인덱스를 만든다.
   ![](https://images.velog.io/images/mulgyeol/post/02a249d9-a3fc-4b5b-9d1a-cdc0330eba64/image.png)

- 중복되지 않는 4글자로 구성된 서브그룹 리스트를 만든다. 그래서 백엔드 인덱스를 만드는데에 사용한다.
- 백엔드 인덱스에 중복되지 않는 4글자로 구성된 서브 그룹을 활용해서 백엔드 인덱스에 문서위치와 같이 생성을 한다.
- 문서위치는 해당 서브그룹이 1)에서 위치하는 곳에 (문서번호,문서열번호)를 넣는다.

3. 백엔드 인덱스에서 중복되지 않은 서브그룹에서 다시한번 n-gram용 서브그룹을 만든다.
   ![](https://images.velog.io/images/mulgyeol/post/a7c65ed7-3f41-4860-bb3c-6a6c1c8f6857/image.png)

- 각 서브그룹을 다시 한번 2-gram 방식으로 서브그룹을 2글자 토큰으로 나눈다.
- n-gram 토크나이징 방식은 왼쪽부터 1글자씩 오른쪽으로 이동하면서 연속된 n글자로 토큰을 구성한다.
- ex) 가나다라마 일때,
  - n=2 => 가나,나다,다라,라마
  - n=3 => 가나다,나다라,다라마
  - n=4 => 가나다라,나다라마

4. 3)과정에서 생성된 2-Gram 토큰을 분류하고, 중복되지 않은 2-Gram 서브 그룹을 생성한다.
   ![](https://images.velog.io/images/mulgyeol/post/7952ecff-2a26-4852-bd29-91d5c931dfbb/image.png)

5. 3), 4) 과정에서 n-gram 방식으로 토크나이징한 서브그룹 정보를 이용해서 프론트엔드 인덱스를 생성한다.
   ![](https://images.velog.io/images/mulgyeol/post/f4f17a1e-9d94-440c-b48c-294646ecfd1c/image.png)

- 프론트엔드 인덱스에서 서브 그룹과 그룹 위치로 구성을 한다.
- 그룹 위치는 3) 과정 인덱스 대상 서브그룹에서 (서브그룹번호,서브그룹열번호) 로 구성된다.

### N-gram Full-text Index 검색 과정

1. 인덱스의 검색 과정은 생성되는 반대로 입력된 검색어를 n-gram의 n바이트 단위로 동일하게 자른다.
   ex) "마바사"를 검색할 경우
   (1) 마바 (2) 바사 를 동일하게 자른다.

2. 동일하게 자른 검색용 n-gram 토큰을 이용해서 프론트엔드 인덱스를 검색한다.
   ex) 프론트엔드 인덱스에서 마바(1,1), 바사(1,2) 정보를 검색한다. => 그룹위치의 행#이 마바에서 1, 바사에서 1이므로 서브그룹 1이 이 토큰에 연관.
   ![](https://images.velog.io/images/mulgyeol/post/e6448017-9914-4b29-aad3-b3389ee02e24/image.png)

3. 검색된 결과 정보를 백엔드 인덱스의 검색 대상 후보 리스트로 두고, 백엔드 인덱스를 통해서 최종 확인을 거쳐 일치하는 결과를 가져온다.

ex) 프론트엔드 인덱스에서 찾아낸 서브그룹 정보 1(="라마바사")로 문서 위치를 찾는다.

최종적으로 "마바사"의 위치는 서브그룹 정보 1인 "라마바사"의 정보 (0,1),(1,2)에서 행인 0,1의 문서0,문서1을 찾는다.

![](https://images.velog.io/images/mulgyeol/post/b6cbc314-c96f-446e-b941-88d1b4a0d558/image.png)

### Stop-word parser와 N-gram parser의 비교

1. 검색 속도

- 검색어 바이트 수가 많아질수록, 쿼리 실행 시간이 Stop-word parser > N-gram parser 이다. 즉, N-gram 검색이 빠르다.

2. 인덱스 용량

- 저장 데이터 바이트 수가 많아질수록, 인덱스 스토리지 사용량은 Stop-word parser < N-gram parser 이다. 즉, N-gram이 더 많은 용량을 사용한다.

### Full-text Search 검색 쿼리

: Full-text Search 쿼리는 다음 쿼리를 사용해야한다.

```
SELECT * FROM "테이블명" WHERE MATCH("검색할컬럼명"[, ...]) AGAINST('"검색할키워드식" "검색모드");
```

### 검색 모드의 종류

1. 자연어 검색(natural search)

- 검색 문자열을 단어 단위로 분리한 후, 해당 단어 중 하나라도 포함되는 행을 찾는다.

```
ex) SELECT * FROM “테이블명" WHERE MATCH (“검색컬럼명") AGAINST (‘맛집' IN NATURAL LANGUAGE MODE);
```

2. 불린 모드 검색(boolean mode search)

- 검색 문자열을 단어 단위로 분리한 후, 해당 단어가 포함되는 행을 찾는 규칙을 추가적으로 적용하여 해당 규칙에 매칭되는 행을 찾는다.
- 검색의 정확도에 따라 결과가 정렬되지 않는다.
- 구문 검색이 가능하다.
- 필수(+), 예외(-), 부분(\*), 구문(“ ") 연산자를 사용할 수 있다

```
ex) SELECT * FROM “테이블명" WHERE MATCH (“검색컬럼명") AGAINST (‘+대구*+닭*+맛집*' IN BOOLEAN MODE);
```

3. 쿼리 확장 검색(query extension search)

- 2단계에 걸쳐서 검색을 수행한다. 첫 단계에서는 자연어 검색을 수행한 후, 첫 번째 검색의 결과에 매칭된 행을 기반으로 검색 문자열을 재구성하여 두 번째 검색을 수행한다. 이는 1단계 검색에서 사용한 단어와 연관성이 있는 단어가 1단계 검색에 매칭된 결과에 나타난다는 가정을 전제로 한다.
