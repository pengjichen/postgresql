# postgresq 普通用户部署


1. 该版本号: postgresql 9.6.10, 安装环境为ubuntu 16.04.2
```
获取路径：

https://github.com/pengjichen/postgresql.git
```


2. 设置依赖路径和bin路径
```
编辑文件~/.profile,添加lib路径和bin路径

export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/home/ubuntu/pgsql/lib:/home/ubuntu/pgsql/otherlib"
export PATH="$PATH:/home/ubuntu/pgsql/bin"
```
```
刷新配置

source ~/.profile
```


3. 初始化数据库实例
```
initdb -D /data/psqldb
```

4. 修改新建数据库实例中的postgresql.conf中的unix_socket_directories
```
unix_socket_directories='/tmp'
```

5. 启动数据库实例
```
pg_ctl -D /data/psqldb -l logfile start

测试登录数据库 默认用户为当前用户 例如:ubuntu
psql postgres

```


6. 修改用户ubuntu密码
```
psql -qd postgres -c "ALTER USER ubuntu WITH PASSWORD 'password'"
```

7. 创建目标数据库
```
createdb xxx
createdb xxxx
```

# FAQ

1. 需要动用sudo权限
```
关于从/usr/share和/var/run路径下查找文件失败的问题:

这两个路径没有找到通过配置文件修改的办法，其中socket锁定文件在初始化数据库的配置文件中以修改，但后续的创建用户，创建用户和数据库时仍会提示从/var/run目录下查找。

解决办法：使用此仓库中的postgresql可以避免这些问题
```
2. 程序在初始化数据库时，提示SSL is not enabled on the server
```
解决办法：修改程序配置文件中的数据库配置，增加sslmode=disable如下:

export DATABASE_URL="postgres://ubuntu:password@localhost/horizon?sslmode=disable"
```
3. postgresql的常用操作
```
https://www.cnblogs.com/kaituorensheng/p/4667160.html
```

# 附录1 postgresql数据库常用操作

数据库配置文件位置：
```
/etc/postgresql/9.6/main/postgresql.conf
在/opt/stellar/postgresql目录下的是一个备份文件，而不是在使用的。
```
配置文件中指定数据库位置
```
data_directory = '/opt/stellar/postgresql/data'
```
登录数据库
```
sudo -u postgres psql
```
添加用户
```
CREATE USER ubuntu WITH PASSWORD '123456';
```
查看用户
```
\du
```
删除用户
```
DROP ROLE ubuntu;
```
创建数据库
```
CREATE DATABASE core OWNER postgres;
CREATE DATABASE horizon OWNER postgres;
```
删除数据库
```
DROP database core;
```
赋给用户操作数据库的所有权限
```
GRANT ALL PRIVILEGES ON DATABASE core TO postgres;
GRANT ALL PRIVILEGES ON DATABASE core TO ubuntu;

GRANT ALL PRIVILEGES ON DATABASE horizon TO postgres;
GRANT ALL PRIVILEGES ON DATABASE horizon TO ubuntu;
```
更改数据库拥有者
```
alter database core owner to postgres;
alter database core owner to postgres;
```
执行创建表结构的脚本 core.sql
```
命令格式：psql -h localhost -d 数据库名 -U 用户名 -f .sql文件

psql -h localhost -d core -U ubuntu -f postgresql/core.sql
psql -h localhost -d core -U ubuntu -f horizon/horizon.sql
```
查看数据库
```
\l
```

切换数据库
```
\c core
```
查看所有表
```
\d

stellar数据表查询:

            List of relations
 Schema |     Name      | Type  |  Owner
--------+---------------+-------+---------
 public | accountdata   | table | ubuntu
 public | accounts      | table | ubuntu
 public | ban           | table | ubuntu
 public | ledgerheaders | table | ubuntu
 public | offers        | table | ubuntu
 public | peers         | table | ubuntu
 public | publishqueue  | table | ubuntu
 public | pubsub        | table | ubuntu
 public | scphistory    | table | ubuntu
 public | scpquorums    | table | ubuntu
 public | signers       | table | ubuntu
 public | storestate    | table | ubuntu
 public | trustlines    | table | ubuntu
 public | txfeehistory  | table | ubuntu
 public | txhistory     | table | ubuntu
```

查看表结构

```
\d accounts
```
查看表数据
```
select * from accounts;
```
退出数据库
```
\q
```
数据库导出和导入，曾用于从docker环境下导出所有表，并在新数据库中创建所有表
```
导出单个表：
su  stellar; \
pg_dump -s -t accounts core > /tmp/accounts.sql; \

导出数据库所有表：
pg_dump -U stellar -s -f /tmp/core.sql core
```
导入表
```
psql -h localhost -d core -U ubuntu -f postgresql/core.sql
```

# FAQ

### 执行initdb -D /data/psqldb时报错: 

initdb: invalid locale settings; check LANG and LC_* environment variables
```
initdb -D /data/psqld --no-locale --encoding=UTF8

https://stackoverflow.com/questions/50746147/postgresqls-initdb-fails-with-invalid-locale-settings-check-lang-and-lc-e
```

