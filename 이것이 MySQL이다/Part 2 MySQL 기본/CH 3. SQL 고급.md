# Part 2 MySQL 기본

## CH 3. SQL 고급

### 1. SQL의 데이터 형식

#### 1-1 지원하는 데이터 형식

##### 숫자 데이터 형식

![1](https://user-images.githubusercontent.com/38815618/95988311-b10e7d00-0e63-11eb-80b6-08e5814b7608.PNG)

##### 문자 데이터 형식

![2](https://user-images.githubusercontent.com/38815618/95988314-b2d84080-0e63-11eb-81cd-39f7babac8ff.PNG)

##### 날짜와 시간 데이터 형식

![3](https://user-images.githubusercontent.com/38815618/95988316-b2d84080-0e63-11eb-8709-d0931fba6df5.PNG)

##### 기타 데이터 형식

![4](https://user-images.githubusercontent.com/38815618/95988319-b370d700-0e63-11eb-9cf9-0acb2a4c154c.PNG)

#### 1-2 변수의 사용

```SQL
SET @변수이름 = 변수의 값; -- 변수의 선언 및 값 대입
SELECT @변수이름; -- 변수의 값 출력
```

<p>
    SQL도 일반적인 언어처럼 변수를 선언하고 사용할 수 있다. 변수의 선언과 값의 대입은 위의 형식을 따른다.
</p>

```SQL
use sqldb;

set @myVar1 = 5;
set @myVar2 = 3;
set @myVar3 = 4.25;
set @myVar4 = '가수 이름 ==> ';

select @myVar1;
select @myVar2 + @myVar3;
select @myVar4, name From usertbl where height > 180;
```

<p>
    변수의 값은 위의 쿼리문처럼 `SELECT ... FROM`문과도 같이 사용할 수 있다.
</p>

```SQL
set @myVar1 = 3;
prepare myQuery
    from 'select name, height from usertbl order by height limit ?';
execute myQuery using @myVar1;
```

<p>
    `LIMIT @변수` 형식으로 사용하면 오류가 발생하기 때문에 다른 방식을 사용해야 한다. `PREPARE 쿼리이름 FROM 쿼리문`은 쿼리이름에 쿼리문을 준비만 해놓고 실행하지 않는다. 이후에 `EXECUTE 쿼리이름`을 만나는 순간에 실행이 되며 `USING @변수`를 이용해 ?으로 처리한 부분에 대입된다.
</p>

#### 1-3 데이터 형식과 형변환

##### 데이터 형식 변환 함수

```SQL
CAST(expression AS 데이터형식 [(길이)])
CONVERT(expression, 데이터형식 [(길이)])
```

<p>
    데이터 형식 변환과 관련해서는 CAST(), CONVERT() 함수를 사용하며 형식만 다를 뿐 비슷한 기능을 한다.
</p>

```SQL
-- 실수를 정수로 변환
use sqldb;
select cast(avg(amount) as signed integer) as '평균 구매 개수' from buytbl;
select convert(avg(amount), signed integer) as '평균 구매 개수' from buytbl;
```

```SQL
-- 구분자를 날짜 형식으로 변환
select cast('2020$12$12' as date);
select cast('2020#12#12' as date);
select cast('2020/12/12' as date);
select cast('2020@12@12' as date);
```

##### 암시적인 형변환

<p>
    명시적인 변환(Explicit Conversion)이란 CAST()나 CONVERT() 함수를 이용해 데이터 형식을 변환하는 것을 말한다. 암시적인 변환(Implicit Conversion)이란 함수를 사용하지 않고 형이 변환되는 것을 말한다.
</p>

```SQL
select '100' + '200'; -- 300
select concat('100', '200'); -- '100200'
select concat(100, '200'); -- '100200'
select 1 > '2mega'; -- 0, 1 > 2 비교
select 3 > '2MEGA'; -- 1, 3 > 2 비교
select 0 = 'mega2'; -- 1, 0 = 0 비교
```

#### 1-4 MySQL 내장 함수

##### 제어 흐름 함수

- IF(수식, 참, 거짓): 수식의 결과에 따라서 2중 분기
- IFNULL(수식1, 수식2): 수식1이 NULL이 아니면 수식1이 반환되고, NULL이면 수식2를 반환
- NULLIF(수식1, 수식2): 수식 1과 수식2가 같으면 NULL 반환, 다르면 수식1 반환
- CASE~WHEN~ELSE~END: CASE는 연산자로 분류되며 다중 분기에 사용

##### 문자열 함수

- ASCII(아스키 코드), CHAR(숫자): 아스키 코드값 또는 아스키 코드값에 해당하는 문자를 반환
- BIT_LENGTH(문자열), CHAR_LENGTH(문자열), LENGTH(문자열): Bit 크기 또는 문자 크기를 반환, 문자의 개수 반환, 할당된 Byte 수 반환

```SQL
select bit_length('abc'), char_length('abc'), length('abc'); -- 24, 3, 3
select bit_length('가나다'), char_length('가나다'), length('가나다'); -- 72, 3, 9
```

- CONCAT(문자열1, 문자열2, ...), CONCAT_WS(문자열1, 문자열2, ...): 문자열을 이음

```SQL
select concat_ws('/', '2020', '01', '01'); -- 2020/01/01
```

- ELT(위치, 문자열1, 문자열2, ...), FIELD(찾을 문자열, 문자열1, 문자열2, ...), FIND_IN_SET(찾을 문자열, 문자열 리스트), INSTR(기존 문자열, 부분 문자열), LOCATE(부분 문자열, 기준 문자열): 조건에 맞는 문자열을 반환
- FORMAT(숫자, 소수점 자릿수): 소수점 아래 자릿수까지 표현
- BIN(숫자), HEX(숫자), OCT(숫자): 2, 16, 8진수의 값을 반환
- INSERT(기존 문자열, 위치, 길이, 삽입할 문자열): 기준 문자열의 위치부터 길이만큼 지우고 삽입할 문자열을 끼워 넣음
- LEFT(문자열, 길이), RIGHT(문자열, 길이): 왼쪽 또는 오른쪽에서 문자열의 길이만큼 반환
- UCASE(문자열), UPPER(문자열), LCASE(문자열), LOWER(문자열): 소문자에서 대문자, 대문자에서 소문자로 변환
- ...

##### 수학 함수

- ABS(숫자): 절댓값 반환
- ACOS(숫자), ASIN(숫자), ATAN(숫자), ATAN2(숫자1, 숫자2), SIN(숫자), COS(숫자), TAN(숫자): 삼각 함수와 관련된 함수
- CEILING(숫자), FLOOR(숫자), ROUND(숫자): 올림, 내림, 반올림
- CONV(숫자, 원래 진수, 변환할 진수): 숫자를 원래 진수에서 변환할 진수로 계산
- ...

##### 날짜 및 시간 함수

- ADDDATE(날짜, 차이), SUBDATE(날짜, 차이): 날짜를 기준으로 차이를 더하거나 뺀 날짜를 구함
- ADDDATE(날짜/시간, 시간), SUBDATE(날짜/시간, 시간): 날짜/시간을 기준으로 시간을 더하거나 뺀 결과를 구함
- CURDATE(), CURTIME(), NOW(), SYSDATE(): 현재 날짜, 현재 시간, 현재 날짜와 시간을 구함
- YEAR(날짜), MONTH(날짜), DAY(날짜), HOUR(시간), MINUTE(시간), SECOND(시간), MICROSECOND(시간): 날짜 또는 시간에서 연, 월, 일, 시, 분, 초, 밀리초를 구함
- ...

##### 시스템 정보 함수

- USER(), DATABASE(): 현재 사용자 및 현재 선택된 데이터베이스를 구함
- FOUND_ROWS(): 앞의 SELECT문에서 조회된 행의 개수
- ROW_COUNT(): 앞의 INSERT, UPDATE, DELETE문에서 실행된 행의 개수
- VERSION(): 현재 MySQL 버전
- SLEEP(초): 쿼리 실행을 잠깐 멈춤

##### JSON 데이터

- JSON은 데이터를 교환하기 위한 개방형 표준 포맷이며, 속성과 값으로 구성
- JSON으로 변환하려면 JSON_OBJECT()나 JSON_ARRAY() 함수를 이용

### 2. 조인

- 조인이란 두 개 이상의 테이블을 묶어서 하나의 결과 집합으로 만들어 내는 것
- 데이터베이스의 테이블은 중복과 공간 낭비를 피하고 데이터의 무결성을 위해서 여러 개의 테이블로 분리하여 저장하며, 분리된 테이블은 서로 관계를 맺고 있음

#### 2-1 INNER JOIN(내부 조인)

- 양쪽 테이블에 모두 내용이 있는 것만 조인되는 방식

```SQL
SELECT <열 목록>
FROM <첫 번째 테이블>
    INNER JOIN <두 번째 테이블>
    ON <조인될 조건>
[WHERE 검색 조건]
```

#### 2-2 OUTER JOIN(외부 조인)

- 한쪽에만 내용이 있어도 그 결과가 표시되는 방식

```SQL
SELECT <열 목록>
FROM <첫 번째 테이블(LEFT 테이블)>
    <LEFT | RIGHT | FULL> OUTER JOIN <두 번째 테이블(RIGHT 테이블)>
    ON <조인될 조건>
[WHERE 검색 조건]
```

- LEFT는 왼쪽 테이블의 것은 모두 출력
- RIGHT는 오른쪽 테이블의 것은 모두 출력
- FULL은 양쪽 모두에 조건이 일치하지 않는 것을 모두 출력

#### 2-3 CROSS JOIN(상호 조인)

- 한쪽 테이블의 모든 행들과 다른 쪽 테이블의 모든 행을 조인
- 결과의 개수는 두 테이블 개수를 곱한 개수이며 카티션 곱(Cartesian Product)

#### 2-4 SELF JOIN(자체 조인)

- 자기 자신과 조인

#### 2-5 UNION / UNION ALL / NOT IN / IN

- 두 쿼리의 결과를 행으로 합치는 것

```SQL
SELECT 문장1
    UNION [ALL]
SELECT 문장2
```

- SELECT 문장1과 SELECT 문장2의 결과 열의 개수가 같아야 하며 데이터 형식도 각 열 단위로 같거나 서로 호환되는 데이터 형식이어야 함

### 3. SQL 프로그래밍

- SQL에서도 다른 언어처럼 분기, 흐름 제어, 반복의 기능이 있음

```SQL
-- 스토어드 프로시저를 만들고 사용하는 방법
DELIMITER $$
CREATE PROCEDURE 스토어드 프로시저이름()
BEGIN
    -- SQL 프로그래밍 코딩
END $$
DELIMITER;
CALL 스토어드 프로시저이름()
```

<p>
    `DELIMITER $$ ~ END $$` 부분까지는 스토어드 프로시저의 코딩할 부분을 묶어준다. MySQL의 종료 문자는 세미콜론인데 `CREATE PROCEDURE` 안에서도 세미콜론이 종료 문자이므로 어디까지가 스토어드 프로시저인지 구별이 어렵다. 그래서 `END $$`가 나올 떄까지를 스토어드 프로시저로 인식하게 하는 것이다. `DELIMITER;`와 같이 종료 문자를 세미콜론으로 변경해 놓아야 한다. `CALL 스토어드 프로시저이름();`은  `CREATE PROCEDURE`로 생성한 스토어드 프로시저를 호출한다.
</p>

#### 3-1 IF ... ELSE

- 조건에 따라 분기
- 한 문장 이상 처리되어야 할 때 `BEGIN ... END`와 함께 묶어줘야함
  - 실행 문장이 한 문장이라도 습관적으로 묶어주는 것이 좋음

#### 3-2 CASE

- CASE문은 조건에 맞는 WHEN이 여러 개더라도 먼저 조건이 맞는 WHEN이 처리되고 종료

#### 3-3 WHILE ITERATE / LEAVE

- 해당 부울 식이 참인 동안에 계속 반복

```SQL
WHILE <부울 식> DO
    ...
END WHILE;
```

- ITERATE문은 다른 언어의 continue, LEAVE문은 break문과 비슷한 역활

#### 3-4 오류 처리

```SQL
DECLARE 액션 HANDLER FOR 오류조건 처리할_문장;
```

- 액션: 오류 발생 시에 행동을 정의하는데 CONTINUE와 EXIT 둘 중 하나를 사용하며, CONTINUE는 제일 뒤의 처리할_문장 부분이 처리
- 오류조건: 어떤 오류를 처리할 것인지를 지정
- 처리할_문장: 처리할 문장이 여러 개일 경우에는 `BEGIN ... END`로 묶어줌

#### 3-5 동적 SQL

- 미리 쿼리문을 준비한 후에 나중에 실행하는 것을 동적 SQL이라 함
- PREPARE는 SQL문을 실행하지 않고 미리 준비만 해놓으며, EXECUTE문은 준비한 쿼리문을 실행
