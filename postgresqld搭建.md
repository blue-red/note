## PostgreSQL安装详细步骤（Centos7）

1. 从官网下载对应安装包，放置opt/pgsql/下

2. ```shell
    tar -xvzf postgresql-9.5-linux-x64-binaries.tar.gz，解压到目录pgsql下
    ```

3. 创建linux用户postgres (postgressql不能以root账号去初始化数据库)

   ```shell
   useradd postgres
   
   passwd postgres
   
   给postgres用户授权目录访问权限： chown postgres /opt/pgsql/pgsqlData/ 
   ```

4. 创建pgsql的数据目录 

    ```shell
    mkdir pgsqlData/
    ```

5. 然后切换postgres用户操作数据库

    ```shell
     su - postgres
    ```

    (切换之后建议修改`~/.bash_profile`将pgsql的bin目录加至环境变量中，方便直接使用pgsql相关命令)

6. 数据库初始化

    ```shell
    /opt/pgsql/bin/initdb -D /opt/pgsql/pgsqlData
    ```

7. 启动pgsql（默认端口5432）

    ```shell
    /opt/pgsql/bin/pg_ctl -D /opt/pgsql/pgsqlData -l logfile start
    ```

    1. -l 是日志文件位置，可以自己指定，不指定的话，默认同级目录下的

    2. -D 是指数据库数据目录不指定的话启动会报错，可以在

       ```shell
       ~/.bash_porfile 文件加入 export PGDATA=/pgsql/pgsqlData 解决
       ```

    3. ```shell
       ps -ef | grep postgres 查看pgsql的运行相关进程
       ```

8. 开启远程访问

    1. 修改配置文件postgresql.conf

       ```shell
       vi /opt/pgsql/pgsqlData/postgresql.conf
       
       #listen_addresses=’localhost’
       #将上面这行改成如下
       listen_addresses=’*’
       ```

    2. 修改配置文件pg_hba.conf

       ```shell
       vi /opt/pgsql/pgsqlData/pg_hba.conf 
       
       找到
       # IPv4 local connections:
       host    all             all             127.0.0.1/32            trust
       
       增加一行
       host	all				all				0.0.0.0/0				trust
       
       ```

    3. 重启数据库

       ```shell
       pg_ctl stop -D /opt/pgsql/pgsqlData
       pg_ctl start -D /opt/pgsql/pgsqlData
       ```

9. 登陆数据库

    1. 使用psql直接登陆，默认用户postgres

    2. ```shell
       psql -h 127.0.0.1 -d postgres -U postgres 
       ```

    3. 进入后"\l"查看当前所有的数据库（默认postgres，template0和1这3个库是不允许操作的）

    4. ```shell
       创建新的数据库执行： CREATE DATABASE test WITH OWNER=postgres ENCODING='UTF-8'
       ```

    5. 使用"\c test"切换当前数据库，"\d"查看当前数据库的所有表

    6. "\q"退出操作界面

10. 修改数据库默认字符集

    ```shell
    update pg_database set datallowconn = TRUE where datname = 'template0';
    
    \c template0
    
    update pg_database set datistemplate = FALSE where datname = 'template1';
    
    drop database template1;
    
    create data template1 with encoding = 'UTF8' LC_CTYPE = 'en_US.UTF-8' LC_COLLATE = 'en_US.UTF-8' template = template0;
    
    update pg_database set datallowconn = TRUE where datname = 'template1';
    
    \c template1
    
    update pg_databse set datallowconn = FALSE where datname = 'template0';
    
    ```

    