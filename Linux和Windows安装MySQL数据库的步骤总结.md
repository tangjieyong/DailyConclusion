# Windows安装MySQL数据库

​    <https://www.cnblogs.com/weigege2015/p/8116445.html>

1. 官网下载数据库的压缩包，解压之后内部有默认的my.ini配置文件和解压的安装文件，没有就自己创建。主要包含的是端口号和安装目录以及安装目录下data文件夹的位置（起初是没有的，安装过程中自动创建，这里指定位置就可以了）


```xml
[mysqld]

port = 3306

basedir=D:\MySQL\3306\mysql-5.7.23-winx64

datadir=D:\MySQL\3306\mysql-5.7.23-winx64\data\

max_connections=200

character-set-server=utf8

default-storage-engine=INNODB

sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

[mysql]

default-character-set=utf8
 
```

         2. <u>配置环境变量</u>，方便在任何目录下执行mysql指令，在管理员权限下执行cmd,进入安装目录的bin文件夹下，输入 mysqld -install命令，显示安装成功即可，若提示已经存在，先去控制面板卸载，我的电脑内搜索MySQL删除对应的文件夹，打开注册表，删除MySQL文件夹，sc mysql delete重启 , mysqld --initialize-insecure --user=mysql会在安装目录下出现data文件夹 ，

         3. net start mysql   打开mysql服务，mysql -uroot -p 不用密码直接登入，进入mssql  set password=password('新密码')为MySQL设置密码

         4. use mysql选择系统库，

              **grant 权限1，权限2......  on  数据库名称.表名称 to 用户名@用户地址 identified by '连接口令';**

            ```mysql
            create user 用户名   为数据库创建用户
            
            GRANT ALL PRIVILEGES ON *.* TO '用户名'@'%' IDENTIFIED BY '密码' WITH GRANT OPTION;
            为用户分配所有权限，可以使用指定的密码并且任意的ip地址进行访问
            flush privillage
            
            ```


# Linux安装MySQL数据库

[安装MySQL步骤]: https://www.cnblogs.com/boris-et/p/8316754.html
[开放3306端口]: https://www.cnblogs.com/kenshinobiy/articles/4251598.html