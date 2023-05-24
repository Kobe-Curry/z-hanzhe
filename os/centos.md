## 防火墙常用命令

防火墙启动命令

```shell
# 启用防火墙
$ systemctl start firewalld

# 禁用防火墙
$ systemctl stop firewalld

# 查看防火墙运行状态
$ systemctl status firewalld

# 配置防火墙开机自启
$ systemctl enable firewalld.service

# 关闭防火墙开机自启
$ systemctl disable firewalld.service
```

防火墙常用操作

```shell
# 开放22端口
$ firewall-cmd --zone=public --add-port=22/tcp --permanent

# 关闭22端口的开放状态
$ firewall-cmd --zone=public --remove-port=22/tcp --permanent

# 查看防火墙开放的端口列表
$ firewall-cmd --permanent --list-port
```