# mysql


## Environment

비밀번호 변경:
```sql
grant all privileges on *.* to '[user]'@'%' identified by '[password]';
```

타임존 조회:
```sql
SELECT @@global.time_zone;
```

서버 구동시간 조회(초단위):
```aql
SHOW GLOBAL STATUS like 'uptime';
```

버전 체크:
```sql
SHOW VARIABLES LIKE "%version%";
```

인코딩 설정:
```sql
SET NAMES '[encoding]'
# 예제 
set names 'utf8mb4'
```


## Database

디비 연결:
```bash
mysql -h [host] -u [user] -p[password]
```

디비 생성:
```sql
CREATE DATABASE [database];
```

디비 삭제:
```sql
DROP DATABASE [database];
```

문자셋 변경:
```sql
ALTER DATABASE [database] CHARACTER SET [charset] COLLATE [collate];
# 많이 사용하는 예제
ALTER DATABASE [database] CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

디비 용량 체크:
```sql
SELECT table_schema "DB Name",
  ROUND(SUM(data_length + index_length) / 1024 / 1024 , 1) "DB Size in MB" 
FROM information_schema.tables 
GROUP BY table_schema; 
```

디비 덤프 뜨기:
```bash
mysqldump -h [host] \
    -u [user] \
    -p[password] \
    --port=[prot] \
    --single-transaction \
    --routines \
    --triggers \
    --databases [database] > dump.sql
```

덤프한 디비 가져오기:
```bash
mysql -h [host] -u [user] -p[password] [database] < dump.sql
```


## Table

테이블 정보 조회:
```sql
desc [table];
```

테이블 컬럼 추가:
```sql
# 예제 
ALTER TABLE [table] ADD attr VARCHAR(10);
```

테이블 속성 수정:
```sql
# 예제
ALTER TABLE [table] MODIFY attr INT NOT NULL AUTO_INCREMENT PRIMARY KEY;
# 예제 
ALTER TABLE [table] MODIFY attr BIGINT NOT NULL DEFAULT 100;
```

데이터 값 수정:
```sql
# 예제 
UPDATE [table] SET [attr] = [value] where [condition]
```

테이블 비우기:
```sql
truncate [table];
```

데이터 입력:
```sql
# 예제 
INSERT INTO [table] (no, name, level, amount)
VALUES ('1002', 'chris', 'user', '10000');
```

데이터 삭제:
```sql
DELETE FROM [table] where [condition];
```

테이블 복제:
```sql
CREATE TABLE [new table] LIKE [old table];
INSERT INTO [new table] SELECT * FROM [old table];
```

## logging/debugging

일반 쿼리 로그 활성화/비활성화:
```sql
SET GLOBAL general_log='on';
SET GLOBAL general_log='off';
```

로그를 테이블에 저장:
```sql
SET GLOBAL log_output='table';
```

로그 조회:
```sql
SELECT * FROM mysql.general_log\G
```

쿼리 상태 조회:
```sql
# 예제 
EXPLAIN EXTENDED SELECT * FROM test WHERE id IN (1,2,3)\G
```

경고 출력:
```sql
SHOW warnings\G
```

## Functions

CURDATE(): 오늘 날짜 반환

CURTIME(): 지금 시간 반환 

CONCAT(): 합친 텍스트 반환 
```sql
SELECT CONCAT(attr1, '-', attr2) FROM tableName;
```

MD5(): 해쉬태그 반환 

IFNULL(): Null 체크 결과 반환 

JOIN: [참고](http://stackoverflow.com/questions/17542431/less-number-of-records-for-left-join-vs-inner-join)

비트연산: [참고](http://www.phpschool.com/gnuboard4/bbs/board.php?bo_table=tipntech&wr_id=77064)


## etc

피봇 테이블 구하기:
```sql
# 예제 
select left(datetime, 10) as date,
  sum(case when type=300 then 1 else 0 end) as type_300,
  sum(case when type=301 then 1 else 0 end) as type_301,
  sum(case when type=302 then 1 else 0 end) as type_302,
  sum(case when type=303 then 1 else 0 end) as type_303,
  sum(case when type=310 then 1 else 0 end) as type_310,
  sum(case when type=311 then 1 else 0 end) as type_311,
  sum(case when type=312 then 1 else 0 end) as type_312,
  sum(case when type=313 then 1 else 0 end) as type_313
from step_web.log
where type > 299 and type < 314
group by date order by date desc;
```

Upsert(기존에 없는 데이터만 추가하는 쿼리): 
```sql
INSERT INTO [table] ([attr1], [attr2])
SELECT ?, ?, NOW() FROM DUAL
WHERE NOT EXISTS(
    SELECT 1
    FROM [table]
    WHERE [attr1] = ?
  )
LIMIT 1;
```

시간 비교:
```sql
# 예제 
SELECT *
FROM [table]
WHERE start < CAST('05:00:00' AS time)
AND end > CAST('05:00:00' AS time)
```

다중 입력:
```sql
INSERT INTO table
  ([attr1], [attr2], [attr3], [attr4])
VALUES
  ('[val1]', '[val2]', '[val3]', '[val4]'),
  ('[val1]', '[val2]', '[val3]', '[val4]')
```


## OSX에서 사용하기 

설치:
```bash
brew install mysql
```

시작/종료/재시작:
```bash
mysql.server start
mysql.server stop
mysql.server restart
```
