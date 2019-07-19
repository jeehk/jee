# PART2 - 빅데이터 분석 (07/18)

#### 1) 
![user_training](/images/pt2_1.png)
#### 2) 
#### 3) 
#### 4) 
#### 5) 
#### 6) 
#### 7) 
#### 8) 
#### 9) 
#### 10) 
#### 11) 




#### 1) Comparison of active account's balance to avg balance
#### Make directory and sql file
```
mkdir problem1
vi solution.sql
```
#### sql statement
```
use problem1;

select a.id,
       a.type,
       a.status,
       a.amount,
       (a.amount - b.avg_amount) as difference
from account as a,
(select avg(amount) as avg_amount from account) as b
where a.status='Active';
```
#### 1.1 hive CLI tool로 쿼리파일 실행 및 Job Browser 확인
```
hive -f problem1/solution.sql
```
### 2) Creating 'solution' table from HDFS in parquet file format
#### 2.1 데이터베이스 생성
```
create database problem2;
```
#### 2.2 parquet mode file 읽어서 'solution'이라는 External 테이블 생성
```
use problem2;

DROP TABLE IF EXISTS solution;
CREATE EXTERNAL TABLE solution (
  id INT,
  fname STRING,
  lname STRING,
  address STRING,
  city STRING,
  state STRING,
  zip STRING,
  birthday STRING,
  hireday STRING
)
STORED AS PARQUET
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
LOCATION '/user/training/problem2/data/employee/';
```

#### 2.3 테이블 속성 및 HDFS file 위치 확인


### 3) Customers who have negative balance
#### 3.1 Negative balance를 가진 고객정보를 solution 테이블로 생성
```
use problem3;

create table solution as
select a.id as id,
       a.fname as fname,
       a.lname as lname,
       regexp_replace(a.hphone,"\\(|\\)| |-","") as hphone
from customer as a join account as b
                            on a.id = b.custid
                            and b.amount < 0
```

### 4) Combining two customer lists into a single dataset
#### 4.1 HDFS directory의 두개의 employee 파일을 각각 External 테이블로 생성
```
create database problem4;
use problem4;

CREATE EXTERNAL TABLE  employee1 (
  id STRING,
  fname STRING,
  lname STRING,
  address STRING,
  city STRING,
  state STRING,
  zipcode STRING
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LOCATION '/user/training/problem4/data/employee1/';

CREATE EXTERNAL TABLE employee2 (
  id STRING,
  unknown_num STRING,
  fname STRING,
  lname STRING,
  address STRING,
  city STRING,
  state STRING,
  zipcode STRING
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
LOCATION '/user/training/problem4/data/employee2/';
```

#### 4.2 조건에 부합되는 결과만 테이블로부터 HDFS파일로 저장
```
INSERT OVERWRITE DIRECTORY '/user/training/problem4/solution/'
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
  Select
    id,fname,lname,address,city,state,substr(zipcode,0,5) as zipcode
    from employee1 where state ='CA'
union all
Select
    id,fname,lname,address,city,state,zipcode
    from employee2 where state ='CA';
```

### 5) Customers and Employees who live in Palo Alto, CA
#### 5.1 디렉토리 생성 및  sql파일 작성
```
mkdir problem5
vi solution.sql

use problem5;

select fname, lname, city, state from employee
  where city ='Palo Alto'
    and state ='CA'
union all
select fname, lname, city, state from customer
  where city ='Palo Alto'
    and state ='CA'
```
#### 5.2  Hive CLI tool로  sql파일  실행
```
cd problem5
hive -f solution.sql
```

### 6) Removing any age information from employee data
#### 6.1 employee 테이블의 생년월일 중 년도를 삭제하고 새로운 테이블 생성
```
use problem6;

CREATE TABLE solution AS
  SELECT
    id, fname, lname, address, city, state, zip, substr(birthday,0,5) as birthday
  from employee
```
#### 6.2 테이블 생성 확인 및 데이터 확인


### 7) Seattle employee names in sorted order
#### 7.1 sql파일 작성
```
use problem7;

SELECT * FROM
(
  select concat(fname,' ',lname) as name
    from employee
    where city='Seattle'
) as emp
order by emp.name asc;
```
#### 7.2 HIVE CLI 사용하여 쿼리실행 및 결과확인

### 8) Export customer data from HDFS into MySQL
#### 8.1 MySQL information & table 확인
* Installation : localhost
* Database name : problem8
* Table : solution
* user/pw : cloudera/cloudera

#### 8.2 sqoop export 실행 및 결과 확인
```
sqoop export \
--connect jdbc:mysql://localhost/problem8 \
--username cloudera \
--password cloudera \
--table solution \
--export-dir /user/training/problem8/data/customer  \
--input-fields-terminated-by '\t'
```
 Exported 100 records  메시지 확인


### 9) 중복제거를 위한  기존 customer ID에 'A'문자 추가하기
```
use prpblem9;

CREATE TABLE solution AS
  select concat('A', id) as id,
        fname,
        lname,
        address,
        city,
        state,
        zip
  FROM customer
```
#### 9.1 신규테이블 생성 및 데이터 조회

#### 9.2 기존 데이터와의 비교


### 10) Creating a database view
```
use problem10;

CREATE VIEW solution AS
select a.id as id,
       a.fname as fname,
       a.lname as lname,
       a.city as city,
       a.state as state,
       b.charge as charge,
       to_date(a.tstamp) as billdate
    from customer as a join billing as b
                      on a.id = b.id
```
#### 10.1 creating view 'solution'

#### 10.2  view 테이블 조회 총 96건 확인



### 11) Creating a database view
default db 사용
Write the report query in the local file /home/training/problem11/solution.sql
Executing the solution.sql script from the hive command-line tools should generate the report output

#### 11.1 a. TOP3 dualcore product(Which top three products has Dualcore sold more of than any other?)
```
SELECT A.brand,
       A.name,
       COUNT(A.prod_id) AS cnt
  FROM products A
  JOIN order_details B
    ON (A.prod_id = B.prod_id)
 WHERE A.brand = 'Dualcore'
 GROUP BY brand, name, A.prod_id
 ORDER BY cnt DESC
 LIMIT 3;
```

#### 11.2  Calculating Revenue and Profit – write a query to show Dualcore’s revenue (total price of products sold) and profit (price minus cost) by date.
```
SELECT TO_DATE(order_date) as date,
       SUM(price) AS revenue,
       SUM(price - cost) as profit
  FROM products A
  JOIN order_details B
    ON (A.prod_id = B.prod_id)
  JOIN orders C
    ON (B.order_id = C.order_id)
 WHERE A.brand = 'Dualcore'
 GROUP BY TO_DATE(order_date)
 ORDER BY date;
```

#### 11.3  TOP 10 출력: Calculating the order Total – Which ten orders had the highest total dollar amounts?
```
SELECT A.order_id,
       SUM(B.price) AS total
  FROM order_details A
  JOIN products B
    ON (A.prod_id = B.prod_id)
  GROUP BY order_id
  ORDER BY total DESC
  LIMIT 10;
```

