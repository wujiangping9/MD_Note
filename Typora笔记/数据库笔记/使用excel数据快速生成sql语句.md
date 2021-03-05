1.例子截图

![image-20210305111629253](F:\MD_Note\Typora笔记\笔记图片\image-20210305111629253.png)

2.生成sql的字符串拼接公式

```sql
1.简单
=CONCATENATE("insert into user(code, name) values('",C2, "','", D2, "');")
2.实际使用例子
=CONCATENATE("insert into user_admin(pk_tenant,name, code,quantity,begindate,expiredate,mobile,email,role,qr_ip,yht_lang,enablestate,qr_exptime) values('",B2, "','",C2, "','", D2, "','",E2,"','",F2,"','",G2,"','",H2,"','",I2,"','",J2,"','",K2,"','",L2,"','",M2,"','",N2,"');")

```

3.效果截图

![image-20210305112341774](F:\MD_Note\Typora笔记\笔记图片\image-20210305112341774.png)