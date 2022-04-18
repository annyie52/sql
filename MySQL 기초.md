# 데이터베이스, 테이블 정의하기

- CREATE(생성)
- ALTER(변경)
- DROP(제거)



## 데이터베이스, 테이블 생성하기

### 데이터베이스

데이터베이스 생성

```sql
CREATE DATABASE test;
```

데이터베이스 확인하기

```sql
SHOW DATABASES;
```

데이터베이스 선택

```sql
USE test;
```

선택한 현재 데이터베이스 확인

```sql
SELECT DATABASE();
```



### 테이블

- 테이블을 생성할 때 테이블 이름, 컬럼 이름, 데이터 타입, 제약 조건 등을 설정할 수 있다.

제약 조건이 없는 user1 테이블 생성

```sql
CREATE TABLE user1(
	user_id INT,
	name VARCHAR(20),
	email VARCHAR(30),
	age INT(3),
	rdate DATE
);
```

제약 조건이 있는 user2 테이블 생성

```sql
CREATE TABLE user2(
	user_id INT PRIMARY KEY AUTO_INCREMENT,
	name VARCHAR(20) NOT NULL,
	email VARCHAR(30) UNIQUE NOT NULL,
	age INT(3) DEFAULT 30,
	rdate TIMESTAMP
);
```



## 데이터베이스, 테이블 변경하기

### 데이터베이스

현재 선택된 데이터베이스의 문자 집합 확인하기

```sql
SHOW VARIABLES LIKE "character_set_databases";
```

ascii 문자 집합 체계로 변경 및 확인

```sql
ALTER DATABASE test CHARACTER SET = "ascii";
SHOW VARIABLES LIKE "character_set_databases";
```

utf8 문자 집합 체계로 변경 및 확인

```sql
ALTER DATABASE test CHARACTER SET = "utf8";
SHOW VARIABLES LIKE "character_set_databases";
```



### 테이블

- 옵션을 이용해 컬럼, 제약 조건, 데이터 타입 등을 변경

컬럼 추가

```sql
ALTER TABLE user2 ADD tmp TEXT;
```

컬럼 변경

```sql
ALTER TABLE user2 MODIFY COLUMN tmp INT(3);
```

컬럼 삭제

```sql
ALTER TABLE user2 DROP tmp;
```



## 데이터베이스, 테이블 제거하기

### 데이터베이스 생성 및 삭제

```sql
CREATE DATABASE tmp;
DROP DATABASE tmp;
```



### 테이블 생성 및 삭제

```sql
CREATE DATABASE tmp;
USE tmp;

CREAT TABLE tmp_tb(id INT);
DROP TABLE tmp_tb;

DROP DATABASE tmp;
```



# 데이터 조작하기 (CRUD)

- INSERT - Create에 해당
- SELECT - Read에 해당
- UPDATE - Update에 해당
- DELETE - Delete에 해당



## 데이터 삽입

```sql
USE test;
```

ROW 하나씩 삽입

```sql
INSERT INTO user1(user_id, name, email, age, rdate)
VALUES(1, "anne", "anne@gmail.com", 24, "2022-04-16");
```

ROW 여러 개 삽입

```sql
INSERT INTO user1(user_id, name, email, age, rdate)
VALUES(2, "yea", "yea@gmail.com", 23, "2022-04-13"),
	  (3, "kate", "kate@gmail.com", 20, "2022-03-25");
```

특정 컬럼을 선택해서 데이터 삽입

```sql
INSERT INTO user1(user_id, name)
VALUES(4, "tom");
```

컬럼명을 생략하여 전체 컬럼에 데이터 삽입

```sql
INSERT INTO user1
VALUES(5, "harry", "harry@naver.com", 30, "2022-01-29");
```



- AUTO_INCREMENT 옵션이 있는 경우 삽입하지 않아도 자동으로 추가된다.
- UNIQUE 설정이 있는 컬럼에 중복된 값을 삽입하면 에러가 발생한다.
- 날짜에 now() 사용 가능하다.



## 데이터 조회

전체 컬럼 조회

```sql
SELECT * FROM user1;
```

조회할 컬럼 선택

```sql
SELECT name, email, rdate
FROM user1;
```

as를 이용하여 조회할 때 컬럼의 이름 변경

```sql
SELECT user_id as "회원 아이디", name as "회원 이름"
FROM user1;
```

중복된 필드 제거해서 조회

```sql
SELECT DISTINCT(age)
FROM user1;
```



### WHERE 절을 이용하여 조건별 선택하기

나이가 25세 이상인 사람들의 모든 데이터 조회

```mysql
SELECT *
FROM user1
WHERE age >= 25;
```

나이가 17살 이상 25살 이하인 사람을 조회

```mysql
SELECT *
FROM user1
WHERE age >= 17 and age <=25;
```

- 구간값에 의해 조건을 만들 때는 Between 활용이 낫다

```mysql
SELECT *
FROM user1
WHERE age BETWEEN 17 and 25;
```

Like나 NOT LIKE를 사용하면 특정 문자열이 들어간 데이터를 조회할 수 있다.

- 조커 문자 (%) 사용

```mysql
SELECT *
FROM user1
WHERE email like "%.com";
```



- and와 or를 섞어서 쓰는 경우에는 괄호로 묶어서 혼동을 주지 않게 한다. (and 연산이 우선적으로 처리된다.)

```mysql
SELECT *
FROM user1
WHERE (email like '%naver%' or email like '%daum%') and age >=35;
```



여러 개의 조건을 만족하는 데이터를 조회할 때

```mysql
SELECT *
FROM user1
where age in (24, 25);
```



### limit 절 사용하기

- `limit <시작 인덱스>, <표시할 데이터의 수>`

```sql
SELECT *
FROM user1
limit 0, 3;
```



### ORDER BY 사용하기

- 기본은 오름차순(ASC)
- 내림차순(DESC)

```sql
SELECT *
FROM user1
ORDER BY age ASC;
```

age 내림차순, rdate 오름차순

```sql
SELECT *
FROM user1
ORDER BY age DESC, rdate asc;
```



### GROUP BY 사용하기

- GROUP BY절로 특정 컬럼을 기준으로 데이터를 묶은 후
- 각종 집계 함수를 이용해 데이터를 집계 ( Aggregate )
  - `SUM` : 총 합
  - `AVG` : 평균
  - `STD, STDDEV` : 표준편차
  - `VAR` : 분산
  - `MAX` : 최댓값
  - `MIN` : 최솟값
  - `COUNT` : 개수

```mysql
SELECT district,
       AVG(Population) as "avg_pop",
       STDDEV(Population) as "std_pop",
       MAX(Population),
       MIN(Population)
FROM city
GROUP BY district;
```



### Having 절 사용하기

group by 집계 결과에 조건을 걸 때 사용한다.

```mysql
SELECT CountryCode, sum(population) as sum_pop
FROM city
GROUP BY CountryCode
HAVING sum_pop >= 50000000;
```



## 데이터 형태

### Numeric

#### 정수 타입

- INT
- TINYINT, BIGINT(무한대)



#### 고정 소수점 타입

- DECIMAL(M, D) = NUMERIC(M, D)
- M: 소수점을 포함한 전체 자릿수
- D: 소수 부분 자릿수



#### 일반 실수 타입

- FLOAT(4 byte), DOUBLE(8 byte)
- FLOAT(M, D), DOUBLE(M, D) 형태로 쓰면 고정 소수점 타입으로 사용 가능



#### 올림, 반올림, 내림

```sql
# 1. CEIL: 실수 데이터에서 올림할 때 사용
SELECT CEIL(123.456);

SELECT CountryCode, Language, Percentage, CEIL(percentage)
FROM countrylanguage;
```

```sql
# 2. ROUND: 반올림할 때 사용
SELECT ROUND(123.456, 2);

SELECT CountryCode, Language, Percentage, ROUND(percentage, 0)
FROM countrylanguage;
```

```sql
# 3. TRUNCATE: 내림할 때 사용
SELECT TRUNCATE(123.456, 2);

SELECT CountryCode, Language, Percentage, TRUNCATE(percentage, 0)
FROM countrylanguage;
```



### DATE, TIME

- DATE: 날짜 "0000-00-00" 년-월-일
- TIME: 시간 "00:00:00" 시:분:초
- DATETIME: "0000-00-00 00:00:00" 년-월-일 시:분:초
- TIMESTAMP: "0000-00-00 00:00:00" 년-월-일 시:분:초
- YEAR(4) -> 2022, YEAR(2) -> 22



### STRING

- CHAR : 문자열이 없어도 채워져 있다.
- VARCHAR : 문자열이 없으면 채워져 있지 않다



## 데이터 무결성

```sql
CREATE TABLE user(
	user_id INT PRIMARY KEY AUTO_INCREMENT,
	username VARCHAR(30) NOT NULL
);


CREATE TABLE addr(
	addr_id INT PRIMARY KEY AUTO_INCREMENT,
	addr VARCHAR(100) NOT NULL,
	user_id INT,
	
	# 무결성 제약 조건(외래키 설정)
	foreign key(user_id) REFERENCES user(user_id)
)
```



- **user 테이블에 없는 user_id를 addr에서 추가하려고 하면 추가되지 않는다.**



## JOIN

- Inner Join, Left Join, Right Join
- Inner Join
  - 두 테이블 사이에 공통된 값이 없는 행은 출력 X
  - 두 테이블 사이에 공통된 값이 있는 ON 절의 조건에 대한 행만 출력
  - FROM절에 오는 테이블 기준

```sql
SELECT user.username, addr.addr
FROM user
JOIN addr
ON user.user_id = addr.user_id;
```

- Left Join
  - 기준 테이블에 있는 데이터를 모두 출력

```sql
SELECT user.username, addr.addr
FROM user
JOIN addr
ON user.user_id = addr.user_id;
```



## SELECT 절에서 사용할 수 있는 기능

### 조건문

- 파이썬의 삼항 연산자와 비슷
- IF (조건, 조건이 TRUE일 때 값, 조건이 FALSE일 때 값)

```sql
SELECT Name, Population,
IF (population > 1000000, "big city", "small city") as city_scale
FROM city;
```



### IFNULL

- 데이터가 NULL일 때 표현할 값을 지정
- IFNULL(컬럼, NULL일 때 표현할 값)

```sql
SELECT name, IFNULL(IndepYear, "독립일 없음")
FROM country;
```



### CASE WHEN THEN

- CASE WHEN (조건 1) THEN ("값") WHEN (조건 2) THEN ("값") WHEN (조건 3) THEN ("값") ELSE ("값") END as"별명"

```sql
SELECT Name, population,
CASE WHEN population >= 1000000000 THEN "완전 많다"
WHEN population >= 100000000 THEN "적당"
ELSE "많이 없다"
END as "result"
FROM country;
```



### DATE_FORMAT

```sql
SELECT payment_date,
DATE_FORMAT(payment_date, "%Y") as "YEAR",
DATE_FORMAT(payment_date, "%m") as "MONTH",
DATE_FORMAT(payment_date, "%d") as "DAY",
DATE_FORMAT(payment_date, "%H") as "HOUR",
DATE_FORMAT(payment_date, "%i") as "MINUTE",
DATE_FORMAT(payment_date, "%S") as "SECOND"
FROM payment;
```

```sql
SELECT DATE_FORMAT(payment_date, "%Y-%m") as monthly, avg(amount) as avg_amount
FROM payment
group by monthly
order by avg_amount desc;
```



## SUB-QUERY

- 쿼리 내에 존재하는 또 다른 쿼리 (SUB-QUERY)
- SELECT, FROM, WHERE 절에 사용이 가능하다.



**SELECT 절에 사용되는 SUB-QUERY의 컬럼은 한 개여야 된다.**

```sql
SELECT
( SELECT COUNT(*) FROM city WHERE name like '%k%' ) as city_cnt,
( SELECT AVG(gnp) FROM country WHERE name like '%a%' ) as avg_gnp
FROM dual;
```

오류가 나는 경우

```sql
SELECT
(SELECT avg(gnp), count(gnp) FROM country WHERE Name like "%a%") as avg_cnt

FROM dual;
```



**FROM절에서는 사용하는 서브 쿼리, 행, 열 표시 제한이 없다.**

```sql
SELECT countryCode, name, population
FROM city
WHERE population > 8000000;
```



**WHERE절에는 대부분 IN 연산을 할 때 사용한다.**

```sql
SELECT code, name, HeadOfState
FROM country
WHERE code IN (
                SELECT DISTINCT(countryCode)
                FROM city
                WHERE population >= 8000000 );
```



## 데이터 수정

- where 문을 사용할 수 있다.

```sql
SET AUTOCOMMIT = 0;

SELECT * FROM user2;

UPDATE user2
SET name = "anne",
age = 24
WHERE user_id = 1;

COMMIT; # 작업을 확정 짓는다.

ROLLBACK; # 마지막 commit 상태로 되돌린다.
```



## 데이터 삭제

- WHERE문을 사용한다.

```sql
DELETE FROM user2
WHERE user_id = 1;
```



# 인덱스

- 논리적인 스키마의 구조를 담당

```sql
SELECT COUNT(*)
FROM salaries
WHERE to_date > "2000-01-01";
```

```sql
CREATE INDEX ix_tdate
ON salaries(to_date);
```

- WHERE 절에 자주 사용되는 컬럼을 인덱스로 지정한다.

- 인덱스가 너무 많을 경우에는 복잡하게 검색을 해야 하기 때문에 때에 따라 더 느려질 수 있다.
