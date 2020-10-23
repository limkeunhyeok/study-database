# SQL 쿼리문 요약

## 테이블 데이터 조회

```SQL
-- 테이블에서 c1, c2열 조회
SELECT c1, c2 FROM t;
```

```SQL
-- 테이블에서 모든 열 조회
SELECT * FROM t;
```

```SQL
-- 테이블에서 조건에 맞는 열 조회
SELECT c1, c2 FROM t
WHERE condition;
```

```SQL
-- 테이블에서 중복되는 열 제거
SELECT DISTINCT c1 FROM t
WHERE condition;
```

```SQL
-- 테이블에서 데이터 오름차순 또는 내림차순 조회
SELECT DISTINCT c1, c2 FROM t
ORDER BY c1 ASC[DESC]
```

```SQL
-- offset부터 n까지 데이터 조회
SELECT c1, c2 FROM t
ORDER BY c1
LIMIT n OFFSET offset;
```

```SQL
-- 집계 함수를 사용해 그룹화된 데이터 조회
SELECT c1, aggregate(c2)
FROM t
GROUP BY c1;
```

```SQL
-- 집계 함수를 사용해 조건에 맞는 그룹화된 데이터 조회
SELECT c1, aggregate(c2)
FROM t
GROUP BY c1;
HAVING condition;
```

## 다중 테이블 데이터 조회

![1](https://user-images.githubusercontent.com/38815618/96962133-27387100-1541-11eb-8c9d-c12ce56e923a.png)

```SQL
-- 모든 경우의 수를 전부 표현
SELECT c1, c2
FROM t1
CROSS JOIN t2;
```

## SQL 연산자 사용

```SQL
-- 두 쿼리문의 행을 결합
SELECT c1, c2 FROM t1
UNION [ALL]
SELECT c1, c2 FROM t2;
```

```SQL
-- 두 쿼리문의 교차점 반환
SELECT c1, c2 FROM t1
INTERSECT
SELECT c1, c2 FROM t2;
```

```SQL
-- 두 쿼리문의 결과 집합 빼기
SELECT c1, c2 FROM t1
MINUS
SELECT c1, c2 FROM t2;
```

```SQL
-- %, _ 등의 패턴과 일치하는 데이터 조회
SELECT c1, c2 FROM t1
WHERE c1 [NOT] LIKE pattern;
```

```SQL
-- 리스트 안에 존재하는 데이터 조회
SELECT c1, c2 FROM t
WHERE c1 [NOT] IN value_list;
```

```SQL
-- 두 값 사이 데이터조회
SELECT c1, c2 FROM t
WHERE  c1 BETWEEN low AND high;
```

```SQL
-- 값이 NULL인 데이터 조회
SELECT c1, c2 FROM t
WHERE  c1 IS [NOT] NULL;
```

## 테이블 관리

```SQL
-- 테이블 생성
CREATE TABLE t (
    id INT PRIMARY KEY,
    name VARCHAR NOT NULL,
    price INT DEFAULT 0
);
```

```SQL
-- 테이블 삭제
DROP TABLE t;
```

```SQL
-- 테이블 새 열 추가
ALTER TABLE t ADD column;
```

```SQL
-- 테이블 열 삭제
ALTER TABLE t DROP COLUMN c ;
```

```SQL
-- 제약 조건 추가
ALTER TABLE t ADD constraint;
```

```SQL
-- 제약 조건 삭제
ALTER TABLE t DROP constraint;
```

```SQL
-- 테이블 이름 변경
ALTER TABLE t1 RENAME TO t2;
```

```SQL
-- 열 이름 변경
ALTER TABLE t1 RENAME c1 TO c2 ;
```

```SQL
-- 테이블의 모든 데이터 제거
TRUNCATE TABLE t;
```

## SQL 제약 조건 사용

```SQL
-- c1 및 c2를 기본 키로 설정
CREATE TABLE t(
    c1 INT, c2 INT, c3 VARCHAR,
    PRIMARY KEY (c1,c2)
);
```

```SQL
-- c2 열을 외래 키로 설정
CREATE TABLE t1(
    c1 INT PRIMARY KEY,  
    c2 INT,
    FOREIGN KEY (c2) REFERENCES t2(c2)
);
```

```SQL
-- c1 및 c2의 값을 UNIQUE로 설정
CREATE TABLE t(
    c1 INT, c1 INT,
    UNIQUE(c2,c3)
);
```

```SQL
-- c1 > 0 및 c1> = c2의 값 확인
CREATE TABLE t(
    c1 INT, c2 INT,
    CHECK(c1 > 0 AND c1 >= c2)
);
```

```SQL
-- NULL이 아닌 c2 열의 값 설정
CREATE TABLE t(
    c1 INT PRIMARY KEY,
    c2 VARCHAR NOT NULL
);
```

## 데이터 수정

```SQL
-- 데이터 삽입
INSERT INTO t(column_list)
VALUES(value_list);
```

```SQL
-- 여러 데이터 삽입
INSERT INTO t(column_list)
VALUES (value_list),
       (value_list), …;
```

```SQL
-- t2의 행을 t1에 삽입
INSERT INTO t1(column_list)
SELECT column_list
FROM t2;
```

```SQL
-- 모든 행에 대해 c1 열의 새 값 수정
UPDATE t
SET c1 = new_value;
```

```SQL
-- 조건과 일치하는 c1, c2 열의 값 수정
UPDATE t
SET c1 = new_value,
        c2 = new_value
WHERE condition;
```

```SQL
-- 테이블의 모든 데이터 삭제
DELETE FROM t;
```

```SQL
-- 조건에 맞는 데이터 삭제
DELETE FROM t
WHERE condition;
```

## 뷰 관리

```SQL
-- c1 및 c2로 구성된 뷰 생성
CREATE VIEW v(c1,c2)
AS
SELECT c1, c2
FROM t;
```

```SQL
-- 옵션을 사용한 뷰 생성
CREATE VIEW v(c1,c2)
AS
SELECT c1, c2
FROM t;
WITH [CASCADED | LOCAL] CHECK OPTION;
```

```SQL
-- 재귀 뷰 생성
CREATE RECURSIVE VIEW v
AS
select-statement -- anchor part
UNION [ALL]
select-statement; -- recursive part
```

```SQL
-- 임시 뷰 생성
CREATE TEMPORARY VIEW v
AS
SELECT c1, c2
FROM t;
```

```SQL
-- 뷰 삭제
DROP VIEW view_name;
```

## 인덱스 관리

```SQL
-- 테이블의 c1, c2에 인덱스 생성
CREATE INDEX idx_name
ON t(c1,c2);
```

```SQL
-- 테이블의 c3, c4에 UNIQUE 인덱스 생성
CREATE UNIQUE INDEX idx_name
ON t(c3,c4)
```

```SQL
-- 인덱스 삭제
DROP INDEX idx_name;
```

## 집계 함수

- AVG: 평균
- COUNT: 갯수
- SUM: 총합
- MAX: 가장 큰 값
- MIN: 가장 작은 값

## 트리거 관리

```SQL
-- 트리거 생성 또는 수정
CREATE OR MODIFY TRIGGER trigger_name
WHEN EVENT
ON table_name TRIGGER_TYPE
EXECUTE stored_procedure;
```

- WHEN
  - BEFORE: 이벤트가 발생하기 전에 호출
  - AFTER: 이벤트 발생 후 호출
- EVENT
  - INSERT: INSERT를 위해 호출
  - UPDATE: UPDATE 호출
  - DELETE: DELETE를 위해 호출
- TRIGGER_TYPE
  - FOR EACH ROW
  - FOR EACH STATEMENT

```SQL
-- 특정 트리거 삭제
DROP TRIGGER trigger_name;
```
