# 한 컬럼의 콤마(,)를 행(row)으로 분리



xml로 형변환 후 cross apply 구문을 통해 처리함

cross apply는 inner join과 비슷하지만 다른점이 있는 것 같다.

나중에 찾아봐야징.

아래 처럼 처리



```mssql
SELECT A.QuestionNo, 
     Split.a.value('.', 'VARCHAR(100)') AS String 
 FROM  (SELECT QuestionNo, 
         CAST ('<M>' + REPLACE(ExamineeAnswer, ',', '</M><M>') + '</M>' AS XML) AS String 
               FROM  TCourseExamineeReply
               WHERE ReplyNo = 136396
        ) AS A CROSS APPLY String.nodes ('/M') AS Split(a);
```



다른방법도 있네?



```sql
with cte (QuestionNo, subjectitem, ExamineeAnswer) as
(
  select QuestionNo
              , cast(left(ExamineeAnswer, charindex(',',ExamineeAnswer+',') - 1) as varchar(50)) subjectitem
              , stuff(ExamineeAnswer, 1, charindex(',',ExamineeAnswer+','), '') ExamineeAnswer
  from TCourseExamineeReply
  where ReplyNo = 136396
  union all
  select QuestionNo, cast(left(ExamineeAnswer, charindex(',',ExamineeAnswer+',') - 1) as varchar(50)) ,stuff(ExamineeAnswer, 1, charindex(',',ExamineeAnswer+','), '') ExamineeAnswer
  from cte
  where ExamineeAnswer > ''
)

select QuestionNo, subjectitem
from cte
```

