# [mssql] begin tran 사용하기

나는  update와 delete문 사용 시 매우 신중하게 사용한다.

그래서 항상 update, delete 작업 시 select를 먼저 실행 후 적용하는 습관이 있는데

가끔 join update 등 select로 먼저 확인 하기 어려울 때가 있다.

이때 사용 하는 것이 begin tran



## 사용법
```sql
begin tran -- 1. 이렇게 먼저 선언 후

update 테이블명
set 변경컬럼 = '값'
where 조건

-- 2. 잘변경됐나 확인
select * from 테이블명
where 조건

-- 3. 정상이면 
commit

-- 4. 이상하면
rollback
```



mssql은 오토커밋이 기본이기 때문에 begin tran을 사용하면 안전하게 데이터를 수정 할 수 있다