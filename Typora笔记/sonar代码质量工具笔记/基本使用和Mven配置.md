1.mven的settings配置

```xml
<profiles>
    <profile>
        <id>sonar</id>
        <properties>
            <sonar.jdbc.url>jdbc:postgresql://10.16.3.51:5432/postgres?currentSchema=sonarqube</sonar.jdbc.url>
            <sonar.jdbc.driver>org.postgresql.Driver</sonar.jdbc.driver>
            <sonar.jdbc.username>postgres</sonar.jdbc.username>
            <sonar.jdbc.password>postgres</sonar.jdbc.password>
            <sonar.host.url>http://10.16.3.51:9000</sonar.host.url> <!-- Sonar服务器访问地址 -->
            <sonar.login>admin</sonar.login>
            <sonar.password>admin</sonar.password>
        </properties>
    </profile>
</profiles>
<activeProfiles>
        <activeProfile>sonar</activeProfile>
</activeProfiles>
```

2.maven打包分析命令

```cmd
mvn sonar:sonar
```

3.sonar使用官网地址
	https://docs.sonarqube.org/latest/

4.设置忽略分析文件设置

```xml
<sonar.exclusions>
            appraisal/*/src/main/java/com/dhr/platform/da/*/entity/**/*,
            appraisal/*/src/main/java/com/dhr/platform/da/*/mapper/**/*,
            appraisal/*/src/main/resources/**/*
        </sonar.exclusions>
```

5.分析前端代码，使用用sonar-scanner

```cmd
sonar-scanner -D"sonar.login=admin" -D"sonar.password=admin"
```

6.分析前端配置文件

```properties
sonar-project.properties
# must be unique in a given SonarQube instance
# 项目的Key，可任意，必须唯一
sonar.projectKey=com.dhr:platform-font
sonar.sources=src

# --- optional properties ---

# defaults to project key
# 项目名称
#sonar.projectName=My project
# defaults to 'not provided'
# 项目版本
#sonar.projectVersion=1.0

# Path is relative to the sonar-project.properties file. Defaults to .
# 需要扫描的源代码目录
#sonar.sources=.

# Encoding of the source code. Default is default system encoding
# 项目编码格式
#sonar.sourceEncoding=UTF-8

# 次要配置
#指定项目配置文件的路径（此选项与sonar.projectBaseDir属性不兼容）
#sonar-scanner -Dproject.settings=../myproject.properties

```

7.sonar-sacnner的配置文件sonar-scanner.properties

```properties
#Configure here general information about the environment, such as SonarQube server connection details for example
#No information about specific project should appear here

#----- Default SonarQube server
#sonar.host.url=http://localhost:9000

#----- Default source code encoding
#sonar.sourceEncoding=UTF-8
sonar.jdbc.url=jdbc:postgresql://10.16.3.51:5432/postgres?currentSchema=sonarqube
useUnicode=true&characterEncoding=utf8$rewriteBatchedStatements=true$useConfigs=maxPerformance$useSSL=false
sonar.jdbc.username=postgres
sonar.jdbc.password=postgres
sonar.host.url=http://10.16.3.51:9000


```

8.jenkins的插件地址

```js
http://updates.jenkins-ci.org/download/plugins/
```

9.gitlab+jenkins+sonarqube搭建代码管理系统

```js
https://www.cnblogs.com/lixianguo/p/12518568.html
```

