## maven

 Maven的classifier作用
classifier可以是任意的字符串，用于确定文件。常见的应用场景如下所示：
（1）可用于区分不同JDK版本所生成的jar包
（2）区分项目的不同组成部分，例如，源代码、javadoc、类文件等。



@ComponentScan 注解的作用是扫描 @SpringBootApplication 所在的 Application 类所在的包在 Spring Boot 项目中，如果你想要被 Spring 容器管理的 bean 不在 Spring Boot 包扫描路径下，怎么办？解决 Spring Boot 中不能被默认路径扫描的配置类的方式，有 2 种：（1）在 Spring Boot 主类上使用 @Import 注解（2）使用 spring.factories 文件

## Docker-compose 安装

```
# 下载
➜  ~ sudo curl -L https://github.com/docker/compose/releases/download/1.24.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

# 修改权限
➜  ~ sudo chmod +x /usr/local/bin/docker-compose

# 查看
➜  ~ docker-compose -v
docker-compose version 1.24.0, build 0aa59064
```

docker

```
version: '3'
services:
  xf-clue-gateway:
    image: java
    restart: "always" #重启策略，能够使服务保持始终运行，生产环境推荐使用
    container_name: ${env}-xf-clue-gateway #容器名称
    logging:
      driver: "json-file"
      options:
        max-size: "500M"
        max-file: "3"
    volumes:
      - /opt/opt2/xf/clue/xf-clue-gateway.jar:/usr/xf-clue-gateway.jar
      - /opt/opt2/iflytek/znqc/config/temp:/iflytek/znqc/config/temp
      - /opt/opt2/iflytek/znqc/logs:/logs
    network_mode: "host"
    ports:
      - 9900:9900
    entrypoint: java -jar /usr/xf-clue-gateway.jar --spring.profiles.active=${env}
    environment:
      - TZ=Asia/Shanghai
```

### Elasticsearch7.14.0部署手册

#### 一、准备工作

1、`/etc/security/limits.conf`

```sh
elasticsearch  -  nofile  65535
elasticsearch  -  nproc  4096
```

2、`/etc/sysctl.conf`

``` sh
vm.max_map_count=262144
```

#### 二、安装和启动

##### 1、解压缩软件包

```sh
cd /data/iflytek/
tar -xzf elasticsearch-7.14.0-linux-x86_64.tar.gz 
```

##### 2、修改elasticsearch配置：

```sh
cd elasticsearch-7.14.0/ 
vi config/elasticsearch.yml
```

>  配置内容：

``` yaml
cluster:
    name: fylx-prod-es-cluster
    initial_master_nodes:
        - fylx-prod-data-node-1
node:
    name: fylx-prod-data-node-1
path:
    data: /data/iflytek/elasticsearch-7.14.0/data
    logs: /data/iflytek/elasticsearch-7.14.0/logs
bootstrap:
    memory_lock: true
#服务器IP
network:
    host: 192.168.0.1
http:
    port: 9200
    cors:
        enabled: true 
        allow-origin: "*"    
```

##### 3、启动用户授权：

```sh
chown elasticsearch $PWD -R
```

##### 4、启动：

```sh
su elasticsearch
./bin/elasticsearch -d -p pid
```

##### 5、验证：

```sh
curl -X GET http://localhost:9200/
```

> 启动成功返回结果：

```json
{
  "name" : "fylx-prod-data-node-1",
  "cluster_name" : "fylx-prod-es-cluster",
  "cluster_uuid" : "ISgwVohRT2uL7dYqWHhPZQ",
  "version" : {
    "number" : "7.14.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "dd5a0a2acaa2045ff9624f3729fc8a6f40835aa1",
    "build_date" : "2021-07-29T20:49:32.864135063Z",
    "build_snapshot" : false,
    "lucene_version" : "8.9.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

##### 6、停止：

``` sh
pkill -F pid

```