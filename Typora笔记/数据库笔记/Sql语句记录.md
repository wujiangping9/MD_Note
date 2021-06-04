#### 1.计算年龄和年龄分层

```sql
 --年龄结构
 SELECT 
 case
 when
 Trunc(MONTHS_BETWEEN(
　　to_date(to_char(sysdate, 'yyyy-MM-dd'),'yyyy-MM-dd'),
　　to_date(null, 'yyyy-MM-dd')
　　) / 12) >35 then 'adssada'
end
from dual;
 
 select agetable.age,count(agetable.age) from (
select
case
when bd_psndoc.birthdate is null then '未知'
when Trunc(MONTHS_BETWEEN(to_date(to_char(sysdate, 'yyyy-MM-dd'),'yyyy-MM-dd'),to_date(bd_psndoc.birthdate, 'yyyy-MM-dd')) / 12)<=30 then '<=30'
when Trunc(MONTHS_BETWEEN(to_date(to_char(sysdate, 'yyyy-MM-dd'),'yyyy-MM-dd'),to_date(bd_psndoc.birthdate, 'yyyy-MM-dd')) / 12)<=35 then '<=35'
when Trunc(MONTHS_BETWEEN(to_date(to_char(sysdate, 'yyyy-MM-dd'),'yyyy-MM-dd'),to_date(bd_psndoc.birthdate, 'yyyy-MM-dd')) / 12)<=40 then '<=40'
when Trunc(MONTHS_BETWEEN(to_date(to_char(sysdate, 'yyyy-MM-dd'),'yyyy-MM-dd'),to_date(bd_psndoc.birthdate, 'yyyy-MM-dd')) / 12)<=45 then '<=45'
when Trunc(MONTHS_BETWEEN(to_date(to_char(sysdate, 'yyyy-MM-dd'),'yyyy-MM-dd'),to_date(bd_psndoc.birthdate, 'yyyy-MM-dd')) / 12)<=50 then '<=50'
when Trunc(MONTHS_BETWEEN(to_date(to_char(sysdate, 'yyyy-MM-dd'),'yyyy-MM-dd'),to_date(bd_psndoc.birthdate, 'yyyy-MM-dd')) / 12)<=55 then '<=55'
when Trunc(MONTHS_BETWEEN(to_date(to_char(sysdate, 'yyyy-MM-dd'),'yyyy-MM-dd'),to_date(bd_psndoc.birthdate, 'yyyy-MM-dd')) / 12)<=60 then '<=60'
when Trunc(MONTHS_BETWEEN(to_date(to_char(sysdate, 'yyyy-MM-dd'),'yyyy-MM-dd'),to_date(bd_psndoc.birthdate, 'yyyy-MM-dd')) / 12)>60 then '>60'
end as age
from   (select * from hi_psnjob where hi_psnjob.endflag = 'N' and hi_psnjob.lastflag  = 'Y' and hi_psnjob.ismainjob = 'Y') hi_psnjob
left join  bd_psndoc on bd_psndoc.pk_psndoc = hi_psnjob.pk_psndoc
) agetable
group by agetable.age
```

#### 2.sql的递归查询

### MYSQL：

##### （1）准备上下级表结构的表

![image-20191116120422120](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20191116120422120.png)

##### （2）使用sql递归查询的SQL语句

```sql
WITH RECURSIVE _children AS
(
 SELECT fun.* FROM `base_position` fun WHERE fun.pid=86 AND creator = 12
    UNION ALL
 SELECT fun.* FROM _children,`base_position` fun WHERE fun.pid=_children.pk_post
)
SELECT * FROM _children;
```

结果

![image-20191116120601603](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20191116120601603.png)

##### （3）分析sql递归查询的效率问题

```
1.极大的减少java使用递归查询出现的频繁连接数据的问题
2. WITH AS的含义

　　WITH AS短语，也叫做子查询部分(subquery factoring)，可以让你做很多事情，定义一个SQL片断，该SQL片断会被整个SQL语句所用到。有的时候，是为了让SQL语句的可读性更高些， 也有可能是在UNION ALL的不同部分，作为提供数据的部分。

　　特别对于UNION ALL比较有用。因为UNION ALL的每个部分可能相同，但是如果每个部分都去执行一遍的话，则成本太高，所以可以使用WITH AS短语，则只要执行一遍即可。如果WITH AS短语所定义的表名被调用两次以上，则优化器会自动将WITH AS短语所获取的数据放入一个TEMP表里，如果只是被调用一次，则不会。而提示materialize则是强制将WITH AS短语里的数据放入一个全局临时表里。很多查询通过这种方法都可以提高速度。
3.CTE支持递归查询，定义一个递归CTE至少需要两个(可能更多)查询：第一个查询称为定位点成员，第二个查询称为递归成员，
定位点成员只是一个返回有效关系结果表的查询，与用于定义非递归表达式的查询类似，定位点成员查询只被调用一次。
```

### ORACLE

（1）查询组织表的下级组织

```sql
select pk_org,pk_fatherorg,name from org_orgs where 1=1 start with pk_org = '0001ZZ10000000000F8Z' connect by prior pk_org = pk_fatherorg

```

结果

![image-20200701205232236](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200701205232236.png)