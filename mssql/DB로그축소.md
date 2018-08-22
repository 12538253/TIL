# [mssql] DB로그축소



## 상황

고객사에서 전화가 왔다.

특정 페이지 진입 시 오류가 난다고

응?  업데이트도 안했는데 갑자기 에러가?

일단 로컬에서 동일한 증상이 발생하는지 확인

> 대기작동시간이 초과하였습니다.

뭔가 잘못됐다. 



## 해결을 위한 시도, 의식의 흐름..

1. 또 스칼라서브쿼리 때문에 타임아웃이 발생했나?

   => 쿼리확인, 문제없음


2. 일단 타임아웃으로 인한 오류부터 처리하자

   DB connect timeout을 늘려줌

    ```xml
    Connection Timeout=60
    ```

      => 오류는 발생하지 않으나, 평소보다 10~15초 정도 느려짐. 이대론 해결이 아님.

          


3. 설마 로그가 다 찼나?  로그를 확인해보자

   ```mssql
   -- 실제용량 확인
   DBCC SQLPERF(LOGSPACE)
   ```

   => 헐 Log space used가 99%

4. 로그를 축소하자

   ```mssql
   --LOG를 지운다. (파일 사이즈는 줄어 들지 않는다.)
   BACKUP LOG 디비명 WITH TRUNCATE_ONLY
   
   --LOG 파일 크기 줄이는 SQL(1000은 1000MB)
   DBCC SHRINKFILE(디비로그파일명, 1000)
   ```

   =>  실행 시 아래와 같은 에러가..

   > 'TRUNCATE_ONLY'은(는) 인식할 수 없는 BACKUP 옵션입니다.



5. 위의 방법은 mssql 2008 이전 버전에 활용 하던 구문이였다.

   그럼 이후 구문도 있을거야 구글링ㄱㄱ

   ```mssql
   -- 1. 축소 시 속도를 위해 리커버리모드를 SIMPLE로 변경
   ALTER DATABASE 디비명 SET RECOVERY SIMPLE
   
   -- 2. 로그파일 축소
   DBCC SHRINKFILE(디비로그파일명)
   
   -- 3. 리커버리모드를 다시 FULL모드로
   ALTER DATABASE 디비명 SET RECOVERY FULL
   ```

   => 성공 사이트속도가 빨라졌다. 이것도 임시방편이니까.. 디비로그를 관리 할 방법을 찾아봐야겠다.



## 정리

mssql2008 이전 
   ```mssql
   --LOG를 지운다. (파일 사이즈는 줄어 들지 않는다.)
   BACKUP LOG 디비명 WITH TRUNCATE_ONLY
   
   --LOG 파일 크기 줄이는 SQL(1000은 1000MB)
   DBCC SHRINKFILE(디비로그파일명, 1000)
   ```

mssql2008 이후
```mssql
   -- 1. 축소 시 속도를 위해 리커버리모드를 SIMPLE로 변경
   ALTER DATABASE 디비명 SET RECOVERY SIMPLE
   
   -- 2. 로그파일 축소
   DBCC SHRINKFILE(디비로그파일명)
   
   -- 3. 리커버리모드를 다시 FULL모드로
   ALTER DATABASE 디비명 SET RECOVERY FULL
```