NCC文件服务器搭建说明

1.配置文件服务器信息，可以使用sysConfig.bat，也可以直接登录文件服务器进行配置

（1）sysConfig.bat进行配置

![image-20200902151616768](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200902151616768.png)

（2）网页地址配置

```http
## 网页服务器地址
http://127.0.0.1:8099/fs/
```

![image-20200902151839007](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200902151839007.png)

2.如果不清楚登录的用户名和密码，可以在SuperUserVerify类中打断点查看自己登录输入的密码（进行加密后的密文），找到密文后更新到数据库中

![image-20200902152747507](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200902152747507.png)

```sql
select * from sm_super_user
```

![image-20200902152840647](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200902152840647.png)