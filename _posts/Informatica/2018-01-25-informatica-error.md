---
layout: post
title: Informatica常见错误汇总
categories: Informatica
description: Informatica常见错误汇总
keywords: Informatica
---

1. 问题：创建mapping没有出错，创建workfly也没有出错，运行之后显示success了，但是数据表中没有数据。
原因：（1）数据源没有数据；（2）数据源不对。
解决方法：修改数据源

2. 问题：执行——ALTER TABLE QB_TEST02_PRODUCT DROP COLUMN PRO_ID1;
出现——ORA-39726: 不支持对压缩表执行添加/删除列操作

    解决方式：先将该字段设置为UNUSED，然后在删除UNUSED字段即可。
例如（执行如下顺序的SQL语句）：
    ```
    ALTER TABLE test SET UNUSED COLUMN ROUTERADDR;
    (ALTER TABLE QB_TEST02_PRODUCT SET UNUSED COLUMN PRO_ID1;)
    ALTER TABLE test DROP UNUSED COLUMNS;
    (ALTER TABLE QB_TEST02_PRODUCT DROP UNUSED COLUMNS;)
    ```


3. 问题：workflow执行成功了，但是报错信息还是跟原来一样或者目标表还是没有数据。

    解决办法：在对mapping进行修改之后，相应的也需要对workflow中的session进行刷新。

4. 问题：在使用Update Strategy组件设计mapping的时候，必须对使用了Update Strategy的目标表设置伪主键（虚拟主键）。如下图：

5. 问题：日期类型的问题

    解决办法：在设计mapping的时候，如果数据源是文件，在导入源的时候日期默认是string类型，需要自己在导入数据源的时候手动修改数据类型伪data/time；或者在数据源导入成功后手动修改数据源。

6. 问题：运行workflow时，在log中出现如下错误： 
        
```
SQL Error[FnName:Prepare -- [Sybase][ODBC Driver][Sybase IQ]Table '表名' not found]
``` 

    解决办法：在mapping中，给只是用了数据源中部分字段的SQ组件添加select语句就可以解决问题了。

7. 问题：在mapping中使用Joiner组件时，如果勾选了sorted input。则需要对接入的两个数据来源进行排序（不管之前是否已经进行了排序，在使用的Joiner组件的时候都需要重新进行排序）

8. 问题：数据类型需要严格对应上。

9. 问题：在Informatica中的输出文件不要使用.bin结尾，这样会让人误以为是Linux主机中的系统文件。

总之，做这类开发最重要的还是要有一颗心细的心，善于发现数据差异的心。谢谢阅读。
