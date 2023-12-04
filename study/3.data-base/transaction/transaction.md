## 트랜잭션 관리와 무결성

### 트랜잭션 개념
#### 트랜잭션(transaction)이란?
> **DB의 나누어지지 않는 최소한의 처리 단위** <br>

#### 트랜잭션 종류
- COMMIT
  - 모든 작업들이 정상적으로 처리되었다고 확정하는 명령어로 처리과정을 DB에 영구저장하는 것이다.
  - Commit을 수행하면 이전 데이터가 완전히 UPDATE 된다.
- ROLLBACK
  - 작업 중 문제가 발생된 트랜잭션의 처리과정에서 발생한 변경사항을 취소하는 명령어이다.
  - 트랜잭션이 시작되기 이전의 상태로 되돌린다. 즉, 마지막 커밋을 완료한 시점으로 다시 되돌아간다.
  - 트랜잭션은 All or Nothing 방식으로 DML 명령어들을 처리한다.
 
#### 트랜잭션 작동 원리 (생명주기) <br>
<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FQ2V31%2Fbtr2sWO3f50%2FyEz1vGCsYmKs9ao4Vnj8KK%2Fimg.png" width="70%"/> <br>
- 활성화 (Activate)
  - 트랜잭션 실행 중 첫 상태이다.
  - 트랜잭션은 명령이 수행되는 동안 활성화된다.
- 부분 커밋 (Partially Committed)
  - 변경이 실행되었지만 데이터베이스는 아직 디스크에서 변경을 커밋하지않았다.
  - 데이터는 메모리 버퍼에만 저장되며 버퍼는 아직 디스크에 기록되지 않았다.
  - 중간에 실패할 수 있다. 
- 커밋 (Committed)
  - 이 상태에서는 모든 트랜잭션 업데이터가 데이터베이스에 영구적으로 저장된다.
  - 이 시점 이후에는 롤백할 수 없다.
- 실패 (Failed)
  - 트랜잭션이 실패하거나 활성 또는 일부만 커밋된 상태에서 중단된 경우 실패 상태가 된다.
- 종료 (Terminated)
  - 커밋되거나 이후의 중단됨(Aborted) 상태 이후의 마지막 상태이다.
  - 트랜잭션의 생명주기를 끝으로 표시한다.

### 트랜잭션 특성
#### ACID
- Atomicity
  - 원자성
  - All or Nothing
  - 모든 작업이 실행되거나, 혹은 모든 작업이 실행되지 않아야한다.
- Consistency
  - 일관성
  - 데이터베이스의 상태과 일관되어야 한다는 성질
  - 모든 트랜잭션이 종료된 후에 DB의 제약 조건을 모두 지키고 있는 상태가 되어야 한다.
- Isolation
  - 격리성, 고립성
  - 트랜잭션 A와 다른 트랜잭션들은 독립적으로 동작해야 한다.
  - 서로의 작업을 알지 못하는 상태여야 한다.
    > 📌 완전한 격리성을 지향해서 설계를 하면 안정성 측면에서는 가장 좋지만 성능은 떨어지게 된다.
    >    - 동시성 처리에 대한 성능이 떨어진다.
    >    - DeadLock이 발생할 확률이 높다
    >    - Lock으로 인한 시스템 오버헤드가 증가한다
    >    - DBMS는 MVCC을 구현하거나 Lock을 사용해야 한다. <br>
- Durability
  - 지속성
  - 하나의 트랜잭션이 성공적으로 수행되었다면, 해당 트랜잭션에 대한 로그가 남아야 한다는 성질을 말한다.
  - 트랜잭션 커밋 후 , 데이터베이스에 영구적으로 보존되어야 한다.

#### 📍트랜잭션의 격리 수준(Transaction Isolation Level)이란?
  특정 트랜잭션이 다른 트랜잭션에서 변경하는 데이터를 볼 수 있도록 허용할지 말지를 결정하는 것으로, 즉 격리 수준을 결정하는 행위는 각 트랜잭션의 격리 정도를 결정하여 안정성과 성능의 균형을 결정하는 행위라고 할 수 있다.
  - 트랜잭션 격리 수준 종류
    - READ_UNCOMMITTED
      - 각 트랜잭션에서의 변경 내용이 COMMIT이나 ROLLBACK 여부에 상관 없이 다른 트랜잭션에서 값을 읽을 수 있도록 하는 격리 수준이다.
      - 성능이 가장 좋은 방식이나, 안정성이 가장 떨어지는 방식으로 DIRTY READ 현상이 발생할 가능성이 크다. <br>
         ※DIRTY READ 란? 특정 트랜잭션이 진행 중일 때 다른 트랜잭션에서 접근 하도록 열어두는 것으로 일관성이 맞지 않는 결과를 도출한다.
      - 권장되지 않는 방식
      <img src = "https://velog.velcdn.com/images/chiyongs/post/1e5bf034-d244-497c-b349-340d9e88e384/image.png" width = "70%"/>
    - READ_COMMITTED
      - 각 트랜잭션에서의 변경 내용이 성공적으로 마무리되어 COMMIT 이되어야 하는 다른 트랜잭션이 접근 가능하도록 하는 격리 수준이다.
      - 대표적으로 Oracle DB에서 사용하고 있는 수준이며 가장 많이 사용되고 있다.
      - DIRTY READ 현상이 발생하지 않는다.
      - 커밋하기 전 다른 트랜잭션에서 조회시 UNDO 영역의 백업된 레코드에서 결과를 가져온다.
      - NON_REPEATABLE READ 문제가 발생할 수 있다.
    - *_NON_REPEATABLE READ_*
      - 한 트랜잭션 안에 똑같은 READ를 실행했을 때 항상 같은 결과를 가져오지 않는 현상이다.
      - Lock을 통해 동시성을 제어하는 방식에서는 Select 쿼리 실행 시 Read Lock을 획득하지 않거나, 획득한 Lock이 Select 쿼리를 실행하자마자 해제될 때 발생한다. <br>
      <img src = "https://velog.velcdn.com/images/chiyongs/post/a78d2b5e-618d-4de5-886c-653ed6dbef79/image.png" width = "70%"/> <br>
      - 해결 방법
        - Transactio A가 commit이나 rollback이 될 때까지 Transaction B의 실행을 지연하는 것이다.
        - Multiversion Concurrency Control(MVCC)을 사용한다. <br>
          > ✏️ MVCC(Multiversion Concurrency Control) 란? <br>
          > 다중 버전 동시성  제어의 약자로  DBMS에서 서로 다른 세션이 동일한 데이터에 접근했을 때 각 세션마다 스냅샷 이미지를 보장해주는 매커니즘이고 RDBMS에서 동시성을 높이기 위해 등장한 기술이다.
          > MVCC의 가장 큰 목적은 Lock을 사용하지 않는 일관된 읽기를 제공하기 위함이고, InnoDB에서는 Undo log를 이용하여 이 기능을 제공하고 있다. <br>
    - REPEATABLE_READ
      - 트랜잭션 범위 내에서 조회한 내용이 항상 동일함을 보장하는 트랜잭션 격리 수준이다.
      - MySQL의 InnoDB에서 기본으로 사용하는 트랜잭션 격리 수준이다.
      - Phantom Read 문제가 발생할 수 있다.
    - *_PHANTOM READ_*
      -  다른 트랜잭션에서 수행한 변경 작업에 의해 레코드가 보였다가 안 보였다가 하는 현상이다.
      -  NON_REPEATABLE READ의 경우 중 하나이며 , Transaction A가 범위를 조회하는 쿼리를 반복적으로 사용할 때, 그 쿼리들 사이에서 Transaction B가 해당 쿼리를 만족하는 새로운 행을 생성했을 때 발생한다.
      -  InnoDB는 index-row locking 과 gap locking을 섞은 next-key locking 알고리즘을 사용하고 있어 Repeatable Read에서도 Phantom Read를 예방할 수 있다.
      <img src = "https://velog.velcdn.com/images/chiyongs/post/3ac94f8c-8321-41af-97b7-c25ebd652ee3/image.png" width = "70%"/>
    - SERIALIZABLE
      - 모든 작업을 하나의 트랜잭션에서 처리하는 것과 같이 동작하는 가장 높은 트랜잭션 격리 수준이다.
      - 한 트랜잭션에서 읽고 쓰는 레코드는 다른 트랜잭션에서 절대 접근할 수 없습니다.
      - PHANTOM READ 문제는 발생하지 않는다.
      - DeadLock에 걸릴 가능성이 존재하고 동시성 처리 성능이 저하되기 떄문에 신중하게 사용해야 한다. 

<br><br>
===
<br>

### Lock
#### Lock이란?
> **트랜잭션 처리의 순차성을 보장하기 위한 방법** <br>
#### Lock의 종류
- 공유 lock
  - 공유 잠금한 트랜잭션이 데이터 항목에 대하여 읽기만 가능하다.
  - 다른 트랜잭션도 읽기만을 실행 할 수 있는 형태이다.
- 전용(베타) lock
  - 전용 잠금한 트랜잭션은 데이터 항목에 대하여 읽기와 쓰기 모두 가능하다.
  - 다른 트랜잭션은 읽기와 기록 모두 할 수 없다.
#### Lock의 설정 범위 (level)
- 데이터베이스
  - 전체 데이터베이스를 기준으로 lock을 하는 것이다.
  - 1개의 세션만이 DB의 데이터에 접근이 가능하다.
  - 일반적으로 사용하지 않으나 DB의 소프트웨어 버전을 올린다던지 주요한 DB의 업데이트에 사용한다. 
- 파일
  - 데이터베이스 파일 기준으로 lock을 설정한다.
  - 잘 사용하지 않는다.
- 테이블
  - 테이블 기준으로 Lock을 설정한다.
  - 테이블의 모든 행을 업데이트 하는 등의 전체 테이블에 영향을 주는 변경을 수행할 때 유용하다.
  - DDL 구문과 함께 사용되며 DDL Lock이라고도 한다.
- 페이지와 블럭
  - 파일의 일부인 페이지와 블록을 기준으로 Lock을 설정한다.
  - 잘 사용하지 않는다.
- 컬럼
  - 컬럼을 기준으로 Lock을 설정할 수 있다.
  - Lock 설정 및 해제의 리소스가 많이 들기 때문에 일반적으로 사용되지 않는다.
  - 지원하는 DBMS도 많지 않다.
- 행
  - 1개의 행을 기준으로 Lock을 설정한다.
  - DML에 대한 Lock으로 가장 일반적으로 사용하는 Lock이다. 

<br><br>
===
<br>

### 동시성 제어
#### 동시성 제어란?
> **DBMS가 다수의 사용자 사이에서 동시에 작용하는 다중 트랜잭션의 상호간섭작용에서 Database를 보호하는 것을 의미한다.** <br>
<img src = "https://velog.velcdn.com/images/choidongkuen/post/bbac64f0-1b91-4b1c-89d9-16974323e3a8/image.png" width = "70%"/> <br>
한번에 들어온 복수의 트랜잭션을 직렬화 하는 수행을 보장한다. <br>

#### 동시성 제어 하지 않을 경우 문제점
- 갱신 손실
  - 트랜잭션이 동일 데이터를 동시에 갱신 할 경우 발생하며 특정 트랜잭션의 갱신 내용이 반영되지 않는 오류
- 현황파악 오류 (Dirty Read)
  - 트랜잭션 과정에서 갱신을 하고 트랜잭션 종료하기 전에 다른 트랜잭션이 참조함으로써 조회 결과의 정확성이 깨지는 오류
- 모순성
  - 두 트랜잭션이 동시에 실행할 때 DB가 일관성이 없는 상태로 남는 오류
- 연쇄 복귀
  -  복수의 트랜잭션이 동일한 데이터 공유시 특정 트랜잭션이 처리를 취소할 경우 다른 트랜잭션은 해당 취소를 적용하지 못하는 오류

#### 동시성 제어 기법 종류
- 락킹 기법
  - 트랜잭션이 사용하는 데이터 자원에 대하여 상호 배제 기능을 제공하는 기법이다.
  - 상호 배제는 트랜잭션이 데이터 항목에 대한 lock을 설정한 트랜잭션이 unlock 할 때까지 데이터를 독점적으로 사용할 수 있는 것을 의미한다.
  - 허점
    - 락을 걸거나 해제하는 시점에서 제한을 두지 않으면 두 개의 트랜잭션이 동시에 실행될 때 데이터의 일관성이 깨질 수 있다.
    - 즉, 중간에 락의 적용이 되지않기 때문에 2단계 로킹을 진행해야한다. (<a href="https://swingswing.tistory.com/44"> 2단계 로킹 규약 </a>)
  - 블로킹 현상 <br>
    <img src = "https://velog.velcdn.com/images/choidongkuen/post/2ca6baa5-c8d3-482e-85a5-d26ce2a265d9/image.png" width="50%"/>
    - lock 간의 경합이 발생하여 특정 트랜잭션이 작업을 진행하지 못하고 멈춰선 상태를 말한다.
    - 공유 lock - 공유 lock은 발생하지 않으나 전용 lock - 전용 lock, 공유 lock - 전용 lock, 전용 lock - 공유 lock 끼리는 블로킹이 발생한다.
    - 어플리케이션 성능에 좋지 않은 영향을 끼치기 때문에 블로킹을 최소화 해야한다.
    > 📌 블로킹 해결 방법
    > 1. 한 트랜잭션의 길이를 너무 길게 설계하는 것은 블로킹의 확률을 증가시키므로 가능한 짧게 설계하기
    > 2. 설계 시, 같은 데이터를 동시에 갱신하는 트랜잭션을 수행하지 않도록 하기
    > 3. 트랜잭션 격리성 수준을 불필요하게 상향 조정하지 않기
    > 4. 쿼리를 너무 오랜시간 동안 잡아두지 않도록 적절한 튜닝을 진행하기 <br>
  - 교착 상태 <br> 
    <img src = "https://velog.velcdn.com/images/choidongkuen/post/7aa7f270-d9f7-424a-8e0b-01aabee0ddc1/image.png" width="50%"/> <br>
    - 두 트랜잭션이 각각 Lock을 설정하고 서로의 Lock에 접근하여 값을 얻어오려고 할 때 이미 각 트랜잭션에 의해 Lock이 설정되어 있기 때문에 트랜잭션이 영원히 처리가 되지안헥 되는 상태를 말한다.
    - 두 트랜잭션이 공유락을 사용한다면 교착상태는 발생하지 않는다.
- 타임 스탬프
  - 트랜잭션의 순서대로 시간 스탬프를 지정하여 동시성 제어의 기준으로 사용되는 개념이다.
  - 특징
    - 시스템에 들어오는 트랜잭션의 순서대로 시간 스탬프를 지정하여 동시성 제어의 기준을 사용한다.
    - 교착상태를 방지 할 수 있으나 RollBack 발생률이 높고 연쇄 복귀를 초래할 수 있다.
  - 타임 스탬프 종류
    - System 시계 사용법
      - 트랜잭션이 시스템에 들어올 때의 System Clock 적용한다.
    - 논리적인 계수기 사용법 
      - 트랜잭션의 시간 스탬프는 그 트랜잭션이 시스템에 들어올 때의 시스템 계수기의 값과 같은 개념이다.
- 적합성 검증 (낙관적 검증 기법) <br>
  <img src = "https://velog.velcdn.com/images/choidongkuen/post/380057f8-e709-4d4f-9c1a-71bc1f3c7618/image.png" witdh = "50%"/> <br>
  - 트랜잭션이 어떠한 검증도 수행하지 않고, 일단 트랜잭션을 수행하고 종료시 검증을 수행하여 데이터베이스에 반영하는 방법이다.
  - 만약 트랜잭션의 길이가 길어 장기적으로 트랜잭션이 진행되었는데 RollBack 되는 경우 자원이 낭비된다.
  - 동시 사용 빈도가 낮은 시스템에서 사용한다.
- MVCC(다중버전 동시성 제어 기법)
- 동시성 제어 기법 종류 장단점
  | 기법 | 장점 | 단점 |
  |:---|:---|:---|
  | Locking 기법 | - 데이터 오류 가능성 사전에 예방<br>- 구현하기 쉬움 | 1. lock 대기 시간 => 동시성 저하 <br>2. 교착 상태 발생 가능성 존재|
  | TimeStamp | - 교착 상태 발생 가능성 없음<br>- 트랜잭션 대기 시간 없음 | 1. Rollback 발생 확률이 높음|
  | 적합성 검증 | - 동시 처리 능력 증가<br>- 트랜잭션 대기 시간 없음 | 1. 장기 트랜잭션 철회시 자원 낭비|
  | MVCC | - 트랜잭션 대기 시간 없음<br>- 다른 트랜잭션이 해당 데이터를 수정해도 영향받지 않음 | 1. 사용하지 않는 데이터를 정리하는 시스템이 필요 <br>2. 데이터 버전의 충돌이 일어날 수 있음 <br>3. UNDO 블록, I/O 등의 부가적인 오버헤드 발생|

📌 동시성 제어 기법 추가 참고 내용 : <a href="https://velog.io/@ha0kim/%EB%8F%99%EC%8B%9C%EC%84%B1-%EC%A0%9C%EC%96%B4" >동시성제어</a>

<br><br>
===
<br>

### 장애와 회복
#### 장애
> **트랜잭션을 더 이상 수행할 수 없는 시스템 중단상태를 의미한다.** <br>
- 종류
  - 실행 장애
    - 데이터베이스 연산 실패 
  - 트랜잭션 장애
    - 프로그램 실행 중지 
  - 시스템 장애
    - 하드웨어 결함
  - 미디어 장애
    - 디스크 장치 결함 

#### 회복
> **장애로 인해 제대로 실행되지 못한 DB를 이전 상태로 복구하는 작업이다.** <br>
- 종류
  - 덤프(Dump)
    - 주기적으로 데이터베이스 전체를 다른 저장장치에 복제하는 것이다.  
  - 로그(Log)
    - 데이터베이스가 변경될 때마다 예전값과 변경값을 기록해 두는 것이다. 
  - 재실행(Redo)
    - 장애 발생 전, 이미 완료된 트랜잭션을 재실행한다.
  - 취소(Undo)
    - 장애 발생 후, 진행 중이던 트랜잭션을 취소하고 이전 상태로 복구한다. 
- 회복 작업을 위해서는 덤프와 로그가 있어야만 가능하다. (Redo)
  - 장애 발생 시, 가장 최근의 복사본(덤프)을 가져 온 후 로그를 보며 복구한다. 복사본에는 반영되지 않았지만, 로그상으로 완료된 트랜잭션은 재실행해서 DB에 반영한다.
- 복사본에 장애 발생 후 진행 중이던 트랜잭션이 있다면, 반대로 로그를 보며 취소 작업을 실시한다. (Undo)

#### 회복 작업에서의 트랜잭션 (원자성과 영속성) <br>
<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FEb6YL%2FbtscAp0PsH7%2FwhbCuGYs1qcERBnKUTAXZ0%2Fimg.png" width="50%"/> <br>
장애 발생 시점을 기준으로 트랜잭션 완료 여부에 따라 Redo와 Undo로 나누어진다.
- Redo를 하는 이유
  - 트랜잭션의 영속성을 만족하기 위해서 이다.
  - 트랜잭션은 일단 완료되면 그 결과를 영구 기억장치에 저장하고 보존해야한다. 이를 위해 다시 Redo 작업을 실시해서 저장한다. 
- Undo를 하는 이유
  - 트랜잭션은 전부 실행되거나, 아예 실행되지 않아야 한다. 장애로 인해 중간 결과만 남은 경우, 다시 실행 이전으로 복구해야 한다.  

#### 데이터베이스 회복 작업
- 즉시 갱신
  > 트랜잭션 실행 중 변경내역을 DB에 즉시 반영하는 방식이다.
- 지연 갱신
  > 트랜잭션이 부분 완료될 때까지 (commit만 남겨둘 때까지) DB 반영을 지연시키는 방식
  <br>

> **출처** <br>
> <a href="https://velog.io/@wonizizi99/CS-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-Transaction">트랜잭션이란,  트랜잭션 종류</a><br>
> <a href="https://puleugo.tistory.com/142">트랜잭션 작동 원리</a><br>
> <a href="https://velog.io/@choidongkuen/%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98Transaction-%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B4%85%EC%8B%9C%EB%8B%A4">ACID, 트랜잭션격리성수준</a><br>
> <a href="https://velog.io/@chiyongs/Transaction-Isolation-Level-%EA%B7%B8%EA%B2%8C-%EB%AD%94%EB%8D%B0">트랜잭션격리성수준</a><br>
> <a href="https://sabarada.tistory.com/121">Lock</a><br>
> <a href="https://velog.io/@choidongkuen/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EB%8F%99%EC%8B%9C%EC%84%B1-%EC%A0%9C%EC%96%B4Concurrency-Controll">동시성 제어</a><br>
> <a href="https://star7sss.tistory.com/824">장애, 회복</a><br>