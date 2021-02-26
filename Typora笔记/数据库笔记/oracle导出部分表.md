## 如何通过权限控制EXP导出指定的表

 

| 标签： [it](http://search.sina.com.cn/?c=blog&q=it&by=tag) | 分类： [oracle](http://blog.sina.com.cn/s/articlelist_2860830692_2_1.html) |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
|                                                            |                                                              |

今天一客户朋友咨询一个Oracle数据库用户EXP权限控制的问题，问我有没有办法可以解决。
问题是这样的： 目前他们那边有外面的开发公司人员在核心系统做开发，考虑到系统数据的敏感性，给他们建了一个数据库用户test，该用户test可以做exp导出操作，但是只能导出指定的几张表，而不是所有的表test都有权限导出。

测试情况如下：

假设MEMA为业务用户，下面有（t1,t2,t3,t4,这4张表），TEST是给外面开发人员使用的用户，
要求：test可以做exp导表操作，但是test用户只能导出t1表，t3表test用户是没有权限导出的

测试内容如下：
1:创建test用户
SYS@EMREP>create user test identified by oracle;

2:赋予test用户create session的权限(最基本权限）
SYS@EMREP>grant create session to test;

3：尝试用test用户去导出mema用户的t1表
[oracle@even ~]$ exp test/oracle  file=/u01/b.dmp tables=mema.t1，mema.t3

Export: Release 10.2.0.2.0 - Production on Mon Aug 25 07:58:18 2014

Copyright (c) 1982, 2005, Oracle.  All rights reserved.


Connected to: Oracle Database 10g Enterprise Edition Release 10.2.0.2.0 - Production
With the Partitioning, OLAP and Data Mining options
Export done in US7ASCII character set and AL16UTF16 NCHAR character set

About to export specified tables via Conventional Path ...
EXP-00009: no privilege to export MEMA's table T1
EXP-00009: no privilege to export MEMA's table T3
Export terminated successfully with warnings.
没有权限执行EXP操作。这是因为test用户需要被赋予exp_full_database的权限才能够导mema用户的表，
SYS@EMREP>grant exp_full_database to test;
此时mema用户下面的表能成功导出：
[oracle@even ~]$ exp test/oracle file=w.dmp tables=mema.t1,mema.t3

Export: Release 10.2.0.2.0 - Production on Tue Aug 26 00:57:02 2014

Copyright (c) 1982, 2005, Oracle.  All rights reserved.


Connected to: Oracle Database 10g Enterprise Edition Release 10.2.0.2.0 - Production
With the Partitioning, OLAP and Data Mining options
Export done in US7ASCII character set and AL16UTF16 NCHAR character set

About to export specified tables via Conventional Path ...
Current user changed to MEMA
. . exporting table                             T1          5 rows exported
. . exporting table                             T3          7 rows exported
Export terminated successfully without warnings.

赋予exp_full_database的权限后，test用户能够导出mema用户下面的所有表，但是我们想限制test用户只导特地的表，也就是说，exp_full_database这个权限有点大了。

查看exp_full_database具有哪些权限：
SYS@EMREP>select * from dba_sys_privs where grantee='EXP_FULL_DATABASE';

GRANTEE                PRIVILEGE                ADM
------------------------------ ---------------------------------------- ---
EXP_FULL_DATABASE           READ ANY FILE GROUP            NO
EXP_FULL_DATABASE           RESUMABLE                NO
EXP_FULL_DATABASE           EXECUTE ANY PROCEDURE            NO
EXP_FULL_DATABASE           EXECUTE ANY TYPE             NO
EXP_FULL_DATABASE           SELECT ANY TABLE             NO
EXP_FULL_DATABASE           ADMINISTER RESOURCE MANAGER        NO
EXP_FULL_DATABASE           BACKUP ANY TABLE             NO
EXP_FULL_DATABASE           SELECT ANY SEQUENCE            NO
8 rows selected.
EXP_FULL_DATABASE 这个role角色包含上面8种权限，注意SELECT ANY TABLE这个权限，正因为包含这个权限，导致test用户可以exp mema用户任何表。于是我先尝试将该SELECT ANY TABLE这个权限从EXP_FULL_DATABASE里面回收掉
SYS@EMREP>revoke SELECT ANY TABLE from exp_full_database;
Revoke succeeded.
接下来我们再来看test用户exp导出情况：
[oracle@even ~]$ exp test/oracle file=1.dmp tables=mema.t1,mema.t3

Export: Release 10.2.0.2.0 - Production on Tue Aug 26 01:28:41 2014

Copyright (c) 1982, 2005, Oracle.  All rights reserved.


Connected to: Oracle Database 10g Enterprise Edition Release 10.2.0.2.0 - Production
With the Partitioning, OLAP and Data Mining options
Export done in US7ASCII character set and AL16UTF16 NCHAR character set

About to export specified tables via Conventional Path ...
Current user changed to MEMA
. . exporting table                             T1
EXP-00056: ORACLE error 942 encountered
ORA-00942: table or view does not exist
. . exporting table                             T3
EXP-00056: ORACLE error 942 encountered
ORA-00942: table or view does not exist
Export terminated successfully with warnings.
SELECT ANY TABLE 从 exp_full_database回收掉之后，test用户无法导出mema下面的表。这也进一步验证了我的猜想。
根据要求，test用户能导出mema.t1表，不能导出mema.t3表，于是我再赋予mema.t1对应的select权限。
SYS@EMREP>grant select on mema.t1 to test;

Grant succeeded.
继续测试exp操作结果：
[oracle@even ~]$ exp test/oracle file=1.dmp tables=mema.t1,mema.t3

Export: Release 10.2.0.2.0 - Production on Tue Aug 26 01:29:08 2014

Copyright (c) 1982, 2005, Oracle.  All rights reserved.


Connected to: Oracle Database 10g Enterprise Edition Release 10.2.0.2.0 - Production
With the Partitioning, OLAP and Data Mining options
Export done in US7ASCII character set and AL16UTF16 NCHAR character set

About to export specified tables via Conventional Path ...
Current user changed to MEMA
. . exporting table                             T1          5 rows exported
. . exporting table                             T3
EXP-00056: ORACLE error 942 encountered
ORA-00942: table or view does not exist
Export terminated successfully with warnings.
我们看到，mema.t1被单独赋予select权限后，能够成功导出。而mema.t3因为没有赋予select权限，所以它不能被导出。


总结： 经过上面一系列的测试，使用上述方法可以实现客户的最终需求。
不过需要说明的是： EXP_FULL_DATABASE作为系统自带的role,考虑到是测试，我是直接在上面revoke select any table,在实际的生产系统中我们不建议直接在该role上面做revoke回收操作。实际的数据库系统中，我们可以新建一个role ,然后把exp_full_database包含的权限(排除select any table)赋予给新建的role.

题外话： 现在很多客户都有一些外面的开发公司在给他们做系统开发，需要频繁的将生产数据导入到测试环境供开发人员访问或者做测试，系统内有些敏感数据客户又不想把这些真实数据提供给他们做测试，Oracle有一款产品叫data masking , 部署该产品，配置相关规则，能够实现真实数据根据规则转换为非真实的数据，这样一方面即没有泄露真实的数据，又能够保证开发人员的测试。有兴趣的同学可以了解下。

<http://blog.sina.com.cn/s/blog_aa84cfe40102v1s2.html>