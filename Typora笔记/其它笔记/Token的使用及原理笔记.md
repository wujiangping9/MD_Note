# Token的使用及原理笔记

## 1.JWT：Json web token

​	特别适用于分布式站点的单点登录（SSO))

##2.JWT的构成

### (1)header

```js
{
  'typ': 'JWT',//声明类型
  'alg': 'HS256'//声明加密算法
}
```

然后需要对其进行base64加密：

```js

```

### (2).playload

存放有效信息的地方，有效信息分成三部分：

#### （1）标准中注册的声明

（建议，不强制使用）

- iss: jwt签发者
- sub: jwt所面向的用户
- aud: 接收jwt的一方
- exp: jwt的过期时间，这个过期时间必须要大于签发时间
- nbf: 定义在什么时间之前，该jwt都是不可用的.
- iat: jwt的签发时间
- jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。

#### （2）公共的声明

​	公共的声明可以添加任何的信息 （不安全，不建议使用敏感信息）

#### （3）私有的声明

​	私有声明是提供者和消费者所共同定义的声明，一般不建议存放敏感信息 

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

​	base64加密：

```js

```

### (3).signature(签证 )

​	个签证信息由三部分组成： 

- header (base64后的)
- payload (base64后的)
- secret

这个部分需要base64加密后的header和base64加密后的payload使用组成的字符串，然后通过header中声明的加密方式进行组合加密，然后就构成了jwt的第三部分 

```js
// javascript
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);
 
var signature = HMACSHA256(encodedString, 'secret'); // TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

最后组成JWT：

```js

```

注意：secret是保存在服务器端的，jwt的签发生成也是在服务器端的，secret就是用来进行jwt的签发和jwt的验证，所以，它就是你服务端的私钥，在任何场景都不应该流露出去。一旦客户端得知这个secret, 那就意味着客户端是可以自我签发jwt了。 