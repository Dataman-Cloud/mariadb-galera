# Galera-mariaDB on Shurenyun

## 1 发布 galera-donor

镜像：index.shurenyun.com/erkules/galera

版本：v4

容器个数：1

所在主机地址：选定

应用模式：网桥

Docker 参数：

| KEY        | VALUE           
| ------------- |:-------------
| publish      | 4444:4444
| publish      | 4567:4567
| publish      | 4568:4568

## 2 发布 galera-joiner

镜像：index.shurenyun.com/erkules/galera

版本：v4

容器个数：2(选定一容器一主机)

所在主机地址：选定

应用模式：网桥

应用地址：

	应用端口：3306
	协议：TCP
	映射端口：3307
	
环境变量：

| KEY        | VALUE           
| ------------- |:-------------
| CLUSTER_ADDRESS      | (galera-donor IP)

Docker 参数：

| KEY        | VALUE           
| ------------- |:-------------
| publish      | 4444:4444
| publish      | 4567:4567
| publish      | 4568:4568



## 3 创建 MariaDB 的可访问用户

1. 登陆任意一台部署了 Galera 集群的主机；
2. 通过 ```docker exec``` 进入 galera 容器内；
3. 输入一下命令：

	mysql -e "GRANT ALL PRIVILEGES on *.* to root@\"%\" \
        IDENTIFIED BY '111111' \
        WITH \
        GRANT OPTION MAX_QUERIES_PER_HOUR 0 \
        MAX_CONNECTIONS_PER_HOUR 0 \
        MAX_UPDATES_PER_HOUR 0 \
        MAX_USER_CONNECTIONS 0; \
        flush privileges;";
        
>注1：galera-donor 的数据库地址未暴露，作为 donor 节点，为保证其稳定性，只同步数据，不对外提供服务；
>注2：该镜像文件请见以下 Repo： ```https://github.com/Dataman-Cloud/mariadb-galera```

## 4 Wordpress 测试

镜像：index.shurenyun.com/wordpress

版本：4.4

容器个数：1

所在主机地址：选定

应用模式：网桥

应用地址：

	应用端口：80
	协议：HTTP
	映射端口：8088

环境变量：

| KEY        | VALUE           
| ------------- |:-------------
| WORDPRESS_DB_HOST      | （某个 galera-joiner 节点的 IP）:3307
| WORDPRESS_DB_PASSWORD      | 111111
| WORDPRESS_DB_USER      | root