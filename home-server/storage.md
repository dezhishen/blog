# 存储

## 外部存储

### webdav
#### 常用方式

##### 挂载到本地
* linux
    * 使用`davfs2`挂载为本地磁盘
* windows
    * 文件资源管理器->此电脑->映射网络驱动器(*ps:非https需要修改注册表*)
    * 使用[raidrive](https://www.raidrive.com/)
##### 其他应用
* 部分应用支持webdav存储和备份

#### 外部存储

名称|描述
-|-
aliyun-driver|使用阿里云构建webdav,地址[webdav-aliyundriver](https://github.com/zxbu/webdav-aliyundriver)

##### aliyun-webdav

```
docker run -d --name=webdav-aliyundriver --restart=always -p 8080:8080  -v /etc/localtime:/etc/localtime -v /docker_data/aliyun-driver/:/etc/aliyun-driver/ -e TZ="Asia/Shanghai" -e ALIYUNDRIVE_REFRESH_TOKEN="your refreshToken" -e ALIYUNDRIVE_AUTH_PASSWORD="admin" -e JAVA_OPTS="-Xmx1g" zx5253/webdav-aliyundriver

# /etc/aliyun-driver/ 挂载卷自动维护了最新的refreshToken，建议挂载
# ALIYUNDRIVE_AUTH_PASSWORD 是admin账户的密码，建议修改
# JAVA_OPTS 可修改最大内存占用，比如 -e JAVA_OPTS="-Xmx512m" 表示最大内存限制为512m
```

```
--aliyundrive.refresh-token
    阿里云盘的refreshToken，获取方式见下文
--server.port
    非必填，服务器端口号，默认为8080
--aliyundrive.auth.enable=true
    是否开启WebDav账户验证，默认开启
--aliyundrive.auth.user-name=admin
    WebDav账户，默认admin
--aliyundrive.auth.password=admin
    WebDav密码，默认admin
--aliyundrive.work-dir=/etc/aliyun-driver/
    token挂载路径（如果多开的话，需修改此配置）
```