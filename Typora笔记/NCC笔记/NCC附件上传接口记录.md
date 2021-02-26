1.所有关于附件的接口

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

2.获取附件预览地址

```java
//1.下载那块可以获取预览地址
uap.pub.fs.client.RestFileTransfer
//2.包含所有的附件接口服务
uap.pub.fs.client.FileStorageClient
```

3.文件查询所用的表

```sql
select pk, filepath, creator, isfolder, lasttime,filelength, isdoc, pk_doc, modifier, filedesc,scantimes, modifytime, filetype from sm_pub_filesystem where filepath=? or filepath like ?
-- '?'为上传附件所传的主键
```

