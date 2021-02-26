Oracle解锁用户：
1. sqlplus /nolog        开启登陆
2. conn /as sysdba     使用dba身份连接
3. alter user system account unlock;     解锁用户
4. alter user system identified by manager;     重置密码
5. commit;    提交

Oracle创建用户并且授权:
前提条件
    以sysdba的身份连接登录到命令行界面
1 创建用户
 create user xxx identified by xxx;
2 用户授权
 grant create session, connect, resource to xxx;


USERNAME                              SID    SERIAL#
------------------------------ ---------- ----------
NC65                                   15      56000
NC65                                   29      40276
NC65                                  138       8660
NC65                                  141      19067
NC65                                  371      15028
NC65                                  372       3899



问题1：ORA-31626:作业不存在 ORA-31633:无法创建主表"XXX.SYS_IMPORT_FULL_05"？

用户权限不足，赋权语句：
GRANT CONNECT TO YAOGANG WITH ADMIN OPTION;
GRANT DBA TO YAOGANG;
GRANT RESOURCE TO YAOGANG;
ALTER USER YAOGANG DEFAULT ROLE ALL;
参考链接资料：
http://www.mamicode.com/info-detail-584184.html

问题2：数据库导入DMP报错，ORA-39001，ORA-39000，ORA-39088？

需要创建directory，如果有的话可以使用现有的。


impdp  wangzpu/qwer1234@orcl directory=xydir dumpfile=NC20190409.DMP logfile=NC20190409.log  ignore=Y full=Y;
//directory=dir在本台机器上是固定的，
  dumpfile=EXEDATA.DMP//EXEDATA.DMP为导入的文件logfile=0719.log//0719.log创建的日志文件，名字自定义
  imp nc/sys@orcl file=dmp文件路径 ignore=Y full=Y

问题3：导入报错ORA-56935？ 现有数据泵作业正在使用其他版本的时区数据文件

解决： https://blog.csdn.net/kiral07/article/details/86916149





导入导出参考：
https://wenku.baidu.com/view/a140067b11661ed9ad51f01dc281e53a580251ea.html?from=search



（1）.创建用户，
    sqlplus system/manager //登录数据库
    create user nc652 identified by sys; //创建有个用户名为nc652密码为sys的用户
(2).给用户进行授权；
grant connect,dba to nc652;  //nc652进行授权连接和数据库管理员权限
（3）退出数据库quit;
alter user 用户名 default tablespace 表空间
(4)进行数据库导入：
impdp nc652/sys@orcl directory=dir dumpfile=EXEDATA.DMP logfile=0719.log  //directory=dir在本台机器上是固定的，
  dumpfile=EXEDATA.DMP//EXEDATA.DMP为导入的文件logfile=0719.log//0719.log创建的日志文件，名字自定义
  imp nc/sys@orcl file=dmp文件路径 ignore=Y full=Y