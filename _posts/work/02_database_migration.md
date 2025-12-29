---
title: Database Migration(AWS RDS -> Azure MySQL)
date: 2025-11-01 1:35:23 +0900
updated: 2025-11-01 1:35:23 +0900
category: work
tags: mysql, DMS, CDC
permalink: /work/improve_2
---
# Database Migration

## 개요

- **프로젝트 목적** : 데이터베이스 마이그레이션
- **프로젝트 기간** : 2025.09 ~ 2025.10

## 마이그레이션 방안

### 1. Dump -> Load

- 데이터 베이스 인스턴스(EC2)에서 데이터를 덤프하여 새로운 데이터 베이스 인스턴스(EC2)에 로드한다.
- 장점 : 메뉴얼로 진행하여 구현이 쉽다.
- 단점 : 데이터 베이스 인스턴스(EC2)에서 데이터를 덤프하여 새로운 데이터 베이스 인스턴스(EC2)에 로드하는 과정에서 데이터 손실이 발생할 수 있다.

### 2. Azure Database Migration Service

- CDC를 활용하여 실시간 데이터 베이스를 업데이트 후 원한는 시점에 DB주소를 변경하면 된다.
- 장점 : 짧은 시간 중단만으로도 서비스 재개가 가능하다.
- 단점 : 비용이 추가적으로 소요된다.

### 3. Dual write

- AWS, Azure에 데이터 베이스 모두에 write를 진행한다.
- 장점 : 데이터 손실이 없다.
- 단점 : 마이그레이션 중에 점검을 2번 실행 해야한다.

## Azure Database Migration Service

### 1. CDC(Change Data Capture) 설정 및 동작 원리

![1767005702638](https://file+.vscode-resource.vscode-cdn.net/Users/admin/git/kknaks.github.io/_posts/work/image/02_1_migration_detail/1767005702638.png)

### 2. AWS CDC 설정

#### Binary Log 확인

```sql
-- Binary Log 활성화 확인
SHOW VARIABLES LIKE 'log_bin';
-- 결과: on 으로 출력 확인
```

#### Binlog Retention 설정

```sql
-- 현재 설정 확인
CALL mysql.rds_show_configuration;
-- binlog retention hours가 NULL이 아닌 2일 이상인지 확인

-- Retention 기간 설정 (168시간 = 7일)
CALL mysql.rds_set_configuration('binlog retention hours', 168);
```

---

### 3. 마이그레이션 단계

#### 1단계: Full Load (초기 복사)

```
[AWS RDS MySQL]
    ↓
전체 테이블 스캔
    ↓
데이터 복사 (Dump & Restore 방식)
    ↓
[Azure MySQL]
```

#### 2단계: CDC (실시간 변경 캡처) ⭐

```
[AWS RDS MySQL]
    ↓
Binary Log 읽기 (binlog)
    ↓
INSERT/UPDATE/DELETE 이벤트 캡처
    ↓
변경사항만 실시간 전송
    ↓
[Azure MySQL]
```

---

### 3.1 Binary Log 동작 원리

#### 1) 쿼리 실행 시 Binary Log 기록

**AWS RDS에서 쿼리 실행:**

```sql
INSERT INTO users (name, email) 
VALUES ('John', 'john@example.com');
```

**Binary Log에 기록되는 내용:**

```sql
### INSERT INTO `myapp`.`users`
### SET
###   @1=1001                      /* INT meta=0 nullable=0 */
###   @2='John'                    /* STRING(255) meta=65535 nullable=1 */
###   @3='john@example.com'        /* STRING(255) meta=65535 nullable=1 */
###   @4='2025-10-13 10:30:45'     /* DATETIME meta=0 nullable=1 */
```

#### 2) Binary Log 파일 확인

```sql
-- Binary Log 파일 목록 조회
SHOW BINARY LOGS;
```

**결과:**

```
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 | 154       |
| mysql-bin.000002 | 25836     |
+------------------+-----------+
```

#### 3) Binary Log 이벤트 조회

```sql
-- 특정 Binary Log 파일의 이벤트 확인
SHOW BINLOG EVENTS IN 'mysql-bin.000002';
```

---

### 3.2 DMS CDC 동작 과정(상세설정)

> 📌 상세 내용은 [여기](/work/02_1_migration_detail)를 참고하세요.

#### 1) Step 1: Binary Log 읽기

DMS가 AWS RDS의 Binary Log를 실시간으로 모니터링합니다.

```
mysql-bin.000001
mysql-bin.000002  ← DMS가 이 로그들을 읽음
```

#### 2) Step 2: 이벤트 파싱

**Binary Log Event:**

```
- Timestamp: 2025-10-13 10:30:45
- Event Type: WRITE_ROWS_EVENT (INSERT)
- Table: myapp.users
- Data: {id: 1001, name: 'John', email: 'john@example.com'}
```

DMS가 이벤트를 분석:

> "users 테이블에 INSERT가 발생했구나!"

#### 3) Step 3: Azure에 적용

DMS가 Azure MySQL에 자동으로 쿼리 실행:

```sql
INSERT INTO users (id, name, email, created_at)
VALUES (1001, 'John', 'john@example.com', '2025-10-13 10:30:45');
```

#### 4) Step 4: 포지션 저장

**DMS 내부 상태:**

```
- Current Position: mysql-bin.000002:25836
- Last Applied: 2025-10-13 10:30:45
- Latency: 0.5 seconds
```

> **장애 복구:** 네트워크 장애 등으로 중단되어도 저장된 포지션부터 다시 시작 가능!
