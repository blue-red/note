## mysql搭建（centos7）

1. 官网下载对应的安装包放置 /opt目录下（ mysql-5.7.25-linux-glibc2.12-x86_64.tar.gz）

2. 修改mysql目录的名称

   ```shell
   mv mysql-5.7.25-linux-glibc2.12-x86_64 mysql
   ```

3. 添加用户组

   ```shell
   groupadd mysql
   ```

4. 添加用户mysql，并归属到mysql用户组下

   ```shell
   useradd -g mysql mysql
   ```

5. 配置mysql数据目录

   ```shell
   cd mysql
   mkdir data
   ```

6. 初始化数据库

   ```shell
   cd bin/
   ./mysql --initialize --user=root --basedir=/opt/mysql --datadir=../data
   
   在日志中找到这一行
   [Note] A temporary password is generated for root@localhost: (dl9kU#OS9Ta
   (dl9kU#OS9Ta ：为数据库的初始密码
   ```

7. 查看是否启动数据库

   ```shell
    ps aux | grep mysql
   ```

8. 用初始化数据库密码，登陆数据库，并修改数据库密码

   ```
   ./mysqladmin -uroot -p password
   
   注：error: 'Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)'
   Check that mysqld is running and that the socket: '/tmp/mysql.sock' exists!
   
   因为在/etc/my.cnf中mysqld的socket=/var/lib/mysql/mysql.sock，所以导致mysql启动是在/tmp下找不到改文件。
   在登陆的时候，遇到该问题，请按以下方式解决
   第一种方式：直接修改my.cnf的socket的路径
   第二种方式（无权限时）：建立软连接
   ln -s /var/lib/mysql/mysql.sock /tmp/mysql.sock
   
   ```

   

   