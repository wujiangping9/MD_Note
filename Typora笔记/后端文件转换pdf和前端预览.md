1.安装openOffice,自动点击下一步安装即可

```http
-- 下载地址
http://www.openoffice.org/download/
-- 使用api
http://jodconverter.sourceforge.net/api/
```

2.启动openOffice服务

```sh
## 查看端口对应的pid
 netstat -ano|findstr "8100"

## 启动
soffice -headless -accept="socket,host=127.0.0.1,port=8100;urp;" -nofirststartwizard

## 查看pid对应的服务程序名
 tasklist|findstr "ipd值"
```

![image-20200914093613120](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200914093613120.png)

