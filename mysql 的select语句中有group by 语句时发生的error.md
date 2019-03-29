#### mysql使用group by 分组时出现的错误：
>ERROR 1055 (42000): Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column ‘advanced.dept.deptno’ which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by

**问题原因**:**only_full_group_by**语句的意思对于group by的聚合操作，如果在SELECT中的列，没有在GROUP BY中出现，那么这个SQL是不合法的，因为列不在GROUP BY从句中，也就是说查出来的列必须在group by后面出现否则就会报错，或者这个字段出现在聚合函数里面。

查看sql_mode命令参数：_select @@session.sql_mode;_
解决办法：输入命令<font color =green>```set sql_mode =’STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION’; ```</font>即可。
