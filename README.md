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
pg_ctl initdb -D /data/psqldb
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

