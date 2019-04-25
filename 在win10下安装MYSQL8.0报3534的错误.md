#### 在win10下安装MYSQL8.0,只到“net start mysql”这一步报错:3534的错误：
解决方法：

1、环境变量PATH添加完成（例如：C:\Program Files\mysql-5.7.13-winx64\bin）；

2、在MYSQL安装目录下，新建data目录。

之前参考百度知道中“MySQL下载安装、配置与使用（win10x64）“这篇文章时，当把从官网下载好特定版本的MYSQL安装包，解压放到相应目录后，进入MYSQL的安装目录下，新建一个默认配置文件my.ini。

在my.ini（.ini文件是window里面的配置文件，里面各种默认的数据。）中写入如下内容。

复制代码
 

```
1 [mysql]
 2 # 设置mysql客户端默认字符集
 3 default-character-set=utf8 
 4 [mysqld]
 5 #设置3306端口
 6 port = 3306 
 7 # 设置mysql的安装目录
 8 basedir=C:\Program Files\mysql-5.7.13-winx64
 9 # 设置mysql数据库的数据的存放目录
10 datadir=C:\Program Files\mysql-5.7.13-winx64\data
11 # 允许最大连接数
12 max_connections=200
13 # 服务端使用的字符集默认为8比特编码的latin1字符集
14 character-set-server=utf8
15 # 创建新表时将使用的默认存储引擎
16 default-storage-engine=INNODB 
```

复制代码
保存后，my.ini会替换掉默认的my-default.ini文件。(其中红线标注出，依照个人情况替换)（或者是my.cnf也可以）

3、将MYSQL卸载、重装、初始化，最后开启MYSQL服务。

用管理员身份打开cmd命令行，依次输入以下命令：

1 C:Windows\system32>mysqld --romve  //删除mysql服务
2 C:Windows\system32>mysqld --install //安装mysql服务 
3 C:Windows\system32>mysqld --initialize //一定要初始化 （会通过初始化命令自动创建data文件夹）
4 C:Windows\system32>net start mysql
第四小步初始化很重要，执行完初始化命令后，空的data目录就新生成了如下文件：