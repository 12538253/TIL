# [mssql] 있으면 insert 없으면 update

알아두면 정말 좋은 쿼리로

mssql 2008 이전은 지원하지 않으니 

이전버전은 IF EXISTS 또는 IF NOT EXISTS 문으로 처리하자



## mssql 2008 이후

```sql
MERGE TABLE1 AS A        -- 변경대상 테이블
USING TABLE2 AS B        -- table, view, select절(서브쿼리 포함) 모두 사용가능
ON -- 조인 조건

-- 있을 경우 UPDATE
WHEN MATCHED THEN
   UPDATE
   SET  A.columns = B.columns

-- 없을 경우 INSERT
WHEN NOT MATCHED THEN
   INSERT (columns1, columns2)
   VALUES (B.columns1, B.columns2);
```



## mssql 2008 이전

```sql
if exists (select 절)
  begin
   -- update문
  end
else
  begin
   -- insert문
  end
```

