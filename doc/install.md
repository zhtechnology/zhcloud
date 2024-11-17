本平台平台需要安装MySQL 8.+数据库、Prometheus 0.50+、Alertmanager 0.27+基础环境，以及平台自身Zhcloud、zhcloud-agent客户端。

其中zhcloud-agent负责Prometheus配置文件的更改，采用file_sd_configs模式实现。

## Prometheus安装

### 容器安装

```bash
docker run -d \
        -p 9090:9090 \
        --name prometheus \
        -v /本地路径/data/:/opt/bitnami/prometheus/data/ \
        -v /本地路径/conf/:/opt/zhtec/prom/conf/ \
        bitnami/prometheus:2.54.0 \
        --config.file=/opt/zhtec/prom/conf/prometheus.yml \
        --web.enable-lifecycle
```

注意：1）/本地路径/conf/:/opt/zhtec/prom/conf/这个映射路径要和agent的写入路径保持一致，建议不要修改。
2）[prometheus.yml](../prometheus/prometheus.yml)文件配置信息。

## Alertmanager安装

### 容器安装

```bash
docker run -d --name alertmanager \
        -p 9093:9093 \
        -v /本地路径/config.yml:/opt/bitnami/alertmanager/conf/config.yml \
        bitnami/alertmanager:0.23.0
```

注意：1）config.yml需要修改webhook地址为zhcloud平台地址。
2）[alertmanager.yml](../prometheus/config.yml)文件配置信息。

## MySQL安装

### 容器安装

```bash
docker run -d --name mysql-server8 \
    -p 3306:3306 \
    -e MYSQL_ROOT_PASSWORD=Zhtec@12345 \
    -v /本地路径/my.cnf:/opt/bitnami/mysql/conf/my.cnf \
    -v /本地路径/mysql/data/:/bitnami/mysql/data/ \
    mysql/mysql-server:8.0
```
## Zhcloud平台安装

### 容器安装

```bash
docker run -d \
    --name=zhcloud \
    -p 8000:80 \
    registry.cn-hangzhou.aliyuncs.com/zhtec/zhcloud:v0.2.9 \
    --zhcloud.agentUrl="http://agent的ip和端口" \
    --zhcloud.promUrl="http://Prometheus的ip和端口" \
    --spring.datasource.druid.master.url="jdbc:mysql://数据库ip和端口/zhcloud?useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=true&serverTimezone=GMT%2B8" \
    --spring.datasource.druid.master.username="数据库用户名" \
    --spring.datasource.druid.master.password="数据库密码"
```
注意：[版本更新见](release.md)
## Zhcloud-agent安装

### 容器安装

```bash
docker run -d \
    --name=zhcloud-agent \
    -p 8100:8100 \
    -v /Prometheus配置路径/conf:/opt/zhtec/prom/conf/ \
    registry.cn-hangzhou.aliyuncs.com/zhtec/zhcloud-agent:v1.2.0
```
注意：1）/本地路径/conf/:/opt/zhtec/prom/conf/这个映射路径要和Prometheus的写入路径保持一致，建议不要修改。
2）[版本更新见](agent-release.md)