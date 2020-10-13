# Part 2 MySQL 기본

## CH 2. SQL 기본

<p>
    데이터베이스(=스키마) 개체의 이름을 식별자라고 한다. 데이터베이스 개체란 데이터베이스, 테이블, 인덱스, 열, 뷰, 트리거, 스토어드 프로시저 등과 같은 개체들을 의미한다. MySQL에서 이러한 개체를 정의할 때는 몇 가지 규칙을 따라야 한다.
</p>

- 알파벳 a~z, A~Z, 0~9, $, _를 사용할 수 있다. 하지만 기본 설정은 영문 대문자나 소문자 어떤 것을 사용해도 소문자로 생성된다.
- 개체 이름은 최대 64자로 제한된다.
- 예약어를 사용하면 안 된다.
- 개체 이름은 원칙적으로 중간에 공백이 있으면 안되지만, 공백을 사용하려면 백틱(``)으로 묶어야 한다.
- 개체에 이름을 줄 때 되도록 알기 쉽게 주는 것이 좋고 너무 길게 주는 것보는 짧고 간결하며 명확한 것이 가장 좋다.

### 1. SELECT문

```SQL
-- 많이 사용되는 구조
SELECT select_expr
    [FROM table_references]
    [WHERE where_condition]
    [GROUP BY {col_name | expr | position}]
    [HAVING where_condition]
    [ORDER BY {col_name | expr | position}]
```

#### USE

```SQL
USE 데이터베이스_이름;
```

<p>
    SELECT문을 사용하려면 먼저 사용할 데이터베이스 지정이 필요하며, 위 쿼리는 "지금부터 '데이터베이스_이름'을 사용하겠으니 모든 쿼리는 '데이터베이스_이름'에서 수행하라"라는 뜻이다.
</p>

#### SELECT와 FROM

```SQL
SELECT 열_이름 FROM 테이블_이름;
```

<p>
    일반적으로 *은 모든 것을 의미한다. 열 이름이 나올 위치에 *을 사용하면 모든 열을 의미한다. FROM은 테이블/뷰 등의 항목이다. 원래 테이블의 전체 이름은 `데이터베이스_이름.테이블_이름` 형식으로 표현된다. 허나 USE로 사용한 데이터베이스 이름이 자동으로 붙게되어 앞의 데이터베이스 이름은 생략이 가능하다.
</p>

<p>
    추가로 MySQL에서 주석은 `--`로 처리한다. 여러 줄의 주석은 `/* */`로 묶는다.
</p>

<p>
    열 이름을 별도의 별칭(Alias)으로 지정할 수도 있다. 열 이름 뒤에 `AS 별칭` 형식으로 붙이면 된다. AS는 붙여도 되고 생략해도 되며, 별칭을 붙일 경우에는 되도록 따옴표 안에 별칭을 사용하는 것을 권장한다.
</p>

```SQL
SELECT first_name AS '이름', gender '성별', hire_date '회사 입사일'
FROM employees;
```

#### WHERE

```SQL
SELECT 필드_이름
FROM 테이블_이름
WHERE 조건식;
```

<p>
    WHERE절은 조회하는 결과에 특정 조건을 줘서 원하는 데이터만 보고 싶을 때 사용한다. 조건식에 조건 연산자(=, <, >, <=, >=, != 등)와 관계 연산자(NOT, AND, OR 등)를 조합하여 다양한 조권의 쿼리를 생성할 수 있다.
</p>

#### BETWEEN ... AND와 IN() 그리고 LIKE

```SQL
SELECT Name, height FROM userTBL WHERE height >= 180 AND height <= 183;
```

<p>
    위의 쿼리문은 조건 연산자를 사용하여 키가 180~183인 사람을 조회한다. 만약 조회하는 값이 연속적인 값을 가지는 경우 `BETWEEN ... AND`를 사용하여 아래의 쿼리문처럼 작성할 수 있다.
</p>

```SQL
SELECT Name, height FROM userTBL WHERE height BETWEEN 180 AND 183;
```

<p>
    만약 연속적인 값이 아닌 이산적인 값인 경우 `IN()`을 사용할 수 있다.
</p>

```SQL
SELECT Name, addr FROM userTBL WHERE addr IN ('경남', '전남', '경북');
```

<p>
    문자열의 내용을 검색한다면 `LIKE` 연산자를 사용할 수 있다.
</p>

```SQL
SELECT Name, height FROM userTBL WHERE Name LIKE '김%';
```

<p>
    위의 쿼리문의 조건은 성이 김씨이고 그 뒤는 무엇이든(%) 허용한다는 의미이다. 만약 한 글자와 매치하기 위해서는 _를 사용한다.
</p>

#### ANY/ALL/SOME 그리고 서브쿼리(SubQuery, 하위쿼리)

```SQL
SELECT Name, height FROM userTBL
    WHERE height > (SELECT height FROM userTBL WHERE Name = '김경호');
```

<p>
    서브쿼리는 쿼리문 안에 쿼리문이 들어 있는 것을 말한다. 위의 쿼리문은 김경호보다 큰 사람을 출력한다.
</p>

<p>
    ANY는 서브쿼리의 여러 개의 결과 중 한 가지만 만족해도 되며, ALL은 서브쿼리의 여러 개의 결과를 모두 만족시켜야 한다. 추가로 `=ANY(서브쿼리)`와 `IN(서브쿼리)`는 동일한 의미다.
</p>

#### ORDER BY

```SQL
SELECT Name, mDate FROM userTBL ORDER BY mDate;
```

<p>
    ORDER BY절은 결과물에 영향을 미치지는 않지만 결과가 출력되는 순서를 조절하는 구문이다. 기본적으로 오름차순으로 정렬되며, 내림차순으로 정렬하기 위해서는 열 이름 뒤에 DESC를 적으면 된다.
</p>

#### DISTINCT

```SQL
SELECT DISTINCT addr FROM userTBL;
```

<p>
    DISTINCT 구문은 중복된 것을 제거하여 출력한다.
</p>

#### LIMIT

```SQL
SELECT emp_no, hire_date FROM employees
	ORDER BY hire_date ASC
    LIMIT 5;
```

<p>
    `LIMIT N` 구문은 상위의 N개만 출력한다. 이외에도 `LIMIT 시작, 개수` 형식으로도 사용할 수 있다.
</p>

#### CREATE TABLE ... SELECT

```SQL
CREATE TABLE 새로운테이블 (SELECT 복사할_열 FROM 기존_테이블)
```

<p>
    `CREATE TABLE ... SELECT` 구문은 테이블을 복사해서 사용할 경우에 사용된다.
</p>

#### GROUP BY

```SQL
SELECT userID, SUM(amount) FROM buyTBL GROUP BY userID;
```

<p>
    GROUP BY절은 그룹으로 묶어주는 역활을 한다. 집계 함수는 주로 GROUP BY절과 함께 쓰이며 위의 쿼리는 각 사용자별로 구매한 개수를 합쳐서 출력한다.
</p>

|함수명|설명|
|:---|:---|
|AVG()|평균|
|MIN()|최솟값|
|MAX()|최댓값|
|COUNT()|행의 갯수|
|COUNT(DISTINCT)|행의 갯수, 중복은 1개만 인정|
|STDEV()|표준편차|
|VAR_SAMP()|분산|

#### HAVING

```SQL
SELECT userID AS '사용자', SUM(price * amount) AS '총 구매액'
	FROM buyTBL
    GROUP BY userID
    HAVING SUM(price * amount) > 1000
    ORDER BY SUM(price * amount);
```

<p>
    HAVING은 WHERE와 비슷한 개념으로 집계 함수에 대해서 조건을 제한한다. 위의 쿼리문의 경우 총 구매액이 1000보다 큰 사용자를 오름차순으로 출력한다.
</p>

#### ROLLUP

```SQL
SELECT num, groupName, SUM(price * amount) AS '비용'
	FROM buyTBL
    GROUP BY groupName, num
    WITH ROLLUP;
```

<p>
    총합 또는 중간 합계가 필요하다면 GROUP BY절과 함께 WITH ROLLUP문을 사용하면 된다.
</p>

#### DML(Data Manipulation Language)

- 데이터를 조작하는데 사용하는 언어
- DML 구문이 사용되는 대상은 테이블의 행
- 사용하기 위해서는 이전에 테이블이 정의되어 있어야 함
- SELECT, INSERT, UPDATE, DELETE가 해당
- 트랜잭션이 발생
  - 테이블의 데이터를 변경할 때 실제 테이블에 완전히 적용하지 않고, 임시로 적용시키는 것

#### DDL(Data Definition Language)

- 데이터베이스, 테이블, 뷰, 인덱스 등의 데이터베이스 개체를 생성/삭제/변경하는 역활
- CREATE, DROP, ALTER 등이 해당
- 트랜잭션이 발생하지 않음
  - ROLLBACK, COMMIT 불가
  - DDL은 실행 즉시 MySQL에 적용

#### DCL(Data Control Language)

- 사용자에게 어떤 권한을 부여하거나 빼앗을 때 사용
- GRANT, REVOKE, DENY 등이 해당

### 2. 데이터의 변경을 위한 SQL문

#### INSERT

```SQL
INSERT [INTO] 테이블[(열1, 열2, ...)] VALUES (값1, 값2, ...)
```

<p>
    INSERT는 테이블에 데이터를 삽입하는 명령어이다. 테이블 이름 다음에 나오는 열은 생략 가능하나, VALUE 다음에 나오는 값들의 순서 및 개수가 테이블이 정의된 열 순서 및 개수와 동일해야 한다.
</p>

#### AUTO_INCREMENT

<p>
    AUTO_INCREMENT는 자동으로 1부터 증가하는 값을 입력해준다. 해당 속성이 AUTO_INCREMENT로 지정되어 있다면 INSERT에서는 해당 열이 없다고 생각하고 입력하면 된다. AUTO_INCREMENT로 지정할 때는 PRIMARY KEY 또는 UNIQUE로 지정해야 되며 데이터 형은 숫자 형식만 사용할 수 있다. 어떤 특정 값부터 입력되도록 하고 싶다면 `AUTO_INCREMENT = N`으로 사용하면 되며, 증가값을 지정하려면 서버 변수인 @@auto_increment_increment 변수를 변경시켜야 한다.
</p>

#### 대량의 샘플 데이터 생성

```SQL
INSERT INTO 테이블_이름 (열1, 열2, ...)
    SELECT문;
```

<p>
    위의 쿼리문은 다른 테이블의 데이터를 가져와 대량으로 입력하는 효과를 낸다.
</p>

#### UPDATE

```SQL
UPDATE 테이블_이름
    SET 열1=값1, 열2=값2, ...
    WHERE 조건;
```

<p>
    UPDATE는 기존에 입력되어 있는 값을 변경한다. WHERE절은 생략 가능하나, 테이블의 전체 행이 변경된다.
</p>

#### DELETE FROM

```SQL
DELETE FROM 테이블_이름 WHERE 조건;
```

<p>
    DELETE는 행 단위로 삭제한다. WHERE절은 생략 가능하나, 전체 데이터를 삭제한다.
</p>
