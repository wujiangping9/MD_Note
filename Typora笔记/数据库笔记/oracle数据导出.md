场景：有2个oracle用户，userA默认表空间tablespaceA， userB默认表空间tablespaceB。

​        现在需要将userA中的系统表(该用户表空间中以SYS开头的表)导出到userB中，将userB的系统表覆盖，但不覆盖userB的业务表。方法如下：

​        导出userA用户schema中SYS开头的表，使用tables=SYS%

```sql
expdp userA/password  directory=TMP_BAK dumpfile=userA-170504-expdp-tab.dmp  logfile=userA-170504-expdp-tab.log tables=SYS%
```

```sql
 expdp nc65_final/root DIRECTORY=F:/temp DUMPFILE=exp_tab.dmp LOGFILE=exp_tab.log SCHEMAS=scott EXCLUDE=TABLE:\"NOT LIKE 'WA_%'\" 
--EXCLUDE=TABLE:\"NOT IN ('EMP', 'DEPT')\" 使用not in排除表名
--EXCLUDE=TABLE:\"NOT LIKE 'EMP%'\" 使用not like排除表名
```

​        若只需导出某几张表，则使用tables=table1,table2         

```sql
expdp userA/password  directory=TMP_BAK dumpfile=userA-170504-expdp-tab02.dmp  logfile=userA-170504-expdp-tab02.log tables=table1,table2
```

```sql
 $expdp user/psw@orcl  exclude=table:like('WA_%') dumpfile=exp_daya.dmp
 $expdp user/psw@orcl DIRECTORY=my_dir exclude=table:like('WA_%') dumpfile=exp_daya.dmp SCHEMAS=scott LOGFILE=exp_tab.log
```

​         

```sql
$expdp system/manager DIRECTORY=my_dir DUMPFILE=exp_tab.dmp LOGFILE=exp_tab.log SCHEMAS=scott
     INCLUDE=TABLE:\"IN ('EMP', 'DEPT')\"
```



​         将导出的dmp文件导入到userB用户的schema中。

​         需要注意此处SCHEMA与表对应的TABLESPACE已经发生改变，并且原userB的表空间中已经存在SYS表。故导入时需要加上以下几个参数：

​        ①remap_schema指定源SCHEMA与目的SCHEMA。

​        ②remap_tablespace指定源TABLESPACE与目的TABLESPACE。

​        ③ table_exists_action=replace将重名表进行覆盖。



​        导入前注意先备份userB的数据，避免导出出错造成数据丢失。

```bash
expdp userB/password  directory=TMP_BAK dumpfile=userB-170504-expdp.dmp  logfile=userB-170504-expdp.log
```



​        

​         正式将SYS开头的表导入userB用户中。

```bash
impdp userB/password  directory=TMP_BAK dumpfile=userA-170504-expdp-tab.dmp  remap_schema=userA:userB REMAP_TABLESPACE=tablespaceA:tablespaceB TABLE_EXISTS_ACTION=REPLACE logfile=userB-0504-impdp-tab.log
```




