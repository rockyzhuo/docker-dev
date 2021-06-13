# WSL2（Debian）说明

## 安装

https://docs.microsoft.com/en-us/windows/wsl/install-win10

https://docs.docker.com/docker-for-windows/wsl/

## 设置

### 设置root密码
```
passwd root
```
### 设置默认用户
```
debian config --default-user root
```

## 常见错误

### docker

docker日志在/var/log/docker.log

- 启动时（service docker start）提示：grep: /etc/fstab: No such file or directory

```
touch /etc/fstab
```

- docker-compose build php 报错 docker.credentials.errors.InitializationError: docker-credential-desktop.exe not installed or not available in PATH

```
rm ~/.docker/config.json
```

- 启动没提示，日志中报错

Error initializing network controller: error obtaining controller instance: failed to create NAT chain DOCKER: iptables failed: iptables --wait -t nat -N DOCKER: iptables v1.8.2 (nf_tables):  CHAIN_ADD failed (No such file or directory): chain PREROUTING

故障原因是Docker用iptables初始化NAT网络，而Debian buster使用 nftables 而不是 iptables，导致dockerd不能正常完成NAT初始化，出错退出。
```
update-alternatives --set iptables /usr/sbin/iptables-legacy
# for ipv6
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```