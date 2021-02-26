# 项目BUG处理笔记

## 1.java.sql.SQLException

​	post请求，在查询sql是出现：java.sql.SQLException

​	在本地可以没问题的运行，打补丁到服务器上时。会出现查询失败的情况

​	分析：数据库连接失败，缺少缺少Map<String,String> centerMap，导致没有datasource无法连接数据库，查询用户失败

​	(1)之前代码：	

```java
public String ssoLoginURL(String userId,Map<String,String> centerMap) {
		userId = DESUtils.formatParam(userId);
		String URL="";
		try {
			String decryptorName = DESUtils.decrypt(userId);
			String[] strArr = decryptorName.split(",");
			if((System.currentTimeMillis()-Long.valueOf(strArr[1]))/1000<200){
				BusiCenterVO bcVO = createBusiCenterVO(centerMap);
				String sql = " select sm_user.user_code from sm_user where user_code = '" + strArr[0] + "'";
				String user_code = (String) new BaseDAO().executeQuery(sql,new ColumnProcessor());
				if ( user_code != null && !("".equals(StringUtils.trim(user_code)))) {
					String[] info = DESUtils.createURL(user_code);
					URL = info[1];
					tokenMap.put(user_code,info[0]);
				}
			}
		} catch (Exception e) {
			Logger.error(e.getMessage());
		}
		return URL;
	}
```
​	(2)原因：缺少Map<String,String> centerMap

```java
	BusiCenterVO bcVO = createBusiCenterVO(centerMap);
	if (bcVO != null) {
		InvocationInfoProxy.getInstance().setBizCenterCode(bcVO.getCode());
		InvocationInfoProxy.getInstance().setUserDataSource(bcVO.getDataSourceName());

	}

```

​	(3)修改后的代码

```java
public String ssoLoginURL(String userId,Map<String,String> centerMap) {
		userId = DESUtils.formatParam(userId);
		String URL="";
		try {
			String decryptorName = DESUtils.decrypt(userId);
			String[] strArr = decryptorName.split(",");
			if((System.currentTimeMillis()-Long.valueOf(strArr[1]))/1000<200){
				BusiCenterVO bcVO = createBusiCenterVO(centerMap);
				if (bcVO != null) {
					InvocationInfoProxy.getInstance().setBizCenterCode(bcVO.getCode());
																                 InvocationInfoProxy.getInstance().setUserDataSource(bcVO.getDataSourceName());
				}
				String sql = " select sm_user.user_code from sm_user where user_code = '" + strArr[0] + "'";
				String user_code = (String) new BaseDAO().executeQuery(sql,new ColumnProcessor());
				if ( user_code != null && !("".equals(StringUtils.trim(user_code)))) {
					String[] info = DESUtils.createURL(user_code);
					URL = info[1];
					tokenMap.put(user_code,info[0]);
				}
			}
		} catch (Exception e) {
			Logger.error(e.getMessage());
		}
		return URL;
	}
```

#知识点：

##1.DataSource 

#####1.什么是数据源
​	JDBC2.0 提供了javax.sql.DataSource接口，它负责建立与数据库的连接，当在应用程序中访问​	数据库时 
不必编写连接数据库的代码，直接引用DataSource获取数据库的连接对象即可。用于获取操作	数据Connection对象。

#####2.数据源与数据库连接池
​	数据源建立多个数据库连接，这些数据库连接会保存在数据库连接池中，当需要访问数据库	时，只需要从数据库连接池中获取空闲的数据库连接，当程序访问数据库结束时，数据库连接会放回数据库连接池中。
#####3.数据源DataSource与JNDI
​	数据源DataSource是JNDI资源的一种，很简单，就是将“DataSource”字符串名称与真正的​	​	DataSource对象绑定起来，方便获取。

##### 4.数据库连接池的优势

​	传统的JDBC访问数据库技术，每次访问数据库都需要通过数据库驱动器Driver和数据库名称以		及密码等等资源建立数据库连接。这样的连接存在两大问题：

​	1. 频繁的建立数据库连接与断开数据库，这样会消耗大量的资源和时间，降低性能。

​	2. 数据库的连接需要用户名和密码等等，这些需要一定的内存和CPU一定开销。






