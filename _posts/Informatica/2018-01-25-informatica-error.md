---
layout: post
title: Informatica常见错误汇总
categories: Informatica
description: Informatica常见错误汇总
keywords: Informatica
---


<font color=#FF6827 size=3>**1、创建mapping没有出错，创建workfly也没有出错，运行之后显示success了，但是数据表中没有数据**</font>

**原因**：(1)数据源没有数据；(2)数据源不对。

**解决方法**：修改数据源

<font color=#FF6827 size=3>**2、执行ALTER TABLE QB_TEST02_PRODUCT DROP COLUMN PRO_ID1;
出现ORA-39726: 不支持对压缩表执行添加/删除列操作。**</font>


**解决方法**：先将该字段设置为UNUSED，然后在删除UNUSED字段即可。
例如（执行如下顺序的SQL语句）：
```SQL
ALTER TABLE test SET UNUSED COLUMN ROUTERADDR;
ALTER TABLE QB_TEST02_PRODUCT SET UNUSED COLUMN PRO_ID1;
---
ALTER TABLE test DROP UNUSED COLUMNS;
ALTER TABLE QB_TEST02_PRODUCT DROP UNUSED COLUMNS;
```

<font color=#FF6827 size=3>**3、workflow执行成功了，但是报错信息还是跟原来一样或者目标表还是没有数据**</font>

**解决方法**：在对mapping进行修改之后，相应的也需要对workflow中的session进行刷新。

<font color=#FF6827 size=3>**4、在使用Update Strategy组件设计mapping的时候，必须对使用了Update Strategy的目标表设置伪主键（虚拟主键）**</font>


<font color=#FF6827 size=3>**5、日期类型的问题**</font>


**解决方法**：在设计mapping的时候，如果数据源是文件，在导入源的时候日期默认是string类型，需要自己在导入数据源的时候手动修改数据类型伪data/time；或者在数据源导入成功后手动修改数据源。

<font color=#FF6827 size=3>**6、运行workflow时，在log中出现如下错误：**</font>

        
```SQL
SQL Error[FnName:Prepare -- [Sybase][ODBC Driver][Sybase IQ]Table '表名' not found]
``` 

**解决方法**：在mapping中，给只是用了数据源中部分字段的SQ组件添加select语句就可以解决问题了。

<font color=#FF6827 size=3>**7、在mapping中使用Joiner组件时，如果勾选了sorted input。则需要对接入的两个数据来源重新进行排序**</font>

<font color=#FF6827 size=3>**8、每个组件的数据类型需要严格对应上**</font>

<font color=#FF6827 size=3>**9、在Informatica中的输出文件不要使用.bin结尾，这样会让人误以为是Linux主机中的系统文件**</font>


总之，做ETL开发最重要的还是要有一颗心细的心，善于发现数据差异的心。谢谢阅读。
