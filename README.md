# es-docker
### 作用
    ES 官网有docker-composer的手册。但是在实际生产环境可能不适用。
    在多个主机组建一个cluster。本仓库就是为了解决这个问题。

### 如何使用
    
获取镜像

    docker pull docker.elastic.co/elasticsearch/elasticsearch:7.6.13
    
启动容器

    docker run --name es-node -d \
    -p 9200:9200 -p 9300:9300 \ 
    -v /docker/docker-es/config/elasticsearch.yml:/usr/share/elasticsearc/config/elasticsearch.yml \
    -v /docker/docker-es/data:/usr/share/elasticsearch/data \
    -v /docker/docker-es/log:/usr/share/elasticsearch/log \
    docker.elastic.co/elasticsearch/elasticsearch:7.16.3

### 说明
    9200 es 端口 ；9300 es cluster 端口。
    /config ：配置文件目录
    /data：es 存储文件目录
    /log： es 日志存储目录

### /config 说明

config 配置根据环境自行配置。

    # 集群名称
    cluster.name: es
    
    # 节点名称，仅仅是描述名称
    node.name: es-node                                 
    
    # 数据的默认存放路径
    path.data: /usr/share/elasticsearch/data
    
    # 日志的默认存放路径
    path.logs: /usr/share/elasticsearch/log                  
    
    # 当前节点的IP地址 
    network.bind_host: 0.0.0.0  
    # 对外地址
    network.publish_host: 192.168.3.188
    # 端口
    http.port: 9200                                     # 对外提供服务的端口，9300为集群服务的端口

    #culster 中其他节点在与此节点通信时应使用的端口
    transport.tcp.port: 9300
    transport.tcp.compress: true
    
    #首次启动全新的Elasticsearch 集群时，默认选举节点
    cluster.initial_master_nodes: es-node
    
    # 集群个节点IP地址，也可以使用els、els.com等名称，需要各节点能够解析
    discovery.zen.ping.unicast.hosts: ["192.168.3.200", "192.168.3.201","192.168.3.188"]
    
    # 对集群的稳定性至关重要，防止脑裂的出现。
    discovery.zen.minimum_master_nodes: 2
    
### 设置密码
ES在7.0以后免费开放了安全模块 xpack。没错在7.0之前要企业版才有的功能。

elasticsearch.yml配置文件中加入这三个配置：

    http.cors.allow-headers: Authorization
    xpack.security.enabled: true
    xpack.security.transport.ssl.enabled: true

进入容器

    #重启es
    docker restart es

    #重启后进入es容器中
    docker exec -it es /bin/bash

    #进入es 目录
    cd /usr/share/elasticsearch/bin

    #执行命令，交互式设置密码（注意保存好全部密码）
    ./elasticsearch-setup-passwords interactive


