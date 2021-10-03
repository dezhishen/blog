# AdGuardHome

## 项目地址

[https://github.com/AdguardTeam/AdGuardHome](https://github.com/AdguardTeam/AdGuardHome)

## 简介

```
  AdGuard Home is a network-wide software for blocking ads & tracking.
After you set it up, it'll cover ALL your home devices,
and you don't need any client-side software for that.
  It operates as a DNS server that re-routes tracking domains to a "black hole", 
thus preventing your devices from connecting to those servers.
It's based on software we use for our public AdGuard DNS servers
-- both share a lot of common code.

  AdGuard Home 是一款全网络软件，用于阻止广告和跟踪
设置后，它将涵盖您所有的家庭设备，您不需要任何客户端软件。
  它作为 DNS 服务器运行，将跟踪域重新路由到“黑洞”，从而防止您的设备连接到这些服务器
它基于我们用于公共 AdGuard DNS 服务器的软件——两者共享许多通用代码。
```

## 安装

推荐：使用`docker`安装

### 创建跨主机网络
- 目的
    - 减少端口占用以及主机端口冲突的问题
    - 使用更优雅

- 命令行

    ```
    docker network create -d macvlan \
        --subnet=192.168.31.0/24 \
        --gateway=192.168.31.1 \
        -o parent=eth0 macnet
    ```

- 参数说明
 
    ```
    -d 指定 Docker 网络 driver
    --subnet 指定 macvlan 网络所在的网络
    --gateway 指定网关
    -o parent 指定用来分配 macvlan 网络的物理网卡
    最后的macnet,是网络的名称
    ```



### 启动
- 命令行

    ```
    docker run -d \
        --restart=always \
        --name adguardhome \
        -v /storage/docker_data/adguardhome/data:/opt/adguardhome/work \
        -v /storage/docker_data/adguardhome/conf:/opt/adguardhome/conf \
        --network macnet \
        --ip 192.168.31.103 \
        adguard/adguardhome
    ```

- 参数说明

    ```
    -v /storage/docker_data/adguardhome/data:/opt/adguardhome/work 数据卷映射
    -v /storage/docker_data/adguardhome/conf:/opt/adguardhome/conf 配置卷映射
    --network macnet 指定网络为之前创建的网络
    --ip 192.168.31.103 指定容器ip,方便访问
    ```

### 访问

访问: `http://192.168.31.103:3000`


## 设置
### dns设置 
#### 上游dns
```
https://dns.alidns.com/dns-query
```
### 过滤器
全局去除广告
#### DNS封锁清单
添加阻止列表->添加一个自定义列表
##### 推荐清单
- https://adguardteam.github.io/AdGuardSDNSFilter/Filters/filter.txt
- https://adaway.org/hosts.txt
- https://www.malwaredomainlist.com/hostslist/hosts.txt
- https://filters.adtidy.org/extension/chromium/filters/11.txt
- https://easylist.to/easylist/easylist.txt


