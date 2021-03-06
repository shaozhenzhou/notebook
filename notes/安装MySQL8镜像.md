# 安装MySQL8镜像
## 安装启动MySQL
- 在PowerShell中运行docker命令，拉取mysql最新镜像（TAG:latest）
```
docker pull mysql
```
- 启动mysql
```
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
```
- 查看mysql容器运行状态
```
docker ps -a
```
- 查看mysql容器启动日志
```
docker logs some-mysql
```
- 从命令行连接到容器中的mysql
```
docker run -it --link some-mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```
## MySQL容器中安装vim
- 进入mysql容器
```
docker exec -it some-mysql bash
```
- 更新apt-get索引
```
apt-get update
```
- 安装vim
```
apt-get install vim
```
## 修改mysql字符集配置
- 找到配置文件
```
find / -iname '*.cnf' -print
```
- 修改my.cnf
```
vim /etc/mysql/my.cnf

# 修改以下配置信息
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
character-set-server=utf8
```
- 退出容器，重启容器
```
docker restart some-mysql
```
- 从命令行连接到容器中的mysql
```
docker run -it --link some-mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```
- mysql命令行中查看字符集设置
```
show variables like 'collation_%';
show variables like 'character_set_%';
```
