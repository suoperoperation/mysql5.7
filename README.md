# mysql5.7
mysql5.7 install
下载并安装MySQL官方的 Yum Repository

wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm

使用上面的命令就直接下载了安装用的Yum Repository，大概25KB的样子，然后就可以直接yum安装了

yum -y install mysql57-community-release-el7-10.noarch.rpm


之后就开始安装MySQL服务器

yum -y install mysql-community-server

至此MySQL就安装完成了，然后是对MySQL的一些设置

MySQL数据库设置

首先启动MySQL

systemctl start mysqld.service

此时MySQL已经开始正常运行，不过要想进入MySQL还得先找出此时root用户的密码，通过如下命令可以在日志文件中找出密码：

grep "password" /var/log/mysqld.log

如下命令进入数据库：

mysql -uroot -p 

输入初始密码，此时不能做任何事情，因为MySQL默认必须修改密码之后才能操作数据库：

mysql>     ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';

这里有个问题，新密码设置的时候如果设置的过于简单会报错：

原因是因为MySQL有密码设置的规范，具体是与validate_password_policy的值有关：

MySQL完整的初始密码规则可以通过如下命令查看：

mysql> SHOW VARIABLES LIKE 'validate_password%';

+--------------------------------------+-------+
| Variable_name | Value |
+--------------------------------------+-------+
| validate_password_check_user_name | OFF |
| validate_password_dictionary_file | |
| validate_password_length | 4 |
| validate_password_mixed_case_count | 1 |
| validate_password_number_count | 1 |
| validate_password_policy | LOW |
| validate_password_special_char_count | 1 |
+--------------------------------------+-------+
7 rows in set (0.01 sec)


初始情况下第一个的值是ON，validate_password_length是8。可以通过如下命令修改：

mysql> set global validate_password_policy=0;

mysql> set global validate_password_length=1;

设置之后就是我上面查出来的那几个值了，此时密码就可以设置的很简单，例如1234之类的。到此数据库的密码设置就完成了。

但此时还有一个问题，就是因为安装了Yum Repository，以后每次yum操作都会自动更新，需要把这个卸载掉：

yum -y remove mysql57-community-release-el7-10.noarch

此时才算真的完成了。

