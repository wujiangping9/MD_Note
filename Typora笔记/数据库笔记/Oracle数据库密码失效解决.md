（1）任意用户登陆数据库使用查询语句查询密码过期情况

```sql
select * from dba_profiles where profile='DEFAULT' and resource_name='PASSWORD_LIFE_TIME';
```

（2）修改成没有限制的天数，执行完记得提交，再次执行（1）查看是否修改成功，显示为如下图为成功

```sql
alter profile default  limit password_life_time unlimited; 
```

![1553135440627](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\1553135440627.png)

（3）alter user [USERNAME] identified by [PASSWORD]; （USERNAME为你要更改密码的登录账号，PASSWORD为要更改的密码）

（4）但如果账号被锁住，则需要解锁命令！

```sql
alter user [USERNAME] identified by oracle account unlock; --USERNAME:要解锁的用户名
```

