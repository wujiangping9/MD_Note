# expdp impdp中 exclude/include 的使用





exclude和include参数能够在使用expdp或impdp是对特定的对象或对象类型进行筛选或过滤。比如因工作的需要导出特定的表或不导出特定
的表、视图以及存储过程、索引、约束、授权统计信息等等。下面将给出expdp或impdp使用exclude和include参数的方法和示例。

 

**一、exclude/include参数用法：**

​    EXCLUDE=[object_type]:[name_clause],[object_type]:[name_clause]  -->排出特定对象

​    INCLUDE=[object_type]:[name_clause],[object_type]:[name_clause]  -->包含特定对象

​    object_type子句用于指定对象的类型，如table,sequence,view,procedure,package等等
​    name_clause子句可以为SQL表达式用于过滤特定的对象名字。它由SQL操作符以及对象名(可使用通配符)来过滤指定对象类型中的特定对象。
​    当未指定name_clause而仅仅指定object_type则所有该类型的对象都将被过滤或筛选。多个[object_type]:[name_clause]中间以逗号分割。

​    示例：
​       expdp <other_parameters> SCHEMAS=scott EXCLUDE=SEQUENCE,TABLE:"IN ('EMP','DEPT')"

​       impdp <other_parameters> SCHEMAS=scott INCLUDE=PACKAGE,FUNCTION,PROCEDURE,TABLE:"='EMP'"

 

**二、常用的过滤SQL表达式**

​    EXCLUDE=SEQUENCE,VIEW                          --过滤所有的SEQUENCE,VIEW
  
​    EXCLUDE=TABLE:"IN ('EMP','DEPT')"               --过滤表对象EMP,DEPT
  
​    EXCLUDE=SEQUENCE,VIEW,TABLE:"IN ('EMP','DEPT')" --过滤所有的SEQUENCE,VIEW以及表对象EMP,DEPT
  
​    EXCLUDE=INDEX:"= 'INDX_NAME'"                   --过滤指定的索引对象INDX_NAME
  
​    INCLUDE=PROCEDURE:"LIKE 'PROC_U%'"              --包含以PROC_U开头的所有存储过程(_ 符号代表任意单个字符)
  
​    INCLUDE=TABLE:"> 'E' "                          --包含大于字符E的所有表对象

​    其它常用操作符 NOT IN, NOT LIKE, <, != 等等
​    直接将过滤操作符封装到参数文件中，如下面的例子
​    Parameter file:exp_scott.par
  
​    DIRECTORY = dump_scott
​    DUMPFILE = exp_scott_%U.dmp
​    LOGFILE = exp_scott.log
​    SCHEMAS = scott
​    PARALLEL= 2
​    EXCLUDE = TABLE:"IN ('EMP', 'DEPT')" 
​    
​    expdp system/manager parfile=exp.par 


==>Author: Robinson Cheng                                                          
==>Blog:    <http://blog.csdn.net/robinson_0612>                             
==>MSN:    [robinson_0612@hotmail.com](mailto:robinson_0612@hotmail.com)                                      
==>QQ:     645746311                                                             
  
**三、命令行下转义符的处理**

Windows平台:

​    D:\> expdp system/manager DIRECTORY=my_dir DUMPFILE=exp_tab.dmp LOGFILE=exp_tab.log SCHEMAS=scott
​     INCLUDE=TABLE:\"IN ('EMP', 'DEPT')\"
  
​    在Windows平台下，需要对象双引号进行转义，使用转义符\

Unix平台:
    在未使用parfile文件的情形下，所有的符号都需要进行转义，包括括号，双引号，单引号等
    % expdp system/manager DIRECTORY=my_dir DUMPFILE=exp_tab.dmp LOGFILE=exp_tab.log SCHEMAS=scott
     INCLUDE=TABLE:\"IN \'EMP\',\'DEP\'\'EMP\',\'DEP\'\"

 

**四、exclude/include常见的错误**

  任意需要转义的字符如果未转义或转义错误，都会产生ORA错误。下面给出几种常见的ORA错误。
   
     ORA-39001: invalid argument value
     ORA-39071: Value for INCLUDE is badly formed.
     ORA-00936: missing expression

​     ORA-39001: invalid argument value
​     ORA-39071: Value for EXCLUDE is badly formed.
​     ORA-00904: “DEPT”: invalid identifier

​     ORA-39001: invalid argument value
​     ORA-39041: Filter “INCLUDE” either identifies all object types or no object types.

​     ORA-39001: invalid argument value
​     ORA-39041: Filter “EXCLUDE” either identifies all object types or no object types

​     ORA-39001: invalid argument value
​     ORA-39038: Object path “USER” is not supported for TABLE jobs.

 

**五、导出示例**  -->导出不包含sequence，表EMP，DEPT的其它所有对象
    [oracle@orasrv scott]$ expdp scott/tiger directory=dump_scott dumpfile=fliter_1.dmp log=fliter_1.log schemas=scott \
    \> exclude=sequence,table:\" in \'EMP\',\'DEPT\'\'EMP\',\'DEPT\'\"

  -->包含大于字符T的所有表对象
    [oracle@orasrv ~]$ expdp scott/tiger directory=dump_scott dumpfile=tmp.dmp logfile=tmp.log include=table:\"\>\'T\'\"
    ...........
    Estimate in progress using BLOCKS method...
    Processing object type SCHEMA_EXPORT/TABLE/TABLE_DATA
    Total estimation using BLOCKS method: 18.06 MB
    Processing object type SCHEMA_EXPORT/TABLE/TABLE
    Processing object type SCHEMA_EXPORT/TABLE/STATISTICS/TABLE_STATISTICS
    . . exported "SCOTT"."TB_PARL"                           7.020 MB   72598 rows
    . . exported "SCOTT"."TT"                                7.009 MB   72493 rows
    . . exported "SCOTT"."XP":"P2"                           5.398 KB       1 rows
    Master table "SCOTT"."SYS_EXPORT_SCHEMA_01" successfully loaded/unloaded
    ******************************************************************************
    Dump file set for SCOTT.SYS_EXPORT_SCHEMA_01 is:
      /home/oracle/dump/scott/tmp.dmp
    Job "SCOTT"."SYS_EXPORT_SCHEMA_01" successfully completed at 14:24:55

  

-->仅仅导入表对象TT，如果存在则将其替换
    [oracle@orasrv scott]$ impdp scott/tiger directory=dump_scott dumpfile=tmp.dmp logfile=tmp_imp.log \
    \> include=table:\" =\'TT\' \" table_exists_action=replace
    .............
    Connected to: Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - Production
    With the Partitioning, OLAP, Data Mining and Real Application Testing options
    Master table "SCOTT"."SYS_IMPORT_FULL_01" successfully loaded/unloaded
    Starting "SCOTT"."SYS_IMPORT_FULL_01":  scott/******** directory=dump_scott dumpfile=tmp.dmp logfile=tmp_imp.log
    include=table:" ='TT' " table_exists_action=replace 
    Processing object type SCHEMA_EXPORT/TABLE/TABLE
    Processing object type SCHEMA_EXPORT/TABLE/TABLE_DATA
    . . imported "SCOTT"."TT"                                7.009 MB   72493 rows
    Processing object type SCHEMA_EXPORT/TABLE/STATISTICS/TABLE_STATISTICS
    Job "SCOTT"."SYS_IMPORT_FULL_01" successfully completed at 14:59:32

  

-->下面是一个实际实际导出过程中的parfile参数,exclude参数用于排出所有包含BAK的表对象,以及一些索引对象,使用了in,like运算符
    userid=goex_admin
    directory=DB_DUMP_DIR
    dumpfile=sybo2_full_%U.dmp
    logfile=sybo2_full.log
    exclude=table:"like '%BAK%'",index:" in ('PK_ACC_POS_CASH_PL_TBL_ARC_11','pk_acc_pos_cash_tbl_arc_11',
     'PK_ACC_POS_STOCK_ARCH_TBL_11','I_ACC_POS_STOCK_TBL_ARC_DT','I_ACC_POS_CASH_PL_ARCH_TBL_DT') ",
     table:"in ('ACC_POS_STOCK_TBL_ARC')"
    filesize=5368709120
    schemas=goex_admin

 

 -->下面是一个使用query查询过滤的情形
  -->该操作导出的schema为scott，表对象emp中deptno=20，以及dept表中deptno=20的记录被导出，这两个表中的其余记录被过滤
  -->该操作除上述过滤以后的所有对象将被一一导出
  expdp scott/tiger dumpfile=scott_filter.dmp logfile=scott_filter.log directory=DB_DUMP_DIR schemas=scott \
  \> query=scott.emp:'"where deptno=20"',scott.dept:'"where deptno=20"'