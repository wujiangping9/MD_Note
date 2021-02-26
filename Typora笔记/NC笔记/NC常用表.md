# NC常用表

1.元数据的表：

| 表名           | 作用                | where条件（例子）                                            | 待定 |
| :------------- | ------------------- | :----------------------------------------------------------- | ---- |
| md_ormap       | 元数据列classid信息 | where tableid like '%job%' and columnid like '%num%'         |      |
| md_table       | 元数据模板信息      | where displayname like '招聘%'                               |      |
| md_column      | 元数据列信息        | where name like 'glbdef%' and tableid = 'hi_psndoc_ctrt'     |      |
| md_property    | 元数据列属性信息    | where displayname like '招聘%' and name='restnum'            |      |
| md_class       | 元数据模板类信息    | where displayname like '招聘%'                               |      |
| md_enumvalue   | 元数据模块信息      | where name  like '招聘%'                                     |      |
| md_component   | y元数据模块详细信息 | where namespace = 'hrrm' and id in (select distinct componentID from md_class where name = 'rmdemandvo') |      |
| md_module      | 元数据模块实体      | 暂无                                                         |      |
| md_db_relation | 元数据关系          | where  endtablieid like ‘hi_psndoc_ctrt@@@%’                 |      |
| md_association | 元数据团体          | where name like 'hi_psndoc_ctrt%'                            |      |

