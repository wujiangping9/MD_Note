# 友空间（其他OA）的DHR单点登录

(此处以友空间单点为例子，其他任何系统单点都是以下相同逻辑)

### 1.单点跳转地址的修改，用于在地址栏输出，进行get请求，做用户校验

##### （1）web端单点（此处也可省略）

```js
单点地址输入为：http://ip:port/hrself/#/spaceSsoLogin?usercode=xxxx&token=yyy
这个地址?后面的为加密后的参数，根据登录需要进行填写，此处是做路由跳转到单点的路由界面
```

![image-20200905131349667](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200905131349667.png)

##### （2）移动端单点

移动端无需静态界面跳转，可直接路由界面发单点请求

### 2.进入单点的路由界面，进行单点请求发送

##### （1）web端进行单点请求，进行登录

##### ![image-20200905133951428](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200905133951428.png)

##### （2）移动端单点，进行登录（如无特殊需求，登录接口可以和web端一个接口）

![image-20200905134311517](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200905134311517.png)

### 3.进行单点登录接口的编写（此处以web端移动端使用一个单点登录接口进行登录做例子）

![image-20200905134945483](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200905134945483.png)

最后会走向LoginFacade.java类的方法

![image-20200905135019025](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200905135019025.png)

### 4.NC后端的登录编写，后面的就是对接口传入的参数进行校验，是否满足登录条件，后续校验逻辑根据不同项目的需要进行编写

![image-20200905135710030](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200905135710030.png)

具体校验登录信息的逻辑编写

![image-20200905140102259](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200905140102259.png)

![image-20200905140258510](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200905140258510.png)