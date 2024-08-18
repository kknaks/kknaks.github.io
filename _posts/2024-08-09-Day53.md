# DB 프로그래밍
## Native API
- 판매사에서 만든 API
- Oracle, MSSQL, MySQL 상호 호환이 불가능
- ODBC로 통일

## ODBC
- ODBS API규칙을 정의 하여 각 판매사별로 규칙을 통일
- 명령어를 동일하게 적용가능
- 각 ODBS Driver을 설치 해야한다.
  ![image](https://github.com/user-attachments/assets/b372acd5-a081-43fb-a9ae-82af3a6f0105)

## JDBC
- ODBS의 기능을 JVM에서 동일하게 구현한 드라이버 
### Type1 : ODBS-JDBC Bridge Driver
- JRE에 기본적으로 포함된 드라이버
- ODBC API를 호출(C함수)
