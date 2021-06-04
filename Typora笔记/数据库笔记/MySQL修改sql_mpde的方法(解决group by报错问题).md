1.最初是为了解决sql报错问题

![image-20210309234315836](F:\MD_Note\Typora笔记\笔记图片\image-20210309234315836.png)

```sql
-- 执行的sql语句
SELECT
    bc.cadre_photo       AS cadre_photo,
    bc.name              AS name,
    bc.birth_date        AS birthDate,
    bc.gender_code       AS genderCode,
    bc.present_position  AS presentPosition,
    bc.gender_name       AS genderName,
    pd.A02016A           AS A02016A,
    bc.native_place_name AS nativePlaceName,
    bc.work_date         AS workDate,
    bc.basic_remarks     AS basicRemarks,
    bc.enable            AS enable,
    bc.creationtime,
    bc.identification_status,
    bc.identification_level,
    pdg.A09001A     AS A09001A,
    pe.A08002A      AS A08002A,
    pq.A06001A      AS A06001A,
    pd.A02053       AS A02053,
    pr.A05004       AS A05004,
    bc.pk_cadreinfo AS pkCadreinfo,
    pp.A58005       AS A58005,
    pe.A08014       AS A08014,
    pe.A08024       AS A08024,
    pd.A02043       AS A02043,
    bc.cadre_order,
    bc.cadre_order_young
FROM
    base_cadre bc
LEFT JOIN 
	(SELECT * FROM (
		SELECT * FROM person_education WHERE isnew = 1 ORDER BY A08004 DESC LIMIT 100000) tpe
        GROUP BY tpe.fk_cadreinfo) pe
ON
    bc.pk_cadreinfo = pe.fk_cadreinfo
LEFT JOIN
    (SELECT * FROM
		(SELECT * FROM person_politics WHERE isnew = 1 ORDER BY A58005 DESC LIMIT 100000) tpp
        GROUP BY tpp.fk_cadreinfo) pp
ON
    bc.pk_cadreinfo = pp.fk_cadreinfo
LEFT JOIN
    (SELECT * FROM 
		(SELECT * FROM person_duty WHERE isnew = 1 ORDER BY A02006 DESC LIMIT 100000) tpd
        GROUP BY tpd.fk_cadreinfo) pd
ON
    bc.pk_cadreinfo = pd.fk_cadreinfo
LEFT JOIN
    (SELECT * FROM 
		(SELECT * FROM person_qualification WHERE isnew = 1 ORDER BY A06004 DESC LIMIT 100000) tpq
        GROUP BY tpq.fk_cadreinfo,IF(tpq.fk_cadreinfo IS NULL, tpq.fk_cadreinfo, 0)) pq
ON
    bc.pk_cadreinfo = pq.fk_cadreinfo
LEFT JOIN
    (SELECT * FROM (
		SELECT * FROM person_degree WHERE isnew = 1 ORDER BY A09004 DESC LIMIT 100000) tpdg
        GROUP BY tpdg.fk_cadreinfo) pdg
ON
    bc.pk_cadreinfo = pdg.fk_cadreinfo
LEFT JOIN
    (SELECT * FROM (
		SELECT * FROM person_rank WHERE isnew = 1  ORDER BY A05004 DESC LIMIT 100000) tpr
        GROUP BY tpr.fk_cadreinfo,IF(tpr.fk_cadreinfo IS NULL, tpr.fk_cadreinfo, 0)) pr
ON
    bc.pk_cadreinfo = pr.fk_cadreinfo
WHERE
    bc.pk_comorg = 492
AND bc.enable = 1
AND bc.identification_status IN ( 0 ,1 ,2 ,3 )
ORDER BY
    IF(ISNULL(bc.cadre_order),1,0),bc.cadre_order ,bc.creationtime DESC;
-- 报错信息
Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'tpr.pk_rank' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
```

2.原因是sql_model的模式引起的，可以通过修改sql_model来解决，也可以通过代码，换一种sql查询方式处理，这里讲述如何修改sql_model来处理问题

```sql
-- (1)检查sql_model是否含有ONLY_FULL_GROUP_BY，如果存在则是sql_model引起的问题，如果没有则寻找其他原因
--查询局部sql_model;
select @@sql_mode;
--查询全局sql_model;
select @@global.sql_mode; 
-- (2)修改局部(全局)sql_model
-- 两种模式：1.'ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'
--		   2.'STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION'
--(2.1)修改局部sql_model
set session sql_mode='STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION ';
--	备份
set session sql_mode='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION ';
--(2.2)修改全局sql_model
set global sql_mode='STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION ';
--	备份
set global sql_mode='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION ';

--****注意：修改局部的sql_model只能再当前事务生效，重新打开后会变回原样，修改全局的sql_model可一直生效，但是再重启数据库后又会变成原先模式，可通过修改mysql配置生效，第三步说明如何修改mysql重启达到完全生效
```

![image-20210309235854924](F:\MD_Note\Typora笔记\笔记图片\image-20210309235854924.png)

![image-20210309235943036](F:\MD_Note\Typora笔记\笔记图片\image-20210309235943036.png)

3.第二步处理完，只能再当前事务(局部修改sql_model)或者不重启(全局修改sql_model1)mysql生效通过修改数据库配置

```http
1.https://blog.csdn.net/l631768226/article/details/77105047
2.https://blog.csdn.net/study_in/article/details/92625397
```

（1）Windows如何修改

```shell
## 到mysql的安装的bin文件夹下执行命令：mysqld --help --verbose，查询出配置文件的读取地址和顺序以及会显示配置信息
mysqld --help --verbose
## 到配置文件最下方添加一下内容
[mysqld]
sql_mode = STRICT_TRANS_TABLES, NO_ZERO_IN_DATE, NO_ZERO_DATE, ERROR_FOR_DIVISION_BY_ZERO, NO_AUTO_CREATE_USER, NO_ENGINE_SUBSTITUTION
## 打开系统管理员cmd窗口，重启mysql数据库
net stop mysql
net start mysql
```

![image-20210310101257464](F:\MD_Note\Typora笔记\笔记图片\image-20210310101257464.png)

![image-20210310102007920](F:\MD_Note\Typora笔记\笔记图片\image-20210310102007920.png)

​	(2)Linux如何修改

```sh
## 进入到mysql安装目录，执行命令打开配置文件
sudo vim /etc/mysql/conf.d/mysql.cnf
## 滚动到文件底部复制并粘贴，如果存在mysqld则执行进行修改
[mysqld]
sql_mode = STRICT_TRANS_TABLES, NO_ZERO_IN_DATE, NO_ZERO_DATE, ERROR_FOR_DIVISION_BY_ZERO,      NO_AUTO_CREATE_USER, NO_ENGINE_SUBSTITUTION
## 保存退出重启mysql
sudo service mysql restart
```

4.sql_mode说明

```
1. SQL语法支持类

(1)ONLY_FULL_GROUP_BY
    对于GROUP BY聚合操作，如果在SELECT中的列、HAVING或者ORDER BY子句的列，没有在GROUP BY中出现，那么这个SQL是不合法的。是可以理解的，因为不在 group 		by 的列查出来展示会有矛盾。
    在5.7中默认启用，所以在实施5.6升级到5.7的过程需要注意：

     Expression #1 of SELECT list is not in GROUP BY
    clause and contains nonaggregated column
    '1066export.ebay_order_items.TransactionID' which
    is not functionally dependent on columns in GROUP BY
    clause; this is incompatible with sql_mode=only_full_group_by
    
(2)ANSI_QUOTES 
    启用 ANSI_QUOTES 后，不能用双引号来引用字符串，因为它被解释为识别符，作用与 ` 一样。设置它以后，update t set f1="" ...，会报 Unknown column '' 	in 'field list 这样的语法错误。

(3)PIPES_AS_CONCAT 
    将 || 视为字符串的连接操作符而非 或 运算符，这和Oracle数据库是一样的，也和字符串的拼接函数 CONCAT() 相类似。
    
(4)NO_TABLE_OPTIONS 
	使用 SHOW CREATE TABLE 时不会输出MySQL特有的语法部分，如 ENGINE ，这个在使用 mysqldump 跨DB种类迁移的时候需要考虑。

(5)NO_AUTO_CREATE_USER
    字面意思不自动创建用户。在给MySQL用户授权时，我们习惯使用 GRANT ... ON ... TO dbuser 顺道一起创建用户。设置该选项后就与oracle操作类似，授权之前必	须先建立用户。5.7.7开始也默认了。

2. 数据检查类

(1)NO_ZERO_DATE 
	认为日期 '0000-00-00' 非法，与是否设置后面的严格模式有关。
	如果设置了严格模式，则 NO_ZERO_DATE 自然满足。但如果是 INSERT IGNORE 或 UPDATE IGNORE，'0000-00-00'依然允许且只显示warning
	如果在非严格模式下，设置了NO_ZERO_DATE，效果与上面一样，'0000-00-00'允许但显示warning；如果没有设置NO_ZERO_DATE，no warning，当做完全合法的值。

(2)NO_ZERO_IN_DATE
	情况与上面类似，不同的是控制日期和天，是否可为 0 ，即 2010-01-00 是否合法。

(3)NO_ENGINE_SUBSTITUTION
	使用 ALTER TABLE或CREATE TABLE 指定 ENGINE 时， 需要的存储引擎被禁用或未编译，该如何处理。启用NO_ENGINE_SUBSTITUTION时，那么直接抛出错误；不设		置此值时，CREATE用默认的存储引擎替代，ATLER不进行更改，并抛出一个 warning .

(4)STRICT_TRANS_TABLES 
	设置它，表示启用严格模式。
	注意 STRICT_TRANS_TABLES 不是几种策略的组合，单独指 INSERT、UPDATE出现少值或无效值该如何处理：
	前面提到的把 '' 传给int，严格模式下非法，若启用非严格模式则变成0，产生一个warning

(5)Out Of Range
	变成插入最大边界值
	A value is missing when a new row to be inserted does not contain a value for a non-NULL column that has no explicit DEFAULT clause 	in its definition
	
上面并没有囊括所有的 SQL Mode，选了几个代表性的，详细还是 看手册。
sql_mode一般来说很少去关注它，没有遇到实际问题之前不会去启停上面的条目。我们常设置的 sql_mode 是 ANSI、STRICT_TRANS_TABLES、TRADITIONAL，ansi和traditional是上面的几种组合。
(1)ANSI：更改语法和行为，使其更符合标准SQL
	相当于REAL_AS_FLOAT, PIPES_AS_CONCAT, ANSI_QUOTES, IGNORE_SPACE
(2)TRADITIONAL：更像传统SQL数据库系统，该模式的简单描述是当在列中插入不正确的值时“给出错误而不是警告”。
	相当于 STRICT_TRANS_TABLES, STRICT_ALL_TABLES, NO_ZERO_IN_DATE, NO_ZERO_DATE, ERROR_FOR_DIVISION_BY_ZERO, NO_AUTO_CREATE_USER, 		NO_ENGINE_SUBSTITUTION
(3)ORACLE：相当于 PIPES_AS_CONCAT, ANSI_QUOTES, IGNORE_SPACE, NO_KEY_OPTIONS, NO_TABLE_OPTIONS, NO_FIELD_OPTIONS, NO_AUTO_CREATE_USER
	无论何种mode，产生error之后就意味着单条sql执行失败，对于支持事务的表，则导致当前事务回滚；但如果没有放在事务中执行，或者不支持事务的存储引擎表，则可	能导致数据不一致。MySQL认为，相比直接报错终止，数据不一致问题更严重。于是 STRICT_TRANS_TABLES 对非事务表依然尽可能的让写入继续，比如给个"最合理"的		默认值或截断。而对于 STRICT_ALL_TABLES，如果是单条更新，则不影响，但如果更新的是多条，第一条成功，后面失败则会出现部分更新。
5.6.6 以后版本默认就是NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES，5.5默认为 '' 。
```

