## 查询mysql所有表数据、字段信息

#### 1.根据库名获取所有表的信息

```mysql
SELECT
    *
FROM
    information_schema.`TABLES`
WHERE
    TABLE_SCHEMA = 'erp';
```

#### 2.根据库名获取所有表名称和表说明

```mysql
SELECT
    TABLE_NAME,
    TABLE_COMMENT
FROM
    information_schema.`TABLES`
WHERE
    TABLE_SCHEMA = 'erp';
```

![img](https://img2018.cnblogs.com/blog/1289484/201901/1289484-20190119110751842-70145629.png)

#### 3.根据库名获取所有的字段信息

```mysql
SELECT
    TABLE_SCHEMA AS '库名',
    TABLE_NAME AS '表名',
    COLUMN_NAME AS '列名',
    ORDINAL_POSITION AS '列的排列顺序',
    COLUMN_DEFAULT AS '默认值',
    IS_NULLABLE AS '是否为空',
    DATA_TYPE AS '数据类型',
    CHARACTER_MAXIMUM_LENGTH AS '字符最大长度',
    NUMERIC_PRECISION AS '数值精度(最大位数)',
    NUMERIC_SCALE AS '小数精度',
    COLUMN_TYPE AS 列类型,
    COLUMN_KEY 'KEY',
    EXTRA AS '额外说明',
    COLUMN_COMMENT AS '注释'
FROM
    information_schema.`COLUMNS`
WHERE
    TABLE_SCHEMA = 'erp'
ORDER BY
    TABLE_NAME,
    ORDINAL_POSITION;
```

![img](https://img2018.cnblogs.com/blog/1289484/201901/1289484-20190119110826313-687227710.png)

#### 4.根据库名获取所有的库和表字段的基本信息

```mysql
SELECT
    C.TABLE_SCHEMA AS '库名',
    T.TABLE_NAME AS '表名',
    T.TABLE_COMMENT AS '表注释',
    C.COLUMN_NAME AS '列名',
    C.COLUMN_COMMENT AS '列注释',
    C.ORDINAL_POSITION AS '列的排列顺序',
    C.COLUMN_DEFAULT AS '默认值',
    C.IS_NULLABLE AS '是否为空',
    C.DATA_TYPE AS '数据类型',
    C.CHARACTER_MAXIMUM_LENGTH AS '字符最大长度',
    C.NUMERIC_PRECISION AS '数值精度(最大位数)',
    C.NUMERIC_SCALE AS '小数精度',
    C.COLUMN_TYPE AS 列类型,
    C.COLUMN_KEY 'KEY',
    C.EXTRA AS '额外说明'
FROM
    information_schema.`TABLES` T
LEFT JOIN information_schema.`COLUMNS` C ON T.TABLE_NAME = C.TABLE_NAME
AND T.TABLE_SCHEMA = C.TABLE_SCHEMA
WHERE
    T.TABLE_SCHEMA = 'erp' 
ORDER BY
    C.TABLE_NAME,
    C.ORDINAL_POSITION;
```

![img](https://img2018.cnblogs.com/blog/1289484/201901/1289484-20190119110920624-939250238.png)