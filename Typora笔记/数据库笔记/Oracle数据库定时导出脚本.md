1.数据导入的bat文件内容和解释

内容：（bat脚本需要将下面的#和之后的注释去掉）

```bash
echo 注意设置控制面板中的区域日期时间格式，设置为 yyyy-mm-dd  hh-mm-ss
echo 注意：解决windows7环境上午、下午取时间问题
pause
set THISDATETIME=%DATE:~0,4%%DATE:~5,2%%DATE:~8,2%%TIME:~0,2%%TIME:~3,2%%TIME:~6,2%
set THISDATETIME1=%DATE:~0,4%%DATE:~5,2%%DATE:~8,2%%TIME:~3,2%%TIME:~6,2%
echo  %THISDATETIME%
set name=NCC2005CS #导出的数据库名
set password=NCC2005CS #导出数据库的密码（bat脚本需要口岸##的注释去掉）

E:\app\oracleadmin\product\12.1.0\dbhome_2\BIN\expdp %name%/%password%@orcl directory=datadump cluster=n  dumpfile=%name%_%THISDATETIME%.dmp logfile=%name%_%THISDATETIME1%.log 
```

解释：

```bash
set name=NCC2005CS
set password=NCC2005CS
# 这是需要导出的数据库的用户名和密码，导出时需要自己修改
```

2.数据导入的bat文件内容和解释

内容：（bat脚本需要将下面的#和之后的注释去掉）

```bash
echo
set THISDATETIME=%DATE:~0,4%%DATE:~5,2%%DATE:~8,2%%TIME:~3,2%%TIME:~6,2%
echo  %THISDATETIME%
set name=ncc2005cs #导出的dmp文件的数据的用户名
set password=ncc2005cs #导出的dmp文件的数据的密码（暂时没有用）

E:\app\oracleadmin\product\12.1.0\dbhome_2\BIN\impdp ncczpm/ncczpm@orcl directory=datadump  dumpfile=NCC2005CS_20200819165318.DMP remap_schema=%name%:ncc2005dev table_exists_action=replace logfile=ncc2005dev%THISDATETIME%_impdp.log 
pause
```

解释:

```bash
set name=ncc2005cs #导出的dmp文件的数据的用户名
set password=ncc2005cs #导出的dmp文件的数据的密码（暂时没有用）

E:\app\oracleadmin\product\12.1.0\dbhome_2\BIN\impdp ncczpm/ncczpm@orcl directory=datadump  dumpfile=NCC2005CS_20200819165318.DMP remap_schema=%name%:ncc2005dev table_exists_action=replace logfile=ncc2005dev%THISDATETIME%_impdp.log 
#这个语句中的解释
E:\app\oracleadmin\product\12.1.0\dbhome_2\BIN\impdp
#oracle数据库impdp命令文件的地址
ncczpm/ncczpm@orcl
#登入那个用户进行导入，只要写任意一个有权限的用户就可以
directory=datadump 
#导入文件的地址，datadump是已经设置好的地址参数
dumpfile=NCC2005CS_20200819165318.DMP
#需要导入的dmp文件名称
remap_schema=%name%:ncc2005dev 
#REMAP_SCHEMA=source_schema:target_schema,将source_schema用户的数据导入到target_schema用户中
table_exists_action=replace
#如果target_schema用户存在这个表，进行替换
# 1.skip：默认操作
# 2.replace：先drop表，然后创建表，最后插入数据
# 3.append：在原来数据的基础上增加数据
# 4.truncate：先truncate，然后再插入数据
logfile=ncc2005dev%THISDATETIME%_impdp.log 
#导入输出的日志文件
```

