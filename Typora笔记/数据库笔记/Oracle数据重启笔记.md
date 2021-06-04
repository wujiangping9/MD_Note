1.将服务器所有关于Oracle的服务全部启动一下

![image-20210604100735257](F:\MD_Note\Typora笔记\笔记图片\image-20210604100735257.png)

2.尝试是否可以正常连接，如果不行进行如下操作

```sql
1.打开cmd命令窗口，看是否能使用sqlplus进行登录数据库
sqlplus 用户名/密码
2.如果报错显示
ORA-27101: shared memory realm does not exist
3.使用无用户名密码进行进入数据库
sqlplus /nolog
4.进入SQL>界面后，使用已知的一个用户使用sysdba权限进入
conn 用户名/密码 as sysdba
5.执行命令启动Oracle例程
startup
```

![image-20210604101513963](F:\MD_Note\Typora笔记\笔记图片\image-20210604101513963.png)

