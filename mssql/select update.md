# [mssql] SELECT UPDATE

b테이블의 컬럼을 a테이블의 컬럼에 업데이트 하고 싶을 때

아래와 같이 셀렉트 업데이트하자.



```sql
update a
set a.columns = b.columns
from tableA a
inner join tableB b on b.keycolums = a.keycolums
where a.columns = '조건'
```

