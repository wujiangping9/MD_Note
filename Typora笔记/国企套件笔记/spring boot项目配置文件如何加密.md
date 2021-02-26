1.加密命令

```cmd
# jasypt-1.9.2.jar 项目所依赖的加密解密jar包版本
# password=EbfYkitulv73I2p0mXI50JMXoaxZTKJ7 配置文件中配置的密文
# algorithm=PBEWithMD5AndDES 默认加密方式
# input=GqTj#*12 需要加密的字符串
java -cp jasypt-1.9.2.jar org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI password=EbfYkitulv73I2p0mXI50JMXoaxZTKJ7 algorithm=PBEWithMD5AndDES input=GqTj#*12
```

2.解密命令

```cmd
# jasypt-1.9.2.jar 项目所依赖的加密解密jar包版本
# password=EbfYkitulv73I2p0mXI50JMXoaxZTKJ7 配置文件中配置的密文
# algorithm=PBEWithMD5AndDES 默认加密方式
# input=GqTj#*12 需要解密的字符串
java -cp jasypt-1.9.2.jar org.jasypt.intf.cli.JasyptPBEStringDecryptionCLI password=EbfYkitulv73I2p0mXI50JMXoaxZTKJ7 algorithm=PBEWithMD5AndDES input=PguZbRWl+rFF7cqo0PSpRg==
```

