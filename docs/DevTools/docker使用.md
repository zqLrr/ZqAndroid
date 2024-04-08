# docker使用

```bash
docker version #查看docker信息
docker info
docker images #查看
docker ps #查看容器进程
```

### 容器内执行mysql：

>  docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=root -d ibex/debian-mysql-server-5.7 --bind-address=0.0.0.0

> docker exec -it mysql(容器名称) bash

### 在本地通过Mysql 连接Docker的Mysql

1.查询宿主机容器的ip地址

docker inspect [容器名称]|grep IPAddress



# Pecker 工程

## 环境配置：

1.连接遇到连接不到mysql 修改root的host='%'

```shell
UPDATE mysql.user SET host='%' WHERE user='root';
FLUSH PRIVILEGES;
```

2.数据库的数据是code First,但是必须建立数据库

```shell
CREATE DATABASE front;
```

