# MySQL
## MySQL 기초
<!-- TOC -->
  * [DNL(Data Manipulation Language)](#dnldata-manipulation-language)
    * [insert](#insert)
    * [update](#update)
    * [delete](#delete)
    * [autocommit](#autocommit)
  * [DQL(Data Query Language)](#dqldata-query-language)
    * [select](#select)
    * [연산자](#연산자)
    * [날짜 다루기](#날짜-다루기)
<!-- TOC -->

### DNL(Data Manipulation Language)
- 데이터의 등록, 변경, 삭제를 다루는 SQL문법

#### insert
- **기능** : 데이터를 입력할 때 사용하는 문법
- 전체 컬럼값 입력하기
    ```mysql
    // 테이블명만 명시
    insert into 테이블명 values(값1, 값2, ....)
    
    // 테이블명(컬럼명) 명시
    insert into 테이블명(컬럼,컬럼,....) values(값,값,...)
    ```
  - 테이블명 : 컬럼의 순서에 맞춰 명시 해야한다.
  - 테이블명(컬럼명) : 컬럼과 값의 쌍을 순서와 상관없이 대입 가능하지만, not null은 반드시 명시해야한다.

- 여러개의 값을 한번에 insert하기 
  - 컬럼명과 값의 갯수 및 형식을 맞추면 여러개의 데이터도 한번에 insert가 가능하다.
  ```mysql
  insert into 테이블명(컬럼명, 컬럼명,....) values
    (값,값,.....),
    (값,값,.....),
    (값,값,.....);
  ``` 
  
- 데이터를 선택하여 결과에 insert하기 
  - select을 통하여 데이터를 선택하고 해당 값을 insert 할 수 있다.
  ```mysql
  insert into 테이블명(컬럼,컬럼)
    select 컬럼, 컬럼 from 테이블명 where 조건;
  ```
  - select 결과의 컬럼명과 insert 테이블의 컬럼명이 같을 필요는 없다.
  - 결과의 컬럼 타입과 insert 하려는 컬럼의 타입이 같거나 입력 할 수 있는 타입이어야 한다.

#### update
- **기능** : 등록된 데이터를 변경할 때 사용하는 명령어 이다.
- 데이터 값 변경하기
  ```mysql
  update 테이블명 set 컬럼명=값, 컬럼명=값,......where 조건;
  update 테이블명 set 컬러명=값,......;
  ```
  - 조건을 입력하지 않으면 전체 테이블에 대한 값을 수정한다.

#### delete
- **기능** : 등록된 데이터를 삭제할 때 사용하는 명령어 이다.
- 데이터 값 변경하기
  ```mysql
  delete from 테이블명 where 조건;
  delete from 테이블명;
  ```
  - 조건을 입력하지 않으면 전체 테이블을 삭제한다.


#### autocommit
- mysql은 autocommit의 기본 값이 true이다. 
- 데이터를 삽입/변경/삭제 시 자동으로 commit이 된다.
  ![image](https://github.com/user-attachments/assets/c3cd7480-333b-49a4-8e72-869dc866d1fa)

- 수동 커밋으로 변경하기 
  ```mysql
  set autocommit = false;
  ```
  - 삽입/변경/삭제한 데이터를 서버에 올리려면 commit을 수행한다.
  - 삽입/변경/삭제한 데이터를 취소하려면 rollback을 수행한다.
  ![image](https://github.com/user-attachments/assets/1fbfa2ec-94e9-42a9-8423-00f424c0f294)

  - autocommit의 true와 false의 차이
  - autocommit이 false일 경우
  - 조회 시 마지막으로 커밋한 테이블과 임시저장소에 저장된 내용이 출력된다.
  - 커밋이후에 다른 서버에서 변경한 내용은 조회가 불가능하다. 
  - 다른 서버는 수동commit서버의 수정내용을 commit 전 까지는 확인 할 수 없다.
    ![image](https://github.com/user-attachments/assets/c48a9139-74a5-42c3-932f-5dedd46006ff)

### DQL(Data Query Language)
- 데이터 조회를 다루는 SQL문법

#### select
- 테이블의 데이터를 조회 할 때 사용하는 문법이다.

- 모든 데이터 조회하기
  ```mysql
  select * from 테이블명;
  ```

- 특정 칼럼 데이터 조회하기
  - 특정 칼럼의 값만 조회하는 것을 프로젝션이라고 한다. 
  ```mysql
  select 컬럼명, concat(컬럼명,컬럼명) from 테이블명;
  ```

- 특정 칼럼 병합하여 데이터 조회하기 
  ```mysql
  select 컬럼명, concat(컬럼명,컬럼명) from 테이블명;
  ```

- 특정 칼럼 병합하고 컬럼명을 바꿔서 데이터 조회하기
  - as는 생략가능하다. 
  ```mysql
  select 컬럼명 as 별명, concat(컬럼명,컬럼명) as 별명 from 테이블명;
  select 컬럼명 별명, concat(컬럼명,컬럼명) 별명 from 테이블명;
  ```

- 조회할 조건을 지정하기
  - 조건을 지정해서 결과를 선택하는 것을 셀렉션이라고 한다. 
  ```mysql
  select * from 테이블명 where 조건;
  ```

#### 연산자
- 기본연산자
  - OR  : 두 조건 중에 참인 것이 있으면 조회 결과에 포함시킨다.
  - AND : 두 조건 모두 참일 때만 조회 결과에 포함시킨다.
  - NOT : 조건에 일치하지 않을 때만 결과에 포함시킨다.
  ```mysql
  //or
  select * from 테이브명 where 조건 or 조건;
  
  //and
  select * from 테이브명 where 조건 and 조건;
  
  //not
  select * from 테이브명 where not 컬럼명 = 값;
  select * from 테이브명 where 컬럼명 != 값;
  select * from 테이브명 where not 컬럼명 <> 값;
  ```
- null
  - null 조건 검색은 is null/ is not null(not ~ is null)로 지정한다.
  ```mysql
  //is null
  select * from 테이블명 where 컬럼명 is null;
  
  //is not null
  select * from 테이블명 where 컬럼명 is not null;
  select * from 테이블명 where not 컬럼명 is null;
  ```

- 사칙연산자
  - +, -, *, /, % 연산자를 사용할 수 있다.
  ```mysql
  select (4 + 5), (4 - 5), (4 * 5), (4 / 5), (4 % 5);
  ```
  <img width="650" alt="image" src="https://github.com/user-attachments/assets/1df8f616-ef9e-4951-978f-deb15ff3c0ad">

- 비교연산자
  - =, !=, >, >=, <, <=, <>
  ```mysql
  select (4=5), (4!=5), (4>5), (4>=5), (4<5), (4<=5), (4<>5);
  ```
  <img width="732" alt="image" src="https://github.com/user-attachments/assets/e17d58fc-c683-4d59-8201-2e9df40a31a4">
 
  - between 값 and 값
  ```mysql
  select 5 between 3 and 5;
  ```
  <img width="357" alt="image" src="https://github.com/user-attachments/assets/1222aeb0-4168-42fe-b42d-70b36525c9bc">

- like
  - 문자열을 비교할 때 사용한다. 
  - % : 0개 이상의 문자
  - _ : _갯수당 1자리 글자 탐색 ( ex))_ : 1개 문자, __ : 2개 문자 )
    ```mysql
    //'값%' : 값으로 시작하는 모든 문자열 
    select * from 테이블명 like '값%';
    
    // '%값%' : 값이 포한된 모든 문자열
    select * from 테이블명 like '%값%';
    
    // '값_' : 값으로 시작하고 뒤에 1개의 문자가 붙은 문자열
    select * from 테이블명 like '값_';
    ```

#### 날짜 다루기
- 특정 날짜 찾기
  ```mysql
  select * from 테이블명 where 날짜컬럼 = 'yyyy-mm-dd';
  ```

- 특정 기간 조회
  ```mysql
  select * from test1 where regdt between '2022-11-1' and '2022-12-31';
  select * from test1 where regdt >= '2022-11-1' and regdt <= '2022-12-31';
  ```
- 날짜를 다루는 연산자와 함수
  - now() : 현재 날짜 및 시간 알아내기
  - curdate() : 현재 날짜 알아내기
  - curtime() : 현재 시간 알아내기
  ```mysql
  /* 현재 날짜 및 시간 알아내기 */
  select now();
  
  /* 현재 날짜 알아내기 */
  select curdate();
  
  /* 현재 시간 알아내기 */
  select curtime();
  ```
  
- 주어진 날짜, 시간에서 날짜만 뽑거나 시간만 뽑기
  ```mysql
  select 날짜컬럼, date(날짜컬럼), time(날짜컬럼) from 테이블명;
  ```

- 특정 날짜에 시,분,초,일,월,년을 추가하거나 빼기
  ```mysql
  date_add(날짜데이터, interval 값 년/월/일/시간/분/초);
  date_sub(날짜데이터, interval 값 년/월/일/시간/분/초);
  ```
- 두 날짜 사이의 간격을 알아내기
  - datediff(날짜1, 날짜2); 
  ```mysql
  select datediff(curdate(), 'yyyy-mm-dd');
  ```
- 날짜에서 특정 형식으로 값을 추출하기
  - date_format(날짜, 형식)
  ```mysql
  select regdt, date_format(regdt, '%m/%e/%Y') from test1; /* 09/7/2022 */
  select regdt, date_format(regdt, '%M/%d/%y') from test1; /* September/07/17 */
  select regdt, date_format(regdt, '%W %w %a') from test1; /* Thursday 4 Thu */
  select regdt, date_format(regdt, '%M %b') from test1; /* September Sep */
  select now(), date_format(now(), '%p %h %H %l'); /* PM 01 13 1 */
  select now(), date_format(now(), '%i %s'); /* 05 45 */
  ```
  
- 문자열을 날짜 값으로 바꾸기
  - str_to_date('날짜','데이트포맷') 
  ```mysql
  select str_to_date('11/22/2022', '%m/%d/%Y');
  select str_to_date('2022.2.12', '%Y.%m.%d');
  ```

- 날짜 값을 저장할 때 기본 형식은 yyyy-MM-dd이다.
  - 다른형식의 날짜를 올리기위해서는 format을 변환 해야한다.
  ```mysql
  insert into test1 (title, regdt) values('aaaa', '2022-11-22');
  
    /* 다음 형식의 문자열을 날짜 값으로 지정할 수 없다.*/
  insert into test1 (title, regdt) values('bbbb', '11/22/2022');
  
  /* 특정 형식으로 입력된 날짜를 date 타입의 컬럼 값으로 변환하면 입력할 수 있다.*/
  insert into test1 (title, regdt) values('bbbb', str_to_date('11/22/2022', '%m/%d/%Y'));

  ```
  


