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

### DB서버 생성
### DB서버 스펙 변경
### DMS 인스턴스 생성
### 프로젝트 생성
#### aws cdc 설정

    > 
    > SHOW VARIABLES LIKE 'log_bin'; 
    - on 으로 출력 확인
    > CALL mysql.rds_show_configuration;
    - binlog retention hours NULL이 아닌 2일 이상인지 확인 
    > CALL mysql.rds_set_configuration('binlog retention hours', 168);

1단계: Full Load (초기 복사)
[AWS RDS MySQL]
    ↓
  전체 테이블 스캔
    ↓
  데이터 복사 (Dump & Restore 비슷)
    ↓
[Azure MySQL]

2단계: CDC (실시간 변경 캡처) ⭐
[AWS RDS MySQL]
    ↓
  Binary Log 읽기 (binlog)
    ↓ 
  INSERT/UPDATE/DELETE 이벤트 캡처
    ↓
  변경사항만 실시간 전송
    ↓
[Azure MySQL]

-- AWS RDS에서 이런 쿼리 실행하면
INSERT INTO users (name, email) VALUES ('John', 'john@example.com');

-- Binary Log에 이렇게 기록됨
### INSERT INTO `myapp`.`users`
### SET
###   @1=1001 /* INT meta=0 nullable=0 */
###   @2='John' /* STRING(255) meta=65535 nullable=1 */
###   @3='john@example.com' /* STRING(255) meta=65535 nullable=1 */
###   @4='2025-10-13 10:30:45' /* DATETIME meta=0 nullable=1 */



실제 동작 과정
Step 1: Binary Log 읽기
bash# AWS RDS에서 Binary Log 확인
mysql> SHOW BINARY LOGS;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 | 154       |
| mysql-bin.000002 | 25836     |
+------------------+-----------+

# DMS가 이 로그들을 읽음
mysql> SHOW BINLOG EVENTS IN 'mysql-bin.000002';
Step 2: 이벤트 파싱
Binary Log Event:
- Timestamp: 2025-10-13 10:30:45
- Event Type: WRITE_ROWS_EVENT (INSERT)
- Table: myapp.users
- Data: {id: 1001, name: 'John', email: 'john@example.com'}

DMS가 이걸 파싱:
"users 테이블에 INSERT가 발생했구나!"
Step 3: Azure에 적용
sql-- DMS가 Azure MySQL에 자동으로 실행
INSERT INTO users (id, name, email, created_at) 
VALUES (1001, 'John', 'john@example.com', '2025-10-13 10:30:45');
Step 4: 포지션 저장
DMS 내부 상태:
- Current Position: mysql-bin.000002:25836
- Last Applied: 2025-10-13 10:30:45
- Latency: 0.5 seconds

네트워크 장애 등으로 중단되어도
이 포지션부터 다시 시작 가능!



stock-db
- 엘라스틱
- 알파파인더 dev_new
- 알파파인더 live
- 알파파인더 batch
- 에어플로우