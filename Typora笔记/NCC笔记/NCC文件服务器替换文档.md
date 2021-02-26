### 1找到NCC实现文件上传到文件服务器，使用的接口位置

##### （1）NCC文件上传接口的位置

![image-20200911163352403](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200911163352403.png)

##### （2）attachment.xml文件为所有ncc文件上传所使用的接口

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no" ?>
<actions>
  <action>
    <name>platform.attachment.query</name>
    <label>查询附件列表</label>
    <clazz>nccloud.web.platform.attachment.action.AttachmentQueryAction</clazz>
  </action>
  <action>
    <name>platform.attachment.upload</name>
    <label>上传附件</label>
    <clazz>nccloud.web.platform.attachment.action.AttachmentUpLoadAction</clazz>
  </action>
  <action>
    <name>platform.attachment.download</name>
    <label>下载附件</label>
    <clazz>nccloud.web.platform.attachment.action.AttachmentDownLoadAction</clazz>
  </action>
  <action>
    <name>platform.attachment.delete</name>
    <label>删除附件</label>
    <clazz>nccloud.web.platform.attachment.action.AttachmentDeleteAction</clazz>
  </action>
  <action>
    <name>platform.attachment.limit</name>
    <label>查询附件上传限制</label>
    <clazz>nccloud.web.platform.attachment.action.AttachmentLimitQueryAction</clazz>
  </action>
  <action>
    <name>platform.attachment.createfolder</name>
    <label>附件对话框左侧树新建文件夹</label>
    <clazz>nccloud.web.platform.attachment.action.TreeNodeAddAction</clazz>
  </action>
  <action>
    <name>platform.attachment.lefttree</name>
    <label>附件对话框左侧树查询</label>
    <clazz>nccloud.web.platform.attachment.action.TreeNodeQueryAction</clazz>
  </action>
  <action>
    <name>platform.attachment.treerename</name>
    <label>附件对话框左侧树文件夹重命名</label>
    <clazz>nccloud.web.platform.attachment.action.TreeNodeRenameAction</clazz>
  </action>
  <action>
    <name>platform.attachment.treedelete</name>
    <label>附件对话框左侧树删除</label>
    <clazz>nccloud.web.platform.attachment.action.TreeNodeDeleteAction</clazz>
  </action>
  <action>
    <name>platform.attachment.move</name>
    <label>附件移动</label>
    <clazz>nccloud.web.platform.attachment.action.AttachmentMoveAction</clazz>
  </action>
    <action>
    <name>platform.attachment.rename</name>
    <label>附件修改名称</label>
    <clazz>nccloud.web.platform.attachment.action.AttachmentRenameAction</clazz>
  </action>
  <action>
    <name>platform.attachment.uploadurl</name>
    <label>上传附件URL</label>
    <clazz>nccloud.web.platform.attachment.action.AttachmentUpLoadURLAction</clazz>
  </action>
  <action>
    <name>platform.attachment.mobupload</name>
    <label>移动端上传附件</label>
    <clazz>nccloud.web.platform.attachment.action.AttachmentUpLoadAction</clazz>
  </action>
 <action>
    <name>platform.attachment.mobdownload</name>
    <label>移动端附件下载</label>
    <clazz>nccloud.web.platform.attachment.action.AttachmentDownLoadAction</clazz>
   </action>
 <action>
    <name>platform.attachment.preview</name>
    <label>附件预览</label>
    <clazz>nccloud.web.platform.attachment.action.AttachmentPreviewAction</clazz>
   </action>
</actions>
```

### 2.替换NCC的附件上传方式

第一种：将上图中的attachment.xml文件映射的接口Action类进行替换，所需的参数可以参考替换的Action是怎么实现的

![image-20200911154729076](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200911154729076.png)

第二种：将原先的Action进行重写覆盖，定义一个包名类名一样的类，将补丁打入home中也可以实现替换文件服务器的上传

第三种：如果要替换的服务器和NCC文件服务器实现大致一致，可以直接重写uap.pub.fs.client.FileStorageClient，进行文件服务器的替换

注意点

NCC上传下载查询附件以及删除等操作都会将信息存入数据库表sm_pub_filesystem中，附件的查询接口通过查询这个表获取上传那些附件（查询语句如下），（第一种和第二种都需要记得这个表中添加文件信息，否则NCC附件组件无法查询出上传的文件信息）

```sql
select pk, filepath, creator, isfolder, lasttime,filelength, isdoc, pk_doc, modifier, filedesc,scantimes, modifytime, filetype from sm_pub_filesystem where filepath=? or filepath like ?
-- '?'为上传附件所传的主键
```



![image-20200911155503590](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200911155503590.png)

### 3.NCC文件上传的具体流程

##### （1）下面的是NCC文件服务器使用上传客户端类(uap.pub.fs.client.FileStorageClient)

![image-20200911160149696](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200911160149696.png)

（2）流程图（以附件上传为例）

##### ![image-20200911162813016](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200911162813016.png)