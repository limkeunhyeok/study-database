# Part 3 MySQL 고급

## CH 1. 테이블과 뷰

### 1. 테이블

#### 1-1 테이블 생성

```SQL
-- MySQL 공식 문서에 나오는 테이블 생성 기본 형식
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    { LIKE old_tbl_name | (LIKE old_tbl_name) }

create_definition: {
    col_name column_definition
  | {INDEX | KEY} [index_name] [index_type] (key_part,...)
      [index_option] ...
  | {FULLTEXT | SPATIAL} [INDEX | KEY] [index_name] (key_part,...)
      [index_option] ...
  | [CONSTRAINT [symbol]] PRIMARY KEY
      [index_type] (key_part,...)
      [index_option] ...
  | [CONSTRAINT [symbol]] UNIQUE [INDEX | KEY]
      [index_name] [index_type] (key_part,...)
      [index_option] ...
  | [CONSTRAINT [symbol]] FOREIGN KEY
      [index_name] (col_name,...)
      reference_definition
  | check_constraint_definition
}

column_definition: {
    data_type [NOT NULL | NULL] [DEFAULT {literal | (expr)} ]
      [AUTO_INCREMENT] [UNIQUE [KEY]] [[PRIMARY] KEY]
      [COMMENT 'string']
      [COLLATE collation_name]
      [COLUMN_FORMAT {FIXED | DYNAMIC | DEFAULT}]
      [ENGINE_ATTRIBUTE [=] 'string']
      [SECONDARY_ENGINE_ATTRIBUTE [=] 'string']
      [STORAGE {DISK | MEMORY}]
      [reference_definition]
      [check_constraint_definition]
  | data_type
      [COLLATE collation_name]
      [GENERATED ALWAYS] AS (expr)
      [VIRTUAL | STORED] [NOT NULL | NULL]
      [UNIQUE [KEY]] [[PRIMARY] KEY]
      [COMMENT 'string']
      [reference_definition]
      [check_constraint_definition]
}
```

#### 1-2 제약 조건

- 제약 조건(Constraint)이란 데이터의 무결성을 지키기 위한 제한된 조건
- 특정 데이터를 입력할 때 무조건적으로 입력되는 것이 아닌 어떠한 조건을 만족했을 때에 입력되도록 제약
- 5가지 제약 조건
  - PRIMARY KEY 제약 조건
  - FOREIGN KEY 제약 조건
  - UNIQUE 제약 조건
  - DEFAULT 제약 조건
  - NULL 값 허용

##### 기본 키 제약 조건

- 기본 키란 테이블을 구분할 수 있는 식별자
- 기본 키로 생성한 것은 자동으로 클러스터형 인덱스가 생성
- 테이블에서는 기본 키를 하나 이상의 열에 설정

```SQL
-- 테이블 정보 출력
DESCRIBE userTBL;
```

- 기본 키 생성은 CREATE TABLE 문에 PRIMARY KEY라는 예약어를 넣어줌

```SQL
ALTER TABLE userTBL
    ADD CONSTRAINT PK_userTBL_userID
        PRIMARY KEY (userID);
```

- 위의 쿼리문은 기본 키 설정의 또 다른 방법으로 테이블을 수정하여 제약 조건을 설정
- `ALTER TABLE userTBL`: userTBL 변경
- `ADD CONSTRAINT PK_userTBL_userID`: 제약 조건 추가
- `PRIMARY KEY (userID);`: 추가할 제약 조건은 기본 키 제약 조건

##### 외래 키 제약 조건

- 외래 키는 두 테이블 사이의 관계를 선언함으로써 데이터의 무결성을 보장해주는 역활
  - 하나의 테이블이 다른 테이블에 의존
- 외래 키 테이블이 참조하는 기준 테이블의 열은 Primary key이거나 Unique 제약 조건이 설정되어 있어야 함
- 외래 키 생성은 CREATE TABLE 끝에 FOREIGN KEY 키워드로 설정

```SQL
ALTER TABLE buyTBL
    ADD CONSTRAINT FK_userTBL_buyTBL
    FOREIGN KEY (userID)
    REFERENCES userTBL(userID);
```

- 위의 쿼리문은 외래 키 설정의 또다른 방법
- `ALTER TABLE buyTBL`: buyTBL 수정
- `ADD CONSTRAINT FK_userTBL_buyTBL`: 제약 조건 추가
- `FOREIGN KEY (userID)`: 외래 키 제약 조건을 buyTBL의 userID에 설정
- `REFERENCES userTBL(userID);`: 참조할 기준 테이블은 userTBL의 userID 열

##### UNIQUE 제약 조건

- 중복되지 않은 유일한 값을 입력해야 하는 조건
- PRIMARY KEY와 거의 비슷하며 차이점은 NULL 값을 허용한다는 것

##### DEFAULT 정의

- 값을 입력하지 않았을 때 자동으로 입력되는 기본 값을 정의하는 방법

##### NULL 값 허용

- NULL 값은 '아무 것도 없다'라는 의미
- 공백이나 0과 같은 값과는 다름

#### 1-3 테이블 압축

- MySQL은 자체적으로 테이블 압축 기능을 제공
- 압축 기능은 대용량 테이블의 공간을 절약하는 효과

#### 1-4 임시 테이블

- 임시로 사용되는 테이블

```SQL
CREATE TEMPORARY TABLE [IF NOT EXISTS] 테이블이름
(열 정의 ...)
```

- 임시 테이블은 세션 내에서만 존재하며 세션이 닫히면 자동으로 삭제
- 임시 테이블은 생성한 클라이언트에서만 접근이 가능하며 다른 클라이언트에는 접근할 수 없음
- 임시 테이블은 기존 테이블의 이름을 사용하지 않는 것이 좋음
- 삭제되는 시점
  - 사용자가 DROP TABLE로 직접 삭제
  - Workbench를 종료하거나 mysql 클라이언트를 종료하면 삭제
  - MySQL 서비스가 재시작되면 삭제

#### 1-5 테이블 삭제

```SQL
DROP TABLE 테이블이름;
```

- 외래 키 제약 조건의 기준 테이블은 삭제할 수 없으며, 먼저 외래 키가 생성된 외래 키 테이블을 삭제해야 함
- 여러 개의 테이블을 동시에 삭제하려면 `DROP TABLE 테이블1, 테이블2, 테이블3;` 식으로 나열

```SQL
-- MySQL 공식 문서에 나오는 테이블 수정 기본 형식
ALTER TABLE tbl_name
    [alter_option [, alter_option] ...]
    [partition_options]

alter_option: {
    table_options
  | ADD [COLUMN] col_name column_definition
        [FIRST | AFTER col_name]
  | ADD [COLUMN] (col_name column_definition,...)
  | ADD {INDEX | KEY} [index_name]
        [index_type] (key_part,...) [index_option] ...
  | ADD {FULLTEXT | SPATIAL} [INDEX | KEY] [index_name]
        (key_part,...) [index_option] ...
  | ADD [CONSTRAINT [symbol]] PRIMARY KEY
        [index_type] (key_part,...)
        [index_option] ...
  | ADD [CONSTRAINT [symbol]] UNIQUE [INDEX | KEY]
        [index_name] [index_type] (key_part,...)
        [index_option] ...
  | ADD [CONSTRAINT [symbol]] FOREIGN KEY
        [index_name] (col_name,...)
        reference_definition
  | ADD [CONSTRAINT [symbol]] CHECK (expr) [[NOT] ENFORCED]
  | DROP {CHECK | CONSTRAINT} symbol
  | ALTER {CHECK | CONSTRAINT} symbol [NOT] ENFORCED
  | ALGORITHM [=] {DEFAULT | INSTANT | INPLACE | COPY}
  | ALTER [COLUMN] col_name
        {SET DEFAULT {literal | (expr)} | DROP DEFAULT}
  | ALTER INDEX index_name {VISIBLE | INVISIBLE}
  | CHANGE [COLUMN] old_col_name new_col_name column_definition
        [FIRST | AFTER col_name]
  | [DEFAULT] CHARACTER SET [=] charset_name [COLLATE [=] collation_name]
  | CONVERT TO CHARACTER SET charset_name [COLLATE collation_name]
  | {DISABLE | ENABLE} KEYS
  | {DISCARD | IMPORT} TABLESPACE
  | DROP [COLUMN] col_name
  | DROP {INDEX | KEY} index_name
  | DROP PRIMARY KEY
  | DROP FOREIGN KEY fk_symbol
  | FORCE
  | LOCK [=] {DEFAULT | NONE | SHARED | EXCLUSIVE}
  | MODIFY [COLUMN] col_name column_definition
        [FIRST | AFTER col_name]
  | ORDER BY col_name [, col_name] ...
  | RENAME COLUMN old_col_name TO new_col_name
  | RENAME {INDEX | KEY} old_index_name TO new_index_name
  | RENAME [TO | AS] new_tbl_name
  | {WITHOUT | WITH} VALIDATION
}
```

- 열 추가: ADD
- 열 삭제: DROP COLUMN
- 열 이름 및 데이터 형식 변경: CHANGE COLUMN
- 열 제약 조건 추가 및 삭제: DROP PRIMARY KEY, DROP FOREIGN KEY

### 2. 뷰

- 뷰는 테이블과 동일하게 사용하는 개체
- 뷰의 실체는 SELECT문이 되는 것

```SQL
-- 뷰 생성 구문
USE tableDB;
CREATE VIEW v_userTBL
AS
    SELECT userID, name, addr FROM userTBL;
```

- 뷰는 새로운 테이블이라고 생각해도 무방
- 뷰의 장점
  - 보안
  - 복잡한 쿼리 단순화
