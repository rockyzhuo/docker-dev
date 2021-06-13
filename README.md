# Centos下安装说明

https://docs.docker.com/engine/install/centos/
https://developer.aliyun.com/mirror/docker-ce

- 移除先前版本
```
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
- 安装必要工具

```
yum install -y yum-utils
```

- 添加软件源

```
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

- 安装
```
yum install docker-ce docker-ce-cli containerd.io

curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

- 启动及开机启动
```
systemctl start docker
systemctl enable docker
```

# 使用说明
根据.env.example 新建.env配置。然后执行以下命令启动所有容器。
```
docker-compose up
```

- 配置阿里云CDN

在系统右下角托盘图标内右键菜单选择 Settings，打开配置窗口后左侧导航菜单选择 Docker Daemon。编辑窗口内的JSON串，填写下方加速器地址：

```
{
    "registry-mirrors": ["https://qtdfzqk5.mirror.aliyuncs.com"]
}
```

- 启动容器

https://docs.docker.com/compose/reference/up/

```
docker-compose up // 所有容器
docker-compose up 名称 // 指定容器
```
- 启动时重建

```
docker-compose up -d --build --no-deps php
```

- 重启容器

```
docker-compose restart // 所有容器
docker-compose restart 名称 // 指定容器
```

- 停止容器

```
docker-compose stop // 所有容器
docker-compose stop 名称 // 指定容器
```

名称见 docker-compose.yml 文件中对应服务的container_name。

- 查看容器进程
```
docker-compose ps // 运行中
docker ps // 运行中
docker ps -a  // 查看所有容器，包括停止的
```

- 查看容器内进程
```
docker top 名称
```

- 重建容器
```
docker-compose build // 所有
docker-compose build php // 指定
docker-compose build --no-cache --pull php // 始终拉取镜像，用于更新
```

- 更新版本，针对有dockerfile的
```
docker-compose build --no-cache --pull php // 始终拉取镜像，用于更新
```

- 针对没有dockerfile的
```
docker-compose pull nginx
docker-compose up -d nginx
```

- 进入容器内部
```
docker exec -it 名称 bash
```

- 不进入容器执行容器内程序命令

```
docker exec 名称 命令
// 如，更新nginx配置
docker exec nginx nginx -s reload
```

# 常见问题

- 用户权限问题

如果没有nginx、php默认的用户组，则新增

```
# 添加用户组指定id
groupadd -g 33 www-data
# 添加用户指定id
useradd -u 33 -g www-data -d /srv/www -s /usr/sbin/nologin www-data

# 删除
groupdel www-data
userdel www-data
```

宿主机定时任务、队列 如果通过docker exec执行，则用户通过 Dockerfile USER，或docker exec -u www-data 指定。

- 非root用户无法执行 docker 命令

Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock

```
# 将执行用户加入docker用户组
usermod -a -G docker www-data
```

- 执行定时任务

注意，容器中执行命令的用户 与crontab用户无关

```
* * * * * docker exec -u www-data php sh -c 'cd /srv/www/shanjiao && php artisan schedule:run >> /dev/null 2>&1'
```

- 配置文件问题

为了使配置文件引入版本 同时不同环境又可以差异处理，推荐线上环境使用 符号链接 项目中的配置文件。

```
ln -sb /srv/www/shanjiao/docker/nginx/conf.d/* /srv/docker/nginx/conf.d/
ln -sb /srv/www/shanjiao/docker/nginx/conf.d/shanjiao.conf /srv/docker/nginx/conf.d/
```