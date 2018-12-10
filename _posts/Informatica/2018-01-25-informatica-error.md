---
layout: post
title: Informatica 常见错误汇总
categories: Informatica
description: Informatica 常见错误汇总
keywords: Informatica
---


**1. 创建 mapping 没有出错，创建 workfly 也没有出错，运行之后显示 success 了，但是数据表中没有数据**

**原因**：(1)数据源没有数据；(2)数据源不对。

**解决方法**：修改数据源

**2. 执行 ALTER TABLE QB_TEST02_PRODUCT DROP COLUMN PRO_ID1;
出现 ORA-39726: 不支持对压缩表执行添加 / 删除列操作。**


**解决方法**：先将该字段设置为 UNUSED，然后在删除 UNUSED 字段即可。
例如（执行如下顺序的 SQL 语句）：
```sql
ALTER TABLE test SET UNUSED COLUMN ROUTERADDR;
ALTER TABLE QB_TEST02_PRODUCT SET UNUSED COLUMN PRO_ID1;
---
ALTER TABLE test DROP UNUSED COLUMNS;
ALTER TABLE QB_TEST02_PRODUCT DROP UNUSED COLUMNS;
```

**3. workflow 执行成功了，但是报错信息还是跟原来一样或者目标表还是没有数据**

**解决方法**：在对 mapping 进行修改之后，相应的也需要对 workflow 中的 session 进行刷新。

**4. 在使用 Update Strategy 组件设计 mapping 的时候，必须对使用了 Update Strategy 的目标表设置伪主键（虚拟主键）**


**5. 日期类型的问题**


**解决方法**：在设计 mapping 的时候，如果数据源是文件，在导入源的时候日期默认是 string 类型，需要自己在导入数据源的时候手动修改数据类型伪 data/time；或者在数据源导入成功后手动修改数据源。

**6. 运行 workflow 时，在 log 中出现如下错误：**


```sql
SQL Error[FnName:Prepare -- [Sybase][ODBC Driver][Sybase IQ]Table '表名' not found]
```

**解决方法**：在 mapping 中，给只是用了数据源中部分字段的 SQ 组件添加 select 语句就可以解决问题了。

**7. 在 mapping 中使用 Joiner 组件时，如果勾选了 sorted input。则需要对接入的两个数据来源重新进行排序**

**8. 每个组件的数据类型需要严格对应上**

**9. 在 Informatica 中的输出文件不要使用 .bin 结尾，这样会让人误以为是 Linux 主机中的系统文件**


总之，做 ETL 开发最重要的还是要有一颗心细的心，善于发现数据差异的心。谢谢阅读。
