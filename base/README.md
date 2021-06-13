# 基础镜像说明

为加速镜像构建速度，降部分基础功能单独构建镜像。

## 构建步骤

### 第一步：构建
```
docker build --pull --no-cache ./php7 -t 8565012/php:7.4
# tag格式 账户名/包名:版本
```

### 第二步：推送
```
docker push 8565012/php:7.4
```

## 其它说明

- 登录账号
```
docker login
```

- Error saving credentials: error storing credentials - err: exec: "docker-credential-desktop.exe": executable file not found in $PATH, out: ``

```
vi ~/.bashrc
export PATH=$PATH:"/mnt/c/Program Files/Docker/Docker/resources/bin"
```