**启动springboot启用不同的配置文件**

nohup java -jar  t.jar --spring.profiles.active=dev &

**如果不行就在jar包同级目录下创建config文件夹，把配置文件放进去**



mysql -u用户名 -p密码 数据库名 < 数据库名.sql





##### 一、在linux cenos安装tomcat

1、创建文件夹

服务器要开放8080端口

在 /usr/local文件夹下创建tomcat文件夹

mkdir  tomcat

解压下载的tomcat文件

```linux
tar -zxvf apache-tomcat-9.0.10.tar.gz
```

2、查看系统是否安装jdk

```linux
yum list installed |grep java
```

3、安装jdk

```linux
yum -y list java*
```

4、Linux当前目录所有文件移动到上一级目录

```linux
mv * ../
```

5、删除文件夹

```linux
rm -r    //r表示向下递归，不管多少级都删除。-rf不弹出提示
```



##### 二、linux终端常用命令

###### 1、防火墙

```linux
//关闭防火墙
systemctl stop firewalld 
//防火墙状态
systemctl status firewalld 
//开启防火墙
systemctl start firewalld 
//查看防火墙的状态
firewall-cmd --state
```

###### 2、端口

```
#开放关闭端口
firewall-cmd --zone=public --add-port=5672/tcp --permanent   # 开放5672端口
firewall-cmd --zone=public --remove-port=5672/tcp --permanent  #关闭5672端口

#配置立即生效
firewall-cmd --reload   

#查看防火墙所有开放的端口
firewall-cmd --zone=public --list-ports

#查看监听端口
netstat -lnpt

#查看某个服务占用的端口
ps -ef | grep java

#检查端口被占用
netstat -lnpt |grep 5672

#查看进程的详细信息
ps 5672

#中止进程
kill -9 5672
```

3、启动jar文件命令

nohup java -jar XXX.jar &



##### 三、windowns终端常用命令

###### 1、查看端口号是否被占用    

```win
netstat -nao
```

###### 2、结束PID对应的进程 

```
tasklist|findstr "PID"
```

###### 3、强制结束进程

~~~
taskkill -PID 2188 -F
~~~

###### 4、根据端口号查询进程

~~~
netstat -ano | findstr 8080
~~~

后台直接用postman访问的话用“[http://localhost:8080/你的地址”,去掉dev-api，不登录访问还需要在SecurityConfig](http://localhost:8080/你的地址",去掉dev-api，不登录访问还需要在SecurityConfig里面设置你的地址，这和application中的服务端口和context-path访问路径有关)







##### 四、zookeeper服务命令

###### 1、启动服务

```linux
./zkServer.sh start
```

###### 2、查看状态

```linux
./zkServer.sh status
```

###### 3、客户端连接

```linux
 ./zkCli.sh
```







##### 五、consul服务命令

###### 1、进入

```linux
./consul
```

![](E:\tools\notes\图片\捕获.PNG)

###### 2、启动服务

```linux
./consul agent -dev -ui -node=consul-dev -client=私网ip  172.17.191.235
```

###### 3、访问

```
公网ip:8500
```



##### 六、操作数据库相关

###### 1、linux导入sql文件教程

```linux
1、登录数据库
mysql -uroot -p

2、查看数据库
show databases;

3、创建数据库
create database 数据库名;

4、选择库
use 数据库名;

5、导入sql文件到库中
source /usr/local/sql/lqvm.sql

6、查看表
show tables

```

###### 2、其他相关命令

```
# 检查并且显示Apache相关安装包
[root@localhost ~]# rpm -qa | grep mysql

# 删除MySql
[root@localhost ~]# yum remove -y mysql mysql mysql-server mysql-libs compat-mysql51
或
[root@localhost ~]# rpm -e mysql-community-libs-5.7.20-1.el7.x86_64 --nodeps
或
[root@localhost ~]# yum -y remove mysql-community-libs-5.7.20-1.el7.x86_64

# 查看MySql相关文件
[root@localhost ~]# find / -name mysql

# 重启MySql服务
[root@localhost ~]# service mysqld restart

# 查看MySql版本
[root@localhost ~]# yum repolist all | grep mysql

# 查看当前的启动的 MySQL 版本
[root@localhost ~]# yum repolist enabled | grep mysql

# 通过Yum来安装MySQL,会自动处理MySQL与其他组件的依赖关系
[root@localhost ~]# yum install mysql-community-server

# 查看MySQL安装目录
[root@localhost ~]# whereis mysql

# 启动MySQL服务
[root@localhost ~]# systemctl start mysqld

# 查看MySQL服务状态
[root@localhost ~]# systemctl status mysqld

# 关闭MySQL服务
[root@localhost ~]# systemctl stop mysqld

# 测试MySQL是否安装成功
[root@localhost ~]# mysql

# 查看MySql默认密码
[root@localhost ~]# grep 'temporary password' /var/log/mysqld.log

# 查看所有数据库
mysql>show databases;

# 退出登录数据库
mysql>exit;

# 查看所有数据库用户
mysql>SELECT DISTINCT CONCAT('User: ''',user,'''@''',host,''';') AS query FROM mysql.use
```





##### 七、在linux服务器安装mysql5.7

###### 1、确保服务器在最新状态（可选）

```
[root@localhost ~]# yum -y update
如果显示以下内容说明已经更新完成
Replaced:
grub2.x86_64 1:2.02-0.64.el7.centos grub2-tools.x86_64 1:2.02-0.64.el7.centos
Complete!
```

###### 2、重启服务器（可选）

```
[root@localhost ~]# reboot
```

###### 3、下载MySql安装包

```
[root@localhost ~]# rpm -ivh http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
或
[root@localhost ~]# rpm -ivh http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
```

###### 4、安装MySql

```
[root@localhost ~]# yum install -y mysql-server
或
[root@localhost ~]# yum install mysql-community-server

如果显示以下内容说明安装成功
Complete!
```

###### 5、设置开机启动

```
[root@localhost ~]# systemctl enable mysqld.service
```

###### 6、检查是否已经打开了开机自启动

```
[root@localhost ~]# systemctl list-unit-files | grep mysqld

如果显示以下内容说明已经完成自动启动安装
mysqld.service enabled
```

###### 7、设置开启服务

```
[root@localhost ~]# systemctl start mysqld.service
```

###### 8、查看Mysql默认密码

```
[root@localhost ~]# grep 'temporary password' /var/log/mysqld.log
```

###### 9、登录mysql,输入用户名密码

```
[root@localhost ~]# mysql -uroot -p
```

###### 10、修改密码(注意命令的分号)

```
查看mysql密码策略
SHOW VARIABLES LIKE 'validate_password%';
首先设置密码器强度为low
set global validate_password_policy=LOW;
设置密码长度为6（可选）
set global validate_password_length=6; 
修改密码
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456'; 
```

###### 11、开启远程登录

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
```

###### 12、设置立即生效

```
flush privileges;
```

###### 13、可选

![](E:\tools\notes\图片\微信图片_20200623110217.png)

##### 14、mongodb 拒绝连接

   1、mongodb的配置文件中的bind_ip 默认为127.0.0.1，默认只有本机可以连接。 此时，需要将bind_ip配置为0.0.0.0，表示接受任何IP的连接。

   2、防火墙阻止了mongodb端口。



查看centos版本

cat /etc/redhat-release









mysql错误

```
1055 - Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'lqvm_zhsl.zhlq_device_data.expand_2' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
```

解决

文件地址一般在：/etc/my.cnf，/etc/mysql/my.cnf
找到sql-mode的位置，去掉ONLY_FULL_GROUP_BY
然后重启MySQL   **service mysqld restart**；



```
sql-mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

![](E:\tools\notes\图片\微信图片_20200804112319.png)

不区分大小写

文件地址一般在：/etc/my.cnf，/etc/mysql/my.cnf

lower_case_table_names=1



解压

tar zxvf test.tgz -C 指定目录







```
#启动
./nginx

#停止，此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程
./nginx -s stop

#停止，此方式停止步骤是待nginx进程处理任务完毕进行停止
./nginx -s quit

#重新加载配置文件，Nginx服务不会中断
./nginx -s reload
```

