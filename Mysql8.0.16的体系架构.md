
     
### Mysql8.0.16的体系架构
***
#### 连接层
- client connectors层

```
不同的语言和MySQL的交互（java ，pyhthon c++,scala,php,ruby,go,javascript)
```

#### server层
- services&utilities:系统管理和控制工具（备份和恢复，mysql的复制和集群）
- connection pool：连接池（管理缓存用户连接，用户名，密码，权限校验，线程处理等需要缓存的需求）
- sql interfance：sql接口，接受用户的sql命令，并且返回sql命令结果
- parser：sql命令传递到解析器的时候会被解析器验证和解析
- optimizer ：查询优化器（sql语句在查询之前会先进行sql语句的优化）
#### 存储引擎层
- pluggable storage engines：存储引擎，mysq的存储引擎是插件式的，用哪个引擎就定义那个引擎。（innodb是mysql8.0.16的默认存储引擎）
#### 底层存储层
- file system;文件系统，存储mysql的所有东西。
- logs and files：mysql的一些日志记录。





